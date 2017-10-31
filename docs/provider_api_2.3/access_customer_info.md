# TL API: Kundinformation för Access

Syftet med API är att låta KO hämta kundinformation om en access från Tjänsteleverantör.

*OBS! Till skillnad mot andra APIer är KO klient och TL server i detta API.*

## Exempel

Request:
```http
GET /api/2.3/access/STTA0001 HTTP/1.1
```

Response:
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "BB-1000-100": {
        "customer": {
            "name": "Kalle Anka",
            "email": "karl@ankeborg.se",
            "phone": "",
            "mobilePhone": ""
        },
        "spReferences": {
            "key": "value",
            "key2": "value"
        }
    }
}
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

