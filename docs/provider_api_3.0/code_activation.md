# Code Activation API

Syftet med "Code Activation API" är att erbjuda Tjänsteleverantörer att automatiskt identifiera kunder genom att slutkund anger en kod i ett gränssnitt som tillhör TL.

KO erbjuder kund möjlighet att navigera till TLs kodaktiveringsportal och skickar med en token som TL kan använda för att hämta (för aktiveringen) nödvändig information.

Flöde:
1) Kund kopplar in en dator i mediaomvandlaren i sitt hem.
2) Kund försöker surfa och kommer till KOs portal (eftersom kund inte har någon bredbandstjänst).
3) KO erbjuder kunden möjlighet att köpa tjänster, eller att aktivera redan köpta tjänster hos Tjänsteleverantörer.
4) Kunden väljer att aktivera en redan köpt tjänst (kund har varit i kontakt med KO tidigare).
5) KO redirectar kunden till: 
    `https://kodaktivering.exempel-tl.se/ko=ko&accessid=STTA0001&tid=2016-08-25T13%3A41%3A39Z&hash=lNjQgc3RyaW5nLiA%3D`
   Ur URL kan vi utläsa: 
   ko: exempelko
   accessid: STTA0001
   tid: 2016-08-25T13:41:39Z (ISO8601 date/time, tidpunkt för generering av länk?)
   hash: lNjQgc3RyaW5nLiA=
6) I exemplet ovan är det uppenbart vad ko, accessid och tid innebär. Hash behöver däremot förklaring. Värdet för `hash` används för att validera att informationen (ko, accessid, tid) kom från KO och att den inte manipulerats på vägen. Funktionen för detta beskrivs nedan under [MAC-algoritm](#mac-algoritm).
7) När kund har lyckats att aktivera sig i TLs kodaktiveringsportal kommer TL att beställa rätt tjänster hos KO, och kunden kommer igång med sitt bredband.

## MAC Algoritm<a name="mac-algoritm"></a>

`hash` beräknas genom att konkatenera ko, accessid, tid till en sträng. Efter det används X med en gemensamt delad (mellan KO och TL) nyckel.

Här behöver vi jobba mer.
