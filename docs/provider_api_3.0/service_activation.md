# Service Activation API

## Orderläggning, Exempel: Aktivering

Request:
```http
POST /api/2.2/orders/ HTTP/1.1
Content-Type: application/json

{
    "accessId": "STTA0001",
    "service": "BB-100-10",
    "operation": "ACTIVATE",
    "forcedTakeover": false,
    "customer": {
        "firstName": "Kalle",
        "lastName": "Anka",
        "socialSecurityNumber": "192012317777",
        "email": "karl@ankeborg.se",
        "phone": "031-119119",
        "mobilePhone": ""
    },
    "deliveryAddress": {
        "smsNotificationPhone": "",
        "streetName": "Testvägen",
        "streetNumber": "100",
        "streetLittera": "",
        "zipCode": "10000",
        "city": "Ankeborg"
    },
    "equipment": [
        { "vendorId": "CH_BROADBAND" }
    ]
}
```

Response:
```http
HTTP/1.1 201 CREATED
Last-Modified: Fri, 31 Aug 2012 12:03:28 GMT
Location: /api/2.2/orders/ec4bc754-6a30-11e2-a585-4fc569183061
Content-Type: application/json

{
    "path": "/api/2.2/orders/ec4bc754-6a30-11e2-a585-4fc569183061",
    "accessId": "STTA0001",
    "service": "BB-100-10",
    "operation": "ACTIVATE",
    "state": "RECEIVED",
    "message": ""
}
```

## Orderläggning, Exempel: Avaktivering

Request:
```http
POST /api/2.2/orders/ HTTP/1.1
Content-Type: application/json

{
    "accessId": "STTA0001",
    "service": "BB-100-10",
    "operation": "DEACTIVATE"
}
```

Response:
```http
HTTP/1.1 201 CREATED
Last-Modified: Fri, 31 Aug 2012 12:03:28 GMT
Location: /api/2.2/orders/ec4bc754-6a30-11e2-a585-4fc569183061
Content-Type: application/json

{
    "path": "/api/2.2/orders/ec4bc754-6a30-11e2-a585-4fc569183061",
    "accessId": "STTA0001",
    "service": "BB-100-10",
    "operation": "DEACTIVATE",
    "state": "RECEIVED",
    "message": ""
}
```

## Orderläggning - Fältbeskrivningar

<table>
    <tbody>
        <tr>
            <td><strong>Fält</strong></td>
            <td><strong>Förklaring</strong></td>
        </tr>
        <tr>
            <td>
                <code>accessId</code>
            </td>
            <td>
                Ett, per kommunikationsoperatör, unikt ID på en access.<br>Får enbart bestå av tecknen a-z, A-Z, 0-9. <em>text, obligatoriskt, max 32 tecken, [a-zA-Z0-9]+</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>service</code>
            </td>
            <td>
                Anger teknisk tjänst som skall aktiveras/avaktiveras på accessen. <br>
                Värden för tekniska tjänster är KO-specifika och erhålls genom Feasibility-APIt. <em>text, obligatoriskt</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>operation</code>
            </td>
            <td>
               Anger om tjänsten skall aktiveras eller avaktiveras.<br>
               Giltiga värden: "ACTIVATE", "DEACTIVATE". <em>text, obligatoriskt</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>forcedTakeover</code>
            </td>
            <td>
                Anger om anropande TLs beställning skall ersätta annan Service Providers aktiva tjänster. Det behöver enbart fungera om Feasibility indikerat att funktionen skall fungera. Det gäller enbart för ACTIVATE-ordrar. Skall inte skickas vid DEACTIVATE. <br>Stöd för forcedTakeover är inte obligatoriskt, men i de fall det inte stöds skall tjänsten ändå kunna ta emot en fullständig beställning med "forcedTakeover: false".
                <em>boolean (true/false), obligatoriskt för ACTIVATE</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>customer</code>
            </td>
            <td>
                Syftet med customer är att ge KO information om Tjänsteleverantörens slutkund.<br>
                JSON-strukturen är obligatorisk och kommer alltid skickas med, men samtliga attribut kan vara tomma.<br>
                <em>Obligatoriskt vid ACTIVATE. Skall inte skickas med vid DEACTIVATE.</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>customer.socialSecurityNumber</code>
            </td>
            <td>
                Format: yyyymmddnnnn.
            </td>
        </tr>
        <tr>
            <td>
                <code>customer.phone</code>
            </td>
            <td>
                Exempelformat: +4631650000
            </td>
        </tr>
        <tr>
            <td>
                <code>customer.mobilePhone</code>
            </td>
            <td>
                Exempelformat: +4631650000
            </td>
        </tr>
        <tr>
            <td>
                <code>deliveryAddress</code>
            </td>
            <td>
                Syftet med deliveryAddres är att ge KO information om leveransadress för utrustning till en kund. Det behöver inte vara samma som leveransadressen för tjänsten.<br>
                JSON-strukturen är obligatorisk och kommer alltid skickas med, men samtliga attribut kan vara tomma.<br>
                <em>Obligatoriskt vid ACTIVATE. Skall inte skickas med vid DEACTIVATE.</em>
            </td>
        </tr>
       <tr>
            <td>
                <code>deliveryAddress.mobilePhone</code>
            </td>
            <td>
                Exempelformat: +4631650000
            </td>
        </tr>
        <tr>
            <td>
                <code>equipment</code>
            </td>
            <td>
                Equipment innehåller en lista över den utrustning som kan användas för att avgöra vilken tjänst/KO en tjänst tillhör hos kunden.<br>
                <br>
                Ett use-case är vid Single-Play-Telefoni, där en TL använder en trådlös router för enbart Telefoni. Då behöver KO kunna avgöra att VendorId CH_BROADBAND skall användas för Telefoni istället för bredband.<br>
                <br>
                Listan är per tjänst, och den fullständiga listan skall alltid skickas vid varje aktivering.<br>
                <br>
                Vid avaktivering skall listan av equipment rensas.<br>
                <br>
                När en order enbart avser ändring av utrustning kan KO välja att starta en order för det, eller svara direkt med att det är utfört.
            </td>
        </tr>
        <tr>
            <td>
                <code>equipment.vendorId</code>
            </td>
            <td>
                <em>text, obligatoriskt</em><br>
                <br>
                Exempel: "CH_BROADBAND"
            </td>
        </tr>
    </tbody>
