# CSV Access Upload API V1.1

** Notera: Detta API är under utveckling och inte 100% stabiliserat. **

Dokumentet innehåller ett CSV-format för att beskriva accesser som läses in från kommunikationsoperatörer.

Med "access" menas en unik avlämningspunkt, alltså en port på en access-switch som terminas i exempelvis en lägenhet.

Eftersom det är fördelaktigt att kunna hålla Tjänsteleverantörs system uppdaterat och "i synk" med kommunikationsoperatören om det aktuella beståndet bör nya uppdaterade CSV-filer laddas upp frekvent.

## Ändringar mot V1.0

Fälten portSpeed, iptv och rfof har tillkommit. Dessa medger att man via CSV kan läsa in accesser ur ett heterogent bestånd. Dvs ett bestånd där man per access anger portens maxhastighet, och om iptv och/eller analog TV går att leverera.

## Exempel

<pre style="overflow: auto">
accessId,streetName,streetNumber,streetLittera,postalCode,city,countryCode,premisesType,mduApartmentNumber,mduDistinguisher,outletId,population,option82,coFiberConverter,coCpeSwitch,coCpeRouter,activationDate,salesStartDate,portSpeed,iptv,rfof
2222,TESTVÄGEN,6,D,10000,ANKEBORG,SE,MDU_APARTMENT,1002,,ABC1,pop1,abc123,LASER_3001X_MK2,,NETGEAR_WNDR4000,YES,YES,100,YES,NO
3333,ALLMÄNNINGSGATAN,4,,10001,KRÖSUSKÖPING,SE,MDU_APARTMENT,,0101C11,ABC2,pop1,jkl999,LASER_3001X_MK2,,NETGEAR_WNDR4000,YES,YES,1000,YES,YES
4444,SPECIALISTVÄGEN,5,,10002,SKUTTBY,SE,RESIDENTIAL_HOUSE,,,ABC3,pop1,def456,LASER_3001X_MK2,,NETGEAR_WNDR4000,YES,YES,100,NO,NO
5555,PRODUKTIONSVÄGEN,2,,10003,BAMSESJÖ,SE,RESIDENTIAL_HOUSE,,,ABC4,pop1,ghi789,LASER_3001X_MK2,,NETGEAR_WNDR4000,YES,YES,1000,NO,YES
</pre>

Ur exemplet kan vi utläsa:

* Alla accesser har activationDate och salesStartDate satta till YES, så tjänster kan säljas och provisioneras direkt.
* Om KO vill indikera att en access är "upptagen" skall activationDate och salesStartDate sättas till NO.
* Runt alla textfält kan man använda "", vilket också är nödvändigt om texten i sig innehåller komman
* Två accesser stödjer Gigabit Ethernet, två accesser kan leverera RFOF (RF over fiber), samt två accesser kan leverera IPTV.

## Kolumnbeskrivningar

* Kolumner markerade med <em>obligatoriskt</em> får inte vara tomma

<table>
    <tbody>
        <tr>
            <td><strong>Kolumn</strong></td>
            <td><strong>Förklaring</strong></td>
        </tr>
        <tr>
            <td>
                <code>accessId</code>
            </td>
            <td>
                Ett, per kommunikationsoperatör, unikt ID på en access.<br>Får enbart bestå av tecknen a-z, A-Z, 0-9. <em>text, obligatoriskt, max 32 tecken, [a-zA-Z0-9]+</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>streetName</code>
            </td>
            <td>
                Gatunamn.<br> I fallet "Kungsgatan 10G" är StreetName "Kungsgatan". <em>text, obligatoriskt</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>streetNumber</code>
            </td>
            <td>
                 Gatunummer.<br> I fallet "Kungsgatan 10G" är StreetNumber "10". I fallet "Lantvägen 550-70" är StreetNumber "550". Enbart siffror. <em>text</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>streetLittera</code>
            </td>
            <td>
                Gatubokstav/Uppgång.<br> I fallet "Kungsgatan 10G" är StreetLittera "G". I fallet "Lantvägen 550-70" är StreetLittera "70". <em>text</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>postalCode</code>
            </td>
            <td>
                Postnummer. Exempelvis "41369". Min 10000, max 99999. Alltid fem siffror, vi tillåter att man har ett space, dvs både 12345 och 123 45 är tillåtna. <em>text, obligatoriskt</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>city</code>
            </td>
            <td>
                Postort. Exempelvis "Göteborg". <em>text, obligatoriskt</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>countryCode</code>
            </td>
            <td>
                Landskod. Följer ISO 3166-1 för landskoder. Exempel: "SE" för Sverige. <em>text, ISO 3166-1, obligatoriskt</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>premisesType</code>
            </td>
            <td>
                PremisesType beskriver avlämningspunktens lokal. <em>obligatoriskt</em>
