# Feasibility per AID

Feasibility per AID ger samma information som Feasibility Batch-interface, men enbart för ett AID. Det används typiskt för att be om aktuell information på en access, eller för att hämta Option82 efter en aktivering. 

Fältbeskrivningarna är identiska med Bulk-APIt.

### Exempel

```json
Request:
    GET /api/2.1/accesses/STTA0001 HTTP/1.1

Response:
    HTTP/1.1 200 OK
    Content-Type: application/json
{
    "accessId": "STTA0001", 
    "streetName": "Testvägen", 
    "streetNumber": "100", 
    "streetLittera": "",
    "postalCode": "10000",
    "city": "Ankeborg",
    "countryCode": "SE",
    "premisesType": "MDU_APARTMENT",
    "mduApartmentNumber": "1001", 
    "mduDistinguisher": "12121212", 
    "population": "Hemsöhem",
    "services": [
        {
            "service": "BB-100-100", 
            "connection": "2014-03-01",
            "available": "2014-01-01",
            "serviceType": "BROADBAND",
            "forcedTakeoverPossible": false
        }, {
            "service": "BB-100-10", 
            "connection": "2013-10-12",
            "available": "YES",
            "serviceType": "BROADBAND",
            "forcedTakeoverPossible": false
        }, {
            "service": "BB-10-10", 
            "connection": "2013-10-12",
            "available": "YES",
            "serviceType": "BROADBAND",
            "forcedTakeoverPossible": false                           
        }, {
            "service": "IPTV",
            "connection": "2013-10-12",
            "available": "YES",
            "serviceType": "TV",
            "forcedTakeoverPossible": false
        }, {
            "service": "VOIP", 
            "connection": "2013-08-13", 
            "available": "NO",
            "serviceType": "TELE",
            "forcedTakeoverPossible": true
        }
    ],
    "active": [
        {
            "service": "BB-100-10",
            "option82": "5216010765746820302F31020B31302E31302E31302E3130",
            "equipment": [
                { "vendorId" : "CH_BROADBAND" }
            ]
        }, {
            "service": "IPTV",
            "option82": "5216010765746820302F32020B31302E31302E31302E3130",
            "equipment": []
        }
    ],
    "coFiberConverter": "LASER_3001X_MK2",
    "coCpeSwitch": "",
    "coCpeRouter": "NETGEAR WNDR4000"
}
```
