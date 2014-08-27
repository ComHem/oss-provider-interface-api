# Övergripande Information

## Endpoints

_En_ adress pekar ut bas-urlen till APIt, exv. http://host/api/2.1/

Under bas-urlen förväntas tjänsterna finnas, bland annat /orders/ (http://host/api/2.1/orders/) och /accesses/ (http://host/api/2.1/accesses/)

## Autentisering

Autentisering av Service Provider sker genom HTTP BASIC. Se [RFC-2617][rfc2617]. 

[rfc2617]: http://www.ietf.org/rfc/rfc2617.txt "HTTP Authentication: Basic and Digest Access Authentication"

## Character Encoding

Alla APIer använder UTF-8. 

