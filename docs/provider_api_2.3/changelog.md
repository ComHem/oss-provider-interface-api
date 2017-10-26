# Provider API 2.3

API 2.3 innehåller ändringar motiverade av [GDPR][wikipedia-gdpr].

# Skillnader mot API 2.2

## Ändringar som bryter API

* Borttagning av reglerad kundinformation i Service Activation API.

Påverkar Service Activation API där fälten fälten `customer` och `deliveryAddress` är borttagna.

## Nytt i API 2.3

### Service Activation API

* TL-specifika referenser i Service Activation erbjuder TL att extra information till KO.

### Backport av API för kundinformation från API 3.0

Ger KO möjlighet att, för en access, i realtid fråga TL om kundinformation.
Sådan access loggas, och regleras i enlighet med GDPR.

[wikipedia-gdpr]: https://sv.wikipedia.org/wiki/Allm%C3%A4nna_dataskyddsf%C3%B6rordningen "Allmänna_dataskyddsförordningen" 
