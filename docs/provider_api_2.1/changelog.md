# Provider Interface API V2.1 (deprecated)

## Förbättrat stöd / tydlighet för tillgängliga tjänster

Sellable, Activatable är borttagna och ersatta med enklare varianter.

Connection: Anger när access/tjänst kopplas in alternativt kopplades in första gången.

Available: Anger om access/tjänst är tillgänglig och ledig för beställning.

* Services.Sellable borttaget.
* Services.Activatable borttaget.
* Services.Connection tillagt.
* Services.Available tillagt.

## Stöd för Forced Takeover

För att kunna avgöra om en Forced Takeover är möjlig vid beställning har det lagts till ett fält ("forcedTakeoverPossible") i Feasibility för att indikera detta.

Vid beställning av tjänster har det lagts till ett obligatoriskt fält ("forcedTakeover"), om satt till "true" avser beställningen att ersätta existerande tjänster från annan tjänsteleverantör.

* Nytt fält i Feasibility: Services.forcedTakeoverPossible
* Nytt fält i Service Activation: forcedTakeover

## Stöd för att skicka och hämta Vendor-ID

När kundplacerad utrustning av viss typ kan användas för flera tjänster - men kund enbart köpt en av dem - kan Tjänsteleverantör ange Vendor-ID som styr hur utrustningstyp ska mappas till tjänst.

* Nytt fält i Orderläggning: equipment
* Nytt fält i Feasibility: active.equipment

## Borttagna obsoleta fält

* Service Activation : OperationDate borttaget i både Request och Response.
* Feasibility : ActivationDeliveryDays borttaget.