<dl>
<dt>MDU_APARTMENT</dt><dd>Lägenhet i flerbostadshus. Delad fastighetsbeteckning.</dd>
<dt>MDU_COMMON</dt><dd>Gemensamt utrymme i flerbostadshus. Delad fastighetsbeteckning.</dd>
<dt>RESIDENTIAL_HOUSE</dt><dd>Bostad som har egen fastighetsbeteckning.</dd>
<dt>COMMERCIAL</dt><dd>Lokal, men utan tillträde från allmänheten. Till exempel ett kontor.</dd>
<dt>PUBLIC</dt><dd>Inrättning dit allmänheten har tillträde. Till exempel en restaurang eller ett gym.</dd>
</dl>
                Vi tillåter även Lantmäteriets BYGGTYP, specificerad <a href="https://www.lantmateriet.se/Global/Fastigheter/Fastighetsinformation/%C3%85tkomsts%C3%A4tt/Termkatalog_OFF.pdf">här</a>.
                Vi mappar deras värden mot våra typer såhär:
                <table>
                    <tbody>
                    <tr><td>01</td><td>PUBLIC</td><td>Badhus</td></tr>
                    <tr><td>02</td><td>PUBLIC</td><td>Brandstation</td></tr>
                    <tr><td>03</td><td>PUBLIC</td><td>Busstation</td></tr>
                    <tr><td>04</td><td>PUBLIC</td><td>Distributionsbyggnad</td></tr>
                    <tr><td>05</td><td>PUBLIC</td><td>Djursjukhus</td></tr>
                    <tr><td>06</td><td>PUBLIC</td><td>Försvarsbyggnad</td></tr>
                    <tr><td>07</td><td>PUBLIC</td><td>Hälsocentral</td></tr>
                    <tr><td>08</td><td>PUBLIC</td><td>Högskola</td></tr>
                    <tr><td>09</td><td>PUBLIC</td><td>Ishall</td></tr>
                    <tr><td>10</td><td>PUBLIC</td><td>Järnvägsstation</td></tr>
                    <tr><td>11</td><td>PUBLIC</td><td>Kommunhus</td></tr>
                    <tr><td>12</td><td>PUBLIC</td><td>Kriminalvårdsanstalt</td></tr>
                    <tr><td>13</td><td>PUBLIC</td><td>Kulturbyggnad</td></tr>
                    <tr><td>14</td><td>PUBLIC</td><td>Polisstation</td></tr>
                    <tr><td>15</td><td>PUBLIC</td><td>Reningsverk</td></tr>
                    <tr><td>16</td><td>PUBLIC</td><td>Ridhus</td></tr>
                    <tr><td>17</td><td>PUBLIC</td><td>Samfund</td></tr>
                    <tr><td>18</td><td>PUBLIC</td><td>Sjukhus</td></tr>
                    <tr><td>19</td><td>PUBLIC</td><td>Skola</td></tr>
                    <tr><td>20</td><td>PUBLIC</td><td>Sporthall</td></tr>
                    <tr><td>21</td><td>PUBLIC</td><td>Universitet</td></tr>
                    <tr><td>22</td><td>PUBLIC</td><td>Vattenverk</td></tr>
                    <tr><td>24</td><td>PUBLIC</td><td>Multiarena</td></tr>
                    <tr><td>30</td><td>RESIDENTIAL_HOUSE</td><td>Småhus friliggande</td></tr>
                    <tr><td>31</td><td>RESIDENTIAL_HOUSE</td><td>Småhus kedjehus</td></tr>
                    <tr><td>32</td><td>MDU_APARTMENT</td><td>Småhus radhus</td></tr>
                    <tr><td>33</td><td>MDU_APARTMENT</td><td>Flerfamiljshus</td></tr>
                    <tr><td>35</td><td>MDU_APARTMENT</td><td>Småhus med flera lägenheter</td></tr>
                    <tr><td>40</td><td>COMMERCIAL</td><td>Annan tillverkningsindustri</td></tr>
                    <tr><td>41</td><td>COMMERCIAL</td><td>Gasturbinanläggning</td></tr>
                    <tr><td>42</td><td>COMMERCIAL</td><td>Industrihotell</td></tr>
                    <tr><td>43</td><td>COMMERCIAL</td><td>Kemisk industri</td></tr>
                    <tr><td>44</td><td>COMMERCIAL</td><td>Kondenskraftverk</td></tr>
                    <tr><td>45</td><td>COMMERCIAL</td><td>Kärnkraftverk</td></tr>
                    <tr><td>46</td><td>COMMERCIAL</td><td>Livsmedelsindustri</td></tr>
                    <tr><td>47</td><td>COMMERCIAL</td><td>Metall- eller maskinindustri</td></tr>
                    <tr><td>48</td><td>COMMERCIAL</td><td>Textilindustri</td></tr>
                    <tr><td>49</td><td>COMMERCIAL</td><td>Trävaruindustri</td></tr>
                    <tr><td>50</td><td>COMMERCIAL</td><td>Vattenkraftverk</td></tr>
                    <tr><td>51</td><td>COMMERCIAL</td><td>Vindkraftverk</td></tr>
                    <tr><td>52</td><td>COMMERCIAL</td><td>Värmeverk</td></tr>
                    <tr><td>53</td><td>COMMERCIAL</td><td>Övrig industribyggnad</td></tr>
                    <tr><td>99</td><td>UNKNOWN</td><td>Ospecificerad</td></tr>
                    </tbody>
                </table>
            </td>
        </tr>
        <tr>
            <td>
                <code>mduApartmentNumber</code>
            </td>
            <td>
								Lägenhetsnummer enligt Lantmäteriet. Används för att tillsammans med en adress identifiera en unik access. I fallet när kund vill beställa tjänster kan de inte aktiveras hos KO utan att TL har fastställt vilket AccessID kunden har. Genom att unikt identifiera lägenheten med mduApartmentNumber, mduDistinguisher eller outletId kan TL fastställa exakt vilken access som skall aktiveras. <em>text, 4 digits, obligatoriskt<sup>1</sup></em><br>
								<br>
								Exempel: 1101, 0901, 1201, 1213.<br>
								<br>
                [1] En av <code>mduApartmentNumber</code>, <code>mduDistinguisher</code>, <code>outletId</code> måste finnas om <code>premisesType</code> är <code>"MDU_APARTMENT"</code>.
            </td>
        </tr>
        <tr>
            <td>
                <code>mduDistinguisher</code>
            </td>
            <td>
                Alternativ lokalbeteckning som identifierar lägenheten unikt per adress. Behöver inte följa Lantmäteriets format. <em>text, obligatoriskt<sup>2</sup></em><br>
								<br>
								Exempel: 28, 65113, 1234-1919.<br>
								<br>
	              [2] En av <code>mduApartmentNumber</code>, <code>mduDistinguisher</code>, <code>outletId</code> måste finnas om <code>premisesType</code> är <code>"MDU_APARTMENT"</code>.
            </td>
        </tr>
        <tr>
            <td>
                <code>outletId</code>
            </td>
            <td>
                Anger det ID som kan stå på kontakten/uttaget i väggen. Notera, om värdet är globalt unikt (i KO:s samtliga bestånd) så kan det vara en god idé att använda detta värde som accessId i.s.f. outletId. <em>text, obligatoriskt<sup>3</sup></em><br>
								<br>
	              [3] En av <code>mduApartmentNumber</code>, <code>mduDistinguisher</code>, <code>outletId</code> måste finnas om <code>premisesType</code> är <code>"MDU_APARTMENT"</code>.
            </td>
        </tr>
        <tr>
            <td>
                <code>population</code>
            </td>
            <td>
                Anger delbestånd i hela beståndet. Hela beståndet hämtas alltid in, men det kan filtreras och göras säljbart i olika etapper. <em>text</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>option82</code>
            </td>
            <td>
                Fältet används av TL för att korrelera en DHCP förfrågan till en Access. Värdet utgör alltså en nyckel som DHCP, Radius och TR69-servrar använder för att slå upp access-specifik information. Option82 måste vara unikt inom en kommunikationsoperatörs bestånd. <em>text, obligatoriskt</em><br>
                <br>
                Exempel: "192.168.248.85 ge-0/0/10"<br/>
            </td>
        </tr>
        <tr>
            <td>
                <code>coFiberConverter</code>
            </td>
            <td>
                Typ (tillverkare, modell) av Fiber Konverter Switch som "accessen" är kopplad till. Skall enbart användas för utrustning som KO tillhandahåller. <em>text</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>coCpeSwitch</code>
            </td>
            <td>
                Om CPEn är en Switch, är detta typen (tillverkare, modell) som är inkopplad. Enbart en av CpeSwitch och CpeRouter får finnas. Skall enbart användas för utrustning som KO tillhandahåller. <em>text</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>coCpeRouter</code>
            </td>
            <td>
                Om CPEn är en Router, är detta typen (tillverkare, modell) som är inkopplad. Enbart en av CpeSwitch och CpeRouter får finnas. Skall enbart användas för utrustning som KO tillhandahåller. <em>text</em>
            </td>
        </tr>
        <tr>
            <td>
                <code>activationDate</code>
            </td>
            <td>
                Anger när tjänster kan levereras till denna access. Om tjänster inte är aktiverbara och datumet är okänt kan "NO" användas för att indikera det. Om tjänster är aktiverbara men datumet är okänt kan "YES" användas för att indikera det. Datumet får tidigast vara 1970-01-01. <em>"YES", "NO" eller ISO-8601 datum (YYYY-MM-DD), obligatoriskt</em><br/>
                <br/>
                Exempel: YES, NO, 2012-07-01
            </td>
        </tr>
        <tr>
            <td>
                <code>salesStartDate</code>
            </td>
            <td>
                Anger när tjänster kan börja säljas på accessen (inte levereras).
            </td>
        </tr>
        <tr>
            <td>
                <code>portSpeed</code>
            </td>
            <td>
                Anger högsta hastighet på tjänster som kan levereras på accessen, anges i Mbit/s
                <br/>
                Exempel: 1000
            </td>
        </tr>
        <tr>
            <td>
                <code>iptv</code>
            </td>
            <td>
                Anger om iptv kan levereras på accessen. Värdet "YES" anger att iptv kan levereras, alla andra värden, inklusive tom sträng, tolkas som "NO"
            </td>
        </tr>
        <tr>
            <td>
                <code>rfof</code>
            </td>
            <td>
                Anger om RF over fiber kan levereras på accessen. Värdet "YES" anger att RFOF kan levereras, alla andra värden, inklusive tom sträng, tolkas som "NO"
            </td>
        </tr>
    </tbody>
</table>
