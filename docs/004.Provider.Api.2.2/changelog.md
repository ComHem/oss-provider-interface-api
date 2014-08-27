---
out:provider_interface_2.2_changelog.html
---
# Ändringar från Provider Interface API V2.2

## Separation mellan Feasibility (Bulk) och Availability (per Access)

Bulk-APIt kallas framledes "Feasibility API" och "Per Access-API" kallas framledes "Availability API".
Feasibility har förenklats. Bakgrunden är att Feasibility skall bli enklare och billigare att implementera.
Availability finns endast per Access vid behov och kan därmed implementeras enklare än om det är en del av Feasibility.

* Feasibility: Services.serviceType borttaget.
* Feasibility: Services.available borttaget.
* Feasibility: Services.forcedTakeoverPossible borttaget.
* Feasibility: Active är borttaget.
* Availability: Services.serviceType borttaget.

## Event-baserad Orderstatus

Ett nytt API, som del av Service Activation API, ger möjlighet att fråga om Order Status i bulk. Det betyder att Tjänsteleverantörer kan fråga oftare samtidigt som det är ett stort antal utestående ordrar.

## API för uppslag av AccessID för givet Option82

För att stöda utrustningsbaserad autoaktivering behöver Com Hem kunna slå upp vilket AccessID ett Option82 avser.

