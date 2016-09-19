# TT - Trouble Tickets

Trouble tickets används för att länka ärendehantering mellan operatörer på ett generellt och flexibelt sätt. För att kunna anpassa till speciella behov vid ärendeskapande som en kommunikationsoperatör har används mallar där obligatoriska/frivilliga fält i ett ärende kan specificeras. Grundtanken är att kommunikationsoperatören har ett eget system för ärendehantering där ett arbetsätt med flöden och statusar finns etablerat. Hela denna processen vill man inte behöva förklara/exponera till sina kunder (tjänsteleverantörer). Detta API tar fasta på ett antal statusar och informationsutbyten som kan på ett enkelt och tillräckligt sätt representera det informationsutbyte som är önskat mellan kommunikationsoperatör och tjänsteleverantör.

En tjänsteleverantör kan skapa ett ärende, uppdatera ett ärende, stänga (acceptera lösning på) ett ärende, återöppna (refusera lösning på) ett ärende.
En kommunikationsoperatör kan uppdatera ett ärende, markera ärende som löst tillsammans med information om lösningen.

Det är kommunikationsoperatören som står för mottagarsidan av APIet, dvs. som driftar APIet på sina servrar.

APIet består av 5 st metoder

- [getTemplate](#gettemplate)
  Specificerar kommunikationsoperatörsspecifika extraparametrar för ärenden.
- [getTickets](#gettickets)
  Hämtar uppdateringar av alla ärenden.
- [getTicket](#getticket)
  Hämtar aktuell status på ett ärende.
- [createTicket](#createticket)
  Skapa ett nytt ärende.
- [updateTicket](#updateticket)
  Uppdatera ett ärende.

## getTemplate

Används för att specificera eventuella extra parametrar som skall skickas med vid skapande av nya ärenden.
Man kan ange olika parametrar som behövs beroende på vilken typ av ärende och anslutning det gäller. Generellt så delas problemen in i tre olika grupper:
- Totalt fel på tjänsten, den fungerar inte. 
- Kvalitetsproblem, tjänsten fungerar inte tillfredställande.
- Annat problem, kan tex. utnyttjas för om man vill specialbehandla tex. hastighet i bredband och inte lägga det under kvalitet utan under en egen rubrik.

Det är upp till kommunikationsoperatören att välja hur generella eller specifika dessa parametrar behöver vara. Det kan vara så att man har samma krav/frågor oavsett tjänst, feltyp, eller access. Eller så beror frågorna man vill ha svar på vilken mediaomvandlare eller liknande som just den anslutningen har.  

Request:
```
GET /api/getTemplate?aid=AID&service=SERVICE&type=TYPE
```

Fält    | Förklaring
--------|-----------
`aid`     | access för vilken ett ärende ska skapas. *string* 
`service` | Namn på den service som ärendet gäller. *string __optional__*
`type`    | Typ av fel. *enum (`TOTAL`, `QUALITY`, `OTHER`) __optional__*

Response:  
```
HTTP/1.1 200 OK
Content-type: application/json

{ "templates": [ 
                 { "service": "SERVICENAME",
                   "type": "ISSUETYPE",
                   "fields": [
                               { "field": "FIELD1",
                                 "description": "some short text",
                                 "type": "option",
                                 "values": ["VALUE1", "VALUE2", "VALUE3"],
                                 "mandatory": false
                               }
                             ]
                 }
               ]
}
```

Fält    | Förklaring
--------|-----------
`templates` | Container för mallar som finns att välja på. *array of object*
`templates.service` | Namn på den service som mallen kan användas för. *string*
`templates.type` | Typ av fel som mallen kan användas för. *enum (`TOTAL`, `QUALITY`, `OTHER`)*
`templates.fields`  | Container för alla fält som kan/ska ingå vid skapande av ärende. *array of object*
`fields.field` | Namn på fält. *string [a-z0-9_]* 
`fields.description` | Kort beskrivning av fältet. För att läsas av människor i ev. UI. *string*
`fields.type` | Typ av värde. *enum (`BOOL`, `STRING`, `NUMBER`, `OPTION`, `MULTOPTION`)*.
`fields.values` | Används om `type` är `OPTION` eller `MULTOPTION`. Vid `MULTOPTION` ska valda options skickas som en *array of string* när [createTicket](#createticket) anropas. *array of string __optional__*
`fields.mandatory` | Är fältet obligatoriskt. *bool*

## getTickets

Hämta ärenden som uppdaterats sedan föregående hämtning. Full information om enskilda ärenden kan sedan hämtas per ärende vid behov.

Request:  
```
GET /api/getTickets?eventId=EVENTID
```
Fält    | Förklaring
--------|-----------
`eventId` | Hämta alla tickets som uppdaterats sedan eventID. Kan vara tex. ett UUID, timestamp eller liknande. Det enda viktiga är att värdet unikt identifierar vilka ärenden som kom in före resp. efter detta värde. Användare av APIet (tjänsteleverantörer) får inte förutsätta att värdet kan användas till något annat än att just hålla reda på vilka ärenden som hämtats. Returneras för varje ärende i svaret. *string*

Response:
```
HTTP/1.1 200 OK
Content-type: application/json

[
  {
    "eventId": "b983f17f-47e7-11e4-9a97-d205081fb0a1",
    "ticketRef": "TicketRef123456",
    "spReference" : "MinRef123456789",
    "timestamp": 1413181130416,
    "comment": "Kundens problem löst",
    "state":
      { "assignedTo": "CO",
        "status": "CLOSED",
        "action": "RECONFIGURATION_OR_DATA_FIX",
        "code": "PROBLEM_SOLVED"
      }
  }
]

``` 

Fält    | Förklaring
--------|-----------
`eventId` | Id som identifierar denna uppdatering *string*
`ticketRef` | Kommunikationsoperatörens ticketreferens. *string* 
`spReference` | Tjänsteleverantörents ticketreferens. Angavs när ticketen skapades. *string __optional__*
`timestamp` | Tid när senaste uppdateringen skedde. *unix timestamp*
`comment` | Beskrivning. *string*
`state` | Information om ärendets status *object*
`state.assignedTo` | Vem som förväntas agera på ärendet.<br /> *enum <br />`CO` KOmmunikationsoperatör<br />`SP` Tjänsteleverantör*
`state.status` | Anger vad för status ticketen har. <br />*enum* <br />`NEW` Skapad men hantering inte påbörjad<br />`OPEN` Felsökning pågår.<br />`CLEARED` Acceptläge – Åtgärdad enligt kommunikationoperatör.<br />`CLOSED` Stängd
`state.action` | Anledning till att ticket stängdes. Anges när status ändras till `CLEARED` eller `CLOSED`.  <br />*enum `FAULT_CLEARED_BY_ITSELF`, `NO_FAULT_FOUND`, `HARDWARE_REPLACE_OR_REPAIR`, `SOFTWARE_RELEASE_OR_PATCH`, `RECONFIGURATION_OR_DATA_FIX`, `ENVIRONMENTAL`, `FORCE_MAJEURE`, `THIRD_PARTY`, `RESET`, `CR_ISSUED`, `WRONG_ASSIGNMENT`, `OTHER` __optional__*
`state.code` | Anger stängningsstatus. Anges när status ändras till `CLEARED` eller `CLOSED`.  <br />*enum `PROBLEM_SOLVED`, `PROBLEM_NOT_SOLVED`, `PROBLEM_SOLVED_WITH_INTERIM_SOLUTION`, `CANCELLED`, `REJECTED`, `OTHER` __optional__*


## getTicket

Hämta information och aktuell status för ett ärende. Förutom nuvarande status finns även en log över tidigare statusar och all historisk information som utbytts.

Request:  
```
GET /api/ticket/TicketRef123456
```

Response:  
```
{
  "priority": "NON_SERVICE_AFFECTING",
  "title": "Nytt problem",
  "description": "Ett till problem som har rapporterats",
  "ticketRef": "TicketRef123456",
  "spReference" : "MinRef123456789",
  "accessId": "Test0002",
  "customerInformation":
    { "firstName": "Bosse",
      "lastName": "Larsdotter",
      "email": "",
      "phone": "" },
  "state":
    { "status": "NEW",
      "assingee": "CO" },
  "services": [ "BB 100/100" ],
  log:
    [ { "eventId": "b9ab9183-47e7-11e4-9a97-d205081fb0a1",
        "ticketRef": "TicketRef123456",
        "spReference" : "MinRef123456789",
        "timestamp": 1369921910,
        "comment": "Ny ticket!",
        "accessId": "Test0002",
        "state":
          { "status": "NEW",
            "assignedTo": "CO" 
          }
      }
    ]
}
```

Fält    | Förklaring
--------|-----------
`priority` | Prioritet. Denna prioritet kan vara den som angavs när ticketen skapades, eller uppdaterad av kommunikationsoperatören efter granskning. *enum (`CRITICAL`, `MAJOR`, `MEDIUM`, `MINOR`, `NON_SERVICE_AFFECTING`, `INDETERMINATE`)*
`title` | Titel på ärendet. *string*
`description` | Beskrivning av ärendet *string*
`ticketRef` | Kommunikationsoperatörens ticket referens *string*
`spReference` | Tjänsteleverantörents ticketreferens. Angavs när ticketen skapades. *string __optional__*
`accessId` | Access som ticketen gäller. *string*
`customerInformation` | Container för kundinformation *object*
`customerInformation.firstName` | Förnamn. *string*
`customerInformation.lastName` | Efternamn. *string*
`customerInformation.email` | Email. *string*
`customerInformation.phone` | Telefonnummer. *string*
`state` | Container för status på ärendet. *object*
`state.status` | Anger aktuell status på ärendet. <br />*enum* <br />`NEW` Skapad men hantering inte påbörjad<br />`OPEN` Felsökning pågår.<br />`CLEARED` Acceptläge – Åtgärdad enligt kommunikationsoperatör<br />`CLOSED` Stängd
`state.assignedTo` | Vem som förväntas agera på ärendet.<br /> *enum <br />`CO` KOmmunikationsoperatör<br />`SP` Tjänsteleverantör*
`services` | Tjänster som ärendet gäller. *array of string*
`log` | Container för historik. Se [getTickets](#gettickets) för förklaring till fält. *array of object*


## createTicket


Skapa ett nytt ärende. När man skapar ett nytt ärende anges förutom den obligatoriska informationen för alla ärenden så som accessid, kundinformation och beskrivning, även den extrainformation som kommunikationsoperatören kräver för den tjänst ärendet gäller (enl. template beskrivet ovan). Dessutom kan tjänsteleverantören ange en prioritet som en indikation till kommunikationsoperatören. Hur kommunikationsoperatören väljer att hantera denna prioritet i sitt eget system är upp till kommunikationsoperatören själv. Det är inte säkert eller nödvändigt att samma prioriteter finns i kommunikationsoperatörens egna ärendesystem, eller ens att de väljer att låta en tjänsteleverantörs uppfattning av prioritet slå igenom direkt i det interna ärendesystemet.

Request:  
```
POST /api/ticket
Content-type: application/json

{
  "title": "TEST TICKET",
  "description": "Beskrivning av felet",
  "priority": "NON_SERVICE_AFFECTING",
  "accessId": "DONA0001",
  "spReference": "MinRef123456",
  "customerInformation": {
    "firstName": "Sven",
    "lastName": "Svensson",
    "email": "sven@sven.se",
    "phone": "070-010102"
  },
  "services":
  [
    {
      "service": "BB 100/100",
      "problem": "BANDWIDTH_RELATED",
      "extraInfo": {
        "otherEquipmentConnected": true,
        "computerConnectedWithCable": true,
        "testedWithTwoComputers": false,
        "timingBBSpeed":"48/7",
        "timingBBDateTime":"2014-01-01 12:00:00",
        "customerMac": "aa:bb:cc:dd:ee:ff"
    }
  ]
}
```

Fält    | Förklaring
--------|-----------
`title` | Rubrik som beskriver ärendet *string*
`description` | Beskrivning av problemet *string*
`priority` | Prioritet. Denna prioritet anges som ett stöd för operatören att prioritera. *enum (`CRITICAL`, `MAJOR`, `MEDIUM`, `MINOR`, `NON_SERVICE_AFFECTING`, `INDETERMINATE`)*
`accessId` | Access som ärendet gäller. *string*
`spReference` | Tjänsteleverantörents ticketreferens. *string __optional__*
`customerInformation` | Container för kundinformation *object __optional__*
`customerInformation.firstName` | Förnamn. *string*
`customerInformation.lastName` | Efternamn. *string*
`customerInformation.email` | Email. *string*
`customerInformation.phone` | Telefonnummer. *string*
`services` | Container för tjänster som problemet berör. *array of object*
`services.service` | Namn på tjänst som påverkas. *string*
`services.problem` | Typ av problem. *enum (`TOTAL`, `QUALITY`, `OTHER`) __optional__*
`services.extraInfo` | Container för de fält som hämtats via [getTemplate](#gettemplate). *object*


Response:  
```
HTTP/1.1 200 OK
Content-Type: application/json

{
  "ticketRef": "TT100002"
}
```

## updateTicket

Uppdatera ett ärende. Används när det tillkommer information som tjänsteleverantören vill vidareförmedla till kommunikationsoperatören. Eller när kommunikationsoperatören uppdaterat ett ärende och tjänsteleverantören vill svara, antingen genom att acceptera en lösning av ärendet genom att stänga det, svara på en fråga eller att återöppna ett ärende om lösningen inte accepteras.

Request:  
```
POST /api/ticket/TT00002
Content-type: application/json

{
  "description": "testing update",
  "priority": "MINOR",
  "action": "NOTIFY",
}
```

Fält    | Förklaring
--------|-----------
`description` | Uppdatering av information. *string*
`priority` | Prioritet. Denna prioritet anges av tjänsteleverantören som ett stöd för kommunikationsoperatören att prioritera. *enum (`CRITICAL`, `MAJOR`, `MEDIUM`, `MINOR`, `NON_SERVICE_AFFECTING`, `INDETERMINATE`)*
`action` | Används för att ange syfte med uppdateringen.<br />*enum*<br /> `NOTIFY` Notifiera den andra parten om ny information i ärendet. <br /> `REOPEN` Används för att återöppna ett ärende som kommunikationsoperatören lagt i statusen `CLEARED`. <br /> `CLOSE` Används för att stänga ett ärende.


Response:  
```
HTTP/1.1 200 OK
```
