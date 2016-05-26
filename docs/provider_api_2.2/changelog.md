# Ändringar mot tidigare versioner

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

