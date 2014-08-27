# Provider Interface API V2.2

API mellan tjänsteleverantör (SP) och kommunikationsoperatörer (KO).

Innehåller operationer med följande syften:
* Hämta säljdata/univers från KO.
* Beställa tjänster hos KO.
* Hämta tekniska detaljer och status på aktiva tjänster ho KO.

## Terminologi

### Access

Com Hem definierar en _Access_ som en avlämningspunkt som måste aktiveras för att en slutkund skall få tjänst, typiskt en port i en access-
switch.

### Feasibility

Com Hem definierar _Feasibility_ som lista med _accesser_ med tillhörande data för unikt kunna identifiera accesser (adress, lägenhetsbetec
kningar etc.) och vilka tjänster som är potentiellt beställningsbara (per _access_). _Feasibility_ omfattar inte om tjänsterna är aktiverad
e eller inte. _Feasibility_ används till säljkampanjer, täckningskartor och liknande. _Feasiblitity_ kan även användas vid kontakt med slut
kund för att identifiera specifik _access_ att aktivera.

### Availability

Com Hem definierar _Availability_ som aktuell status vilka tjänster som är aktiva och vilka som går att leverera på en specifik avlämningspunkt. _Availability_-frågor används ofta interaktivt, exempelvis under konversation med potentiell slutkund.

## Innehåll

1. [Ändringar sedan 2.1](changelog.md)
2. [Feasibility API](feasibility.md)
3. [Availability API](availability.md)
4. [Service Activation API](service_activation.md)
5. [Order Events API](order_events.md)
6. [Option82 Lookup API](option82_lookup.md)
7. [Option82](option82.md)
8. [Övergripande](misc.md)