</table>

## Felhantering och beteende vid orderläggning

Vid order med korrekt format och korrekt innehåll skall svaret se ut på följande sätt:

```http
HTTP/1.1 201 CREATED
Content-Type: application/json
Location: /api/2.2/orders/ec4bc754-6a30-11e2-a585-4fc569183061

{
    "path": "/api/2.2/orders/ec4bc754-6a30-11e2-a585-4fc569183061",
    "accessId": "STTA0001",
    "service": "BB-100-10",
    "operation": "ACTIVATE",
    "state": "RECEIVED",
    "message": ""
}
```

Vid en inkorrekt order skall svar ske direkt med "400 Bad Request".

Det skall ske vid:

* Felaktig JSON
* Saknade fält
* Felaktigt innehåll i fält
* Felaktigt AccessId
* Felaktig Service för acceses.
* Felaktig kunddata.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{ "cause": "Missing customer.firstName" }
```

Om en annan tjänst med samma tjänstetyp redan är aktiv.
Exempel: Bredband 100/100 är aktivt vid beställning av Bredband 10/10.:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{ "cause": "Another Service of ServiceType 'Broadband' is already active." }
```

Om tjänsten redan har en aktiv order:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "path": "/api/2.2/orders/ec4bc754-6a30-11e2-a585-4fc569183061",
    "accessId": "STTA0001",
    "service": "BB-100-10",
    "operation": "ACTIVATE",
    "state": "RECEIVED",
    "message": ""
}
```

Om tjänsten redan är aktiv vid `ACTIVATE`, eller inaktiv vid `DEACTIVATE`:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "accessId": "STTA0001",
    "service": "BB-100-10",
    "operation": "ACTIVATE",
    "state": "DONE_SUCCESS",
    "message": ""
}
```

Om tjänsten (tjänstetypen) inte går att beställa för att den är tagen av en annan Service Provider:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{ "cause": "ServiceType is already claimed by other Service Provider." }
```


## Hämtning av Order Status - Exempel

Request:
```http
GET /api/2.2/orders/ec4bc754-6a30-11e2-a585-4fc569183061 HTTP/1.1
```

Response:
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "path": "/api/2.2/orders/ec4bc754-6a30-11e2-a585-4fc569183061",
    "accessId": "STTA0001",
    "service": "BB-100-10",
    "operation": "ACTIVATE",
    "state": "RECEIVED",
    "message": ""
}
```

## Hämtning av Order Status - Fältbeskrivningar

<table>
    <tbody>
        <tr>
            <td><strong>Fält</strong></td>
            <td><strong>Förklaring</strong></td>
        </tr>
        <tr>
            <td>
                <code>path</code>
            </td>
            <td>
               Path till order. Anges alltid från /. <em>text, obligatoriskt</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>state</code>
            </td>
            <td>
               Anger orderns status. Statusen är "RECEIVED" tills tjänsten är aktiverad eller har misslyckats. När ordern lyckas skall state bli "DONE_SUCCESS". När ordern misslyckas skall state uppdateras till "DONE_FAILED". Ordern får sedan aldrig ändra state.<br>
               Giltiga värden: "RECEIVED", "DONE_SUCCESS", "DONE_FAILED". <em>text, obligatoriskt</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>message</code>
            </td>
            <td>
               Om en order misslyckas (state == "DONE_FAILED"), kan message användas för att beskriva varför. Fältet används inte annars. Fältet skall alltid finnas men får vara tomt. <em>text, obligatoriskt*</em>
            </td>
        </tr>
    </tbody>
</table>
