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
    "expectedState": [
        { 
            "service": "BB-1000-100",
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
    "actualState": [
        { 
            "service": "BB-100-100",
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

Listan omfattar de fält som Availability definererar _utöver_ <a href="feasibility.md">Feasibility</a>.

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
                <code>services</code>
            </td>
            <td>
                Anger accessens tjänster och feasibility per tjänst. Se fält per tjänst nedan. <br/>
                Oavsett vilken Service Provider som hämtar feasibility-data skall services innehålla samma information.<br/>
                <em>obligatorisk</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>services / service</code>
            </td>
            <td>
                Id/namn på teknisk tjänst som avses. Den tekniska tjänsten kan beställas via Service Activation API. <em>obligatorisk</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>services / connection</code>
            </td>
            <td>
                Anger när accessen kopplas in och den tekniska tjänsten blir aktiverbar första gången. På det angivna datumet, eller om connection är "YES", skall det gå att aktivera tjänster på accessen. <br/>
                Om tjänsten är aktiverbar men datumet är okänt kan "YES" användas för att indikera det. Datumet får tidigast vara 1970-01-01.<br/>
                <br/>
                <em>"YES", "NO" eller ISO-8601 datum (YYYY-MM-DD), obligatoriskt</em><br/>
                Exempel: YES, NO, 2012-07-01
            </td>
        </tr>
        <tr>
            <td>
                <code>services / available</code>
            </td>
            <td>
                Anger om/när teknisk tjänst är/blir tillgänglig för beställning och leverans.<br/>
                Om tjänsten inte är tillgänglig för beställning av anropande TL skall det indikeras med "NO" eller datum då tjänsten blir tillgänglig.
                <br/> Datumet får tidigast vara 1970-01-01.<br/>
                <br/>
                Innan tjänsten är tillgänglig första gången sammanfaller Available och Connection.<br/>
                <br/>
                <em>"YES", "NO" eller ISO-8601 datum (YYYY-MM-DD), obligatoriskt</em><br/>
                Exempel: YES, NO, 2012-07-01<br>
                Exempel: Anropande TL har BB 10/10 aktivt. BB 10/10, BB 100/100 kan beställas om BB 10/10 först avaktiveras. KO skall svara med available YES.<br>
                Exempel: Annan TL har BB 10/10 aktivt. BB 10/10, 100/100 skall svara med NO eller datum.
            </td>
        </tr>
        <tr>
            <td>
                <code>services / forcedTakeoverPossible</code>
            </td>
            <td>
                Anger om flaggan "force" i en beställning kommer att ha effekt.<br>
                Alltså, är det möjligt att använda "force" (Forced Takeover) för att KO skall börja leverera anropande TLs tjänst istället för annan TLs tjänst. <br>
                Om Forced Takeover inte stöds skall <b>false</b> returneras.<br>
                <em>true eller false, obligatoriskt</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>active</code>
            </td>
            <td>
                Lista över de tjänster som är aktiva på accessen för inloggad TL. <em>obligatorisk</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>active / service</code>
            </td>
            <td>
                Service-namn på den aktiva tjänsten. Skall finnas i "services"-listan. <em>text, obligatoriskt</em><br>
                <br>
                Exempel: "BB-100-10"
            </td>
        </tr>
        <tr>
            <td>
                <code>active / option82</code>
            </td>
            <td>
                Fältet används av tjänsteleverantör för att korrelera en DHCP förfrågan till en Access. Värdet utgör alltså en nyckel som DHCP, Radius och TR69-servrar använder för att slå upp access-specifik information. Option82 måste vara unikt inom en kommunikationsoperatörs bestånd. Se <em>text, obligatoriskt</em><br>
                <a href="option82.md">Option82 format</a>
                <br>
                Exempel: "5216010765746820302F31020B31302E31302E31302E3130"<br/>
            </td>
        </tr>
        <tr>
            <td>
                <code>active / equipment</code>
            </td>
            <td>
                Lista av utrustning som tjänsteleverantör angett för tjänsten.<br>
                Se <a href="service_activation.md">Service Activation</a> för mer information.
            </td>
        </tr>
    </tbody>
</table>
