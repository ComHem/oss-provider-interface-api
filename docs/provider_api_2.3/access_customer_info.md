# TL API: Kundinformation för Access

Syftet med API är att låta KO hämta kundinformation om en access från Tjänsteleverantör.

## Exempel

Request:
```http
GET /api/2.3/access/STTA0001 HTTP/1.1
```

Response:
```http
HTTP/1.1 200 OK
Content-Type: application/json

[
    {
        "service": "BB-1000-100",
        "customer": {
            "name": "Kalle Anka",
            "personnummer": "",
            "email": "karl@ankeborg.se",
            "phone": "",
            "mobilePhone": ""
        },
        "spReferences": {
            "key": "value",
            "key2": "value"
        }
    }
]
```

## Fältbeskrivningar

<table>
    <tbody>
        <tr>
            <td><strong>Fält</strong></td>
            <td><strong>Förklaring</strong></td>
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
                <code>customer</code>
            </td>
            <td>
                Syftet med customer är att ge KO information om Tjänsteleverantörens slutkund.<br>
                JSON-strukturen är obligatorisk och kommer alltid skickas med, men samtliga attribut kan vara tomma.<br>
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
                <code>spReferences</code>
            </td>
            <td>
                `spReferences` är ett frivilligt fält, som, om det finns, måste vara ett objekt med en nivå av key/value.
                Alla values skall vara strängar.<br>
                Syftet med `spReferences` är att erbjuda en mekanism till Tjänsteleverantörer att kunna ange data vid en aktivering, som TL senare själv kan utläsa. Inga andra garantier eller funktioner än lagring av `spReferences` garanteras.<br>
                TL bestämmer själv nycklar och värden inuti spReferences-objektet och en KO som implementerar API skall spara och återge `spReferences` som skickats vid aktivering.<br>
                Nycklar och värden måste vara strängar, aldrig null, endast i en nivå (se exempel) och har maximal innehållslängd på 255 tecken.<br>
            </td>
        </tr>
    </tbody>
</table>

## Felhantering och beteende vid orderläggning

Vid order med korrekt format och korrekt innehåll skall svaret se ut på följande sätt:

```http
HTTP/1.1 201 CREATED
Content-Type: application/json
Location: /api/2.3/orders/ec4bc754-6a30-11e2-a585-4fc569183061

{
    "path": "/api/2.3/orders/ec4bc754-6a30-11e2-a585-4fc569183061",
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

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{ "cause": "Unknown service: 'INTERNET_FLUGA'" }
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
    "path": "/api/2.3/orders/ec4bc754-6a30-11e2-a585-4fc569183061",
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
GET /api/2.3/orders/ec4bc754-6a30-11e2-a585-4fc569183061 HTTP/1.1
```

Response:
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "path": "/api/2.3/orders/ec4bc754-6a30-11e2-a585-4fc569183061",
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
