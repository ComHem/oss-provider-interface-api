---
out:provider_interface_2.2_option82_to_accessid.html
---
# Option82 Lookup API

För att stöda utrustningsbaserad autoaktivering behöver Com Hem kunna slå upp vilken Access ett Option82-värde avser.

På olika sätt får Com Hem in DHCP-loggar med MAC-adress och Option82 (port). På så vis kan Com Hem avgöra vilken port en kund är inkopplad i. För att kunna fortsätta aktiveringsprocessen och beställa tjänster på rätt port (via Access) behöver Com Hem kunna avgöra vilket AccessId som hör till en given port.

Beroende på hur stadsnätet förmedlar loggar till Com Hem kommer Com Hem att skicka anrop med olika suboptions.

Om DHCP-loggen enbart innehåller RemoteId, kommer anropet enbart innehålla RemoteId, och vice versa.

## Exempel: Uppslagning av Option82 som hittas.

```json
Request:
    GET /api/2.2/option82/5216010765746820302F31020B31302E31302E31302E3130

Response:
    HTTP/1.1 200 OK
    Content-Type: application/json
{
    "accessId": "SMBA0002"
}
```

Option82 är case-insensitive.

## Exempel: Uppslagning av Option82 som inte hittas.

```json
Request:
    GET /api/2.2/option82/5216010765746820302F31020B31302E31302E31302E3130

Response:
    HTTP/1.1 404 Not Found
```

