# Access & Activation API

Access & Activation API erbjuder möjligheten att i realtid hämta följande information:
* Vilka tekniska tjänster som är Tillgängliga för TL att beställa.
* Vilka tekniska tjänster TL har beställt (önskat Access-State).
* Vilka tekniska tjänster som för närvarande är aktiva på accessen (faktiskt Access-State).
* Förklaring till varför det är skillnader mellan Önskat och Faktiskt Access-State.
* Uppslag mellan Port-representationerna (AccessID och SwitchPort/O82).

### Exempel

Request:
```http
GET /api/3.0/accesses/accessid/STTA0001 HTTP/1.1
```
eller
```http
GET /api/3.0/accesses/accessport/5216010765746820302F31020B31302E31302E31302E3130 HTTP/1.1
```

Response:
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "accessId": "STTA0001",
    "accessPort": "5216010765746820302F31020B31302E31302E31302E3130",
    "feasibility": {
        "streetName": "Testvägen",
        "streetNumber": "100A",
        "postalCode": "10000",
        "city": "Ankeborg",
        "mduApartmentNumber": "1001",
        "mduDistinguisher": "12121212",
        "outlet": "A-11-14",
        "population": "Hemsöhem",
        "services": [
            {
                "service": "BB-100-100",
                "connection": "2014-03-01"
            }, {
                "service": "BB-100-10",
                "connection": "2013-10-12"
            }, {
                "service": "BB-10-10",
                "connection": "2013-10-12"
            }, {
                "service": "IPTV",
                "connection": "2013-10-12"
            }, {
                "service": "VOIP",
                "connection": "2013-08-13"
            }
        ],
        "coFiberConverter": "LASER_3001X_MK2",
        "coCpeSwitch": "",
        "coCpeRouter": "NETGEAR WNDR4000"
    },
    "realisedState": [
        { 
            "service": "BB-100-100",
            "suspended": "NOT_SUSPENDED",
            "customer": {
                "name": "Kalle Anka",
                "personnummer": "",
                "email": "karl@ankeborg.se",
                "phone": "",
                "mobilePhone": ""
            },
            "deliveryAddress": {
                "smsNotificationPhone": "",
                "streetName": "Testvägen",
                "streetNumber": "100A",
                "postalCode": "10000",
                "city": "Ankeborg"
            }
        }
    ],
    "requestedState": [
        { 
            "service": "BB-1000-100",
            "suspended": "NOT_SUSPENDED",
            "customer": {
                "name": "Kalle Anka",
                "personnummer": "",
                "email": "karl@ankeborg.se",
                "phone": "",
                "mobilePhone": ""
            },
            "deliveryAddress": {
                "smsNotificationPhone": "",
                "streetName": "Testvägen",
                "streetNumber": "100A",
                "postalCode": "10000",
                "city": "Ankeborg"
            }
        }
    ],
    "mismatchCauses": [
        { "type": "IN_PROGRESS", "cause": "" },
        { "type": "OTHER", "cause": "Ny mediaomvandlare är beställd. Tills dess har kunden 100/100." }
    ]
}
```

## Fältbeskrivningar

* `null` är inte ett giltigt värde för något fält.
* Fält markerade med _obligatoriskt_ får inte vara tomma stängen (`""`)

<table>
    <tbody>
        <tr>
            <td><strong>Fält</strong></td>
            <td><strong>Förklaring</strong></td>
        </tr>
        <tr>
            <td>
                <code>feasibility / *</code>
            </td>
            <td>
                För alla `feasibility`-fält, se <a href="feasibility.md">Feasibility</a>.
            </td>
        </tr>
        <tr>
            <td>
                <code>realisedState, requestedState</code>
            </td>
            <td>
                `realisedState` och `requestedState` har samma struktur och format.<br>
                `realisedState` är faktiskt access-state hos KO nu.<br>
                `requestedState` är state som anropande TL beställt av KO.<br>
                Om `realisedState` och `requestedState` skiljer sig får inte `mismatchCauses` vara tom.<br>
                <em>obligatorisk</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>realisedState, requestedState / service</code>
            </td>
            <td>
                Syftar på en teknisk tjänst i Feasibility. <em>obligatorisk</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>realisedState, requestedState / suspended</code>
            </td>
            <td>
                Anger om tjänsten är aktiv/beställd med blockerad av KO. Exempel på användning är när kund inte betalt räkning till TL. Då kan TL begära att tjänsten suspenderas.<br>
                Giltiga värden: "NOT_SUSPENDED", "SUSPENDED". <em>obligatorisk</em><br>
            </td>
        </tr>
        <tr>
            <td>
                <code>realisedState, requestedState / customer</code>
            </td>
            <td>
                
            </td>
        </tr>
        <tr>
            <td>
                <code>realisedState, requestedState / deliveryAddress</code>
            </td>
            <td>
                
            </td>
        </tr>
        <tr>
            <td>
                <code>mismatchCauses</code>
            </td>
            <td>
            
            </td>
        </tr>
        <tr>
            <td>
                <code>mismatchCauses / type</code>
            </td>
            <td>
            
            </td>
        </tr>
        <tr>
            <td>
                <code>mismatchCauses / cause</code>
            </td>
            <td>
            
            </td>
        </tr>
    </tbody>
</table>
