# Ändringar mot tidigare versioner

## Nytt i API 3.0

* Format för Feasibility är ändrat till Concatenated JSON.
* Nya fält:
    * Fastighetsbeteckning
    * Urkoppling
    * Media
* Borttagna fält:
    * Feasibility & Availability: PremisesType
    * Feasibility & Availability: CountryCode
* Feasibility & Availability: Hopslagning av streetNumber och streetLittera till streetNumber.
  På så vis används streetNumber för gatunummer och gatubokstav "10A" likaväl som vid metertalsadresser: "100-30".
* "Service Activation API" är utbytt till ett State-baserat API. "Access & Activation API".
* "Availability API", "Service Activation API" och "Option82 Lookup API" är hopslaget med "Access & Activation API".
* "Order Events API" är utbytt till "Access Event API" och är en grundläggande del i nya "Access & Activation API".
* Stöd för Suspend av slutkunds tjänster.

## Nytt i API 2.2.3

### Stöd för uttagsnummer i Feasibility och Availability.

I vissa sammanhang känner slutkunden till sitt uttagsnummer och använder det i kommunikationen med TL.

## Nytt i API 2.2.2

### Nytt API: FM Link Status API

API för att se länkstatus för felsökning är tillagt genom FM Link Status API.

## Nytt i API 2.2.1

### AccessID får innehålla '.' och '-'

Validering för AccessID har utökats till att tillåta '.' och '-'.

## Nytt i API 2.2

### Separation mellan Feasibility (Bulk) och Availability (per Access)

Bulk-APIt kallas framledes "Feasibility API" och "Per Access-API" kallas framledes "Availability API".
Feasibility har förenklats. Bakgrunden är att Feasibility skall bli enklare och billigare att implementera.
Availability finns endast per Access vid behov och kan därmed implementeras enklare än om det är en del av Feasibility.

* Feasibility: Services.serviceType borttaget.
* Feasibility: Services.available borttaget.
* Feasibility: Services.forcedTakeoverPossible borttaget.
* Feasibility: Active är borttaget.
* Availability: Services.serviceType borttaget.

### Event-baserad Orderstatus

Ett nytt API, som del av Service Activation API, ger möjlighet att fråga om Order Status i bulk. Det betyder att Tjänsteleverantörer kan fråga oftare samtidigt som det är ett stort antal utestående ordrar.

### API för uppslag av AccessID för givet Option82

För att stöda utrustningsbaserad autoaktivering behöver TL kunna slå upp vilket AccessID ett Option82 avser.

