# Code Activation API

Syftet med "Code Activation API" är att erbjuda Tjänsteleverantörer att automatiskt identifiera kunder genom att slutkund anger en kod i ett gränssnitt som tillhandahållet av TL.

KO erbjuder kund möjlighet att navigera till TLs kodaktiveringsportal tillsammans med (för kundaktivering) nödvändig information.

## Flöde:

1. Kund kopplar in en dator i mediaomvandlaren i sitt hem.
2. Kund försöker surfa och kommer till KOs portal (eftersom kund inte har någon bredbandstjänst).
3. KO erbjuder kunden möjlighet att köpa tjänster, eller att aktivera redan köpta tjänster hos Tjänsteleverantörer.
4. Kunden väljer att aktivera en redan köpt tjänst (kund har varit i kontakt med KO tidigare).
5. KO redirectar kunden till `https://kodaktivering.exempel-tl.se/ko=ko&accessid=STTA0001&tid=2016-08-25T13%3A41%3A39Z&hash=lNjQgc3RyaW5nLiA%3D`<br>
6. I exemplet ovan är det uppenbart vad ko, accessid innebär. `tid` och `hash` behöver däremot förklaring:
7.    Ur URL kan vi utläsa:<br>
  * `tid` (2016-08-25T13:41:39Z) är tiden när redirect-länken genererades i ISO8601 date/time. Den används för att motverka sk. 'replay'-attacker.
  * `hash` används för att validera att informationen (ko, accessid, tid) kom från KO och att den inte manipulerats på vägen.<br> Funktionen för detta beskrivs nedan under [MAC-algoritm](#mac-algoritm).
7. När kund har lyckats att aktivera sig i TLs kodaktiveringsportal kommer TL att beställa rätt tjänster hos KO, och kunden kommer igång med sitt bredband.

## MAC Algoritm<a name="mac-algoritm"></a>

`hash` beräknas genom att konkatenera ko, accessid, tid till en sträng. Efter det används X med en gemensamt delad (mellan KO och TL) nyckel.

Här behöver vi jobba mer.
