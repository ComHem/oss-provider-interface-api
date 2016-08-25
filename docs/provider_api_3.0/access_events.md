# Access Events API

För att TL skall kunna följa uppdateringar och förändringar av accesser (och beställningar på accesser) har "Access Events API" tagits fram. Efter förändring av Access-resursen i <a href="access.md">Access & Activation API</a>) skall det skapas ett nytt event i Access Events API.

Access Events API är byggt för att vara enkelt att prenumerera på och hämta alla events som inträffat sen sist. På så vis undviker KO och TL att behöva stödja pollning på ordrar, order-status eller accesser.

## Exempel: Hämtning av nya events

Request:
```http
GET /api/3.0/events/?since=cc537e54-e59c-11e3-a593-3c970e806452 HTTP/1.1
```

Response:
```http
HTTP/1.1 200 OK
Content-Type: application/json
{
    "event": "2a6d6432-e59d-11e3-9a52-3c970e806452",
    "access": "STTA0001"
},
{
    "event": "251825a8-e59d-11e3-bf71-3c970e806452",
    "access": "STTA0001"
}
```

Anropet skall returnera samtliga events som skett sedan Eventet `since`.
Event som `since` avser skall inte ingå i svaret.

Listan är oföränderlig. Vid två tidpunkter, t1 och t2, där t2 inträffar efter t1, skall tjänstens svar vid t2 alltid omfatta hela t1. Svaret kan alltså enbart växa, inte förändras.

Av denna anledningen är det viktigt att tjänstens svar inte är en projektion på ordrar, som kan ändra status, utan är baserad på oföränderliga events.

`event` skall vara unikt över alla events tjänsten har returnerat.
Det måste inte vara ett UUID, utan KO kan bestämma innehållet så länge det är unikt.

Listan över events skall vara stigande i kronologisk ordning (senaste eventet sist). Tjänsteleverantören skall att använda det sista lästa eventet som `since`-parameter i nästföljande anrop.

Svaret skickas som Concatenated JSON. "Concatenated JSON" omfattar "Line delimited JSON".

## Exempel: Hämtning av alla events

Request:
```http
GET /api/3.0/orderevents/ HTTP/1.1
```

Response:
```http
HTTP/1.1 200 OK
Content-Type: application/json
{
    "event": "251825a8-e59d-11e3-bf71-3c970e806452",
    "access": "STTA0001"
}
...
```

Om ingen _since_ parameter skickas med i anropet skall _samtliga_ events returneras.
