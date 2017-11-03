---
title: "Härledd kolumn genom exempel omvandling med Azure Machine Learning arbetsstationen"
description: "Referensdokumentet för transformeringen härledd kolumn efter exempel"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: a02f5e827345a1d28f01d691e1b6fbccfc03ae8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="derive-column-by-example-transformation"></a>Härledd kolumn av exempel omvandling

Den **härledd kolumn efter exempel** transformeringen kan du skapa ett derivat av en eller flera kolumner med hjälp av användarens angivna exempel på härledda resultatet. Den härledda verk kan vara valfri kombination av strängen, datum och antalet transformationer. 

Följande sträng, datum och nummer transformationer stöds:

**Strängomvandlingar:** 

Delsträngen inklusive intelligent extrahering av nummer och datum, sammanfogning fallet manipulering mappning konstantvärden.

**Datumomvandlingar:** 

Datumformatet ändra, extrahera Datumdelar mappning av tid till tid lagerplatser.

Datum-transformationer är ganska Allmänt med några viktiga begränsningar:
* Tidszoner stöds inte.
* Några vanliga format som inte stöds:
    * ISO 8601 vecka på året format (till exempel ”2009-W53-7”) 
    * UNIX epok tid.
* Alla format är skiftlägeskänsliga (särskilt ”4 am” identifieras inte som en tid även om ”04: 00”).

**Antalet omvandlingar:** 

Avrunda Floor, tak, Diskretisering, fylla ut med nollor eller utrymme, avdelning eller multiplikation med 1000.

**Sammansatta omvandlingar:** 

En kombination av sträng, siffra eller datum transformationer.

## <a name="how-to-use-this-transformation"></a>Hur du använder den här omvandlingen

Följ dessa steg om du vill utföra den här transformeringen:
1. Välj en eller flera kolumner som du vill erhålla värde från. 
2. Välj **härledd kolumn efter exempel** från den **transformerar** menyn. Eller högerklicka på rubriken för någon av de markerade kolumnerna och välj **härledd kolumn efter exempel** på snabbmenyn. Transformera redigeraren öppnas och lägga till en ny kolumn bredvid den högra mest markerade kolumnen. Valda kolumner kan identifieras av kryssrutorna i en kolumnrubrik. Tillägg och borttagning av kolumner från markeringen kan göras med hjälp av kryssrutorna i en kolumnrubrik.
3. Skriv ett exempel på den *utdata* mot en rad och trycker på Ange. Nu analyserar arbetsstationen indatakolumnen samt angivna utdata till syntetisera ett program som kan omvandla de angivna indata till utdata. Syntetiskt programmet körs mot alla rader i rutnätet. Flera exempel kan behövas för tvetydig och komplicerade fallen. Flera exempel kan anges på olika sätt beroende på om du är i grundläggande eller Avancerat läge.
4. Granska utdata och klicka på **OK** att acceptera transformeringen.

### <a name="transform-editor-basic-mode"></a>Transformera editor: grundläggande läge

Basic-läge tillhandahåller en infogad redigeringsmiljön i rutnätet. Du kan ge exempel på utdata genom att gå till cellen intressanta och att skriva värdet. 

Arbetsstationen analyserar data och försöker identifiera edge fall som bör granskas av användaren. När data analyseras **analysera Data** visas i rubriken för Redigeraren för transformeringen. En analysen är klar, antingen **Nej förslag** eller **granska nästa föreslagna rad** visas i rubriken. Du kan bläddra i edge-fall genom att klicka på **granska nästa föreslagna rad**. Om värdet är felaktigt för en rad ska du ange rätt värde ytterligare exempel. 

### <a name="transform-editor-advanced-mode"></a>Transformera editor: Avancerat läge

Avancerat läge ger en bättre upplevelse för härledda kolumner efter exempel. Alla exempel som visas på samma ställe. Du kan också granska alla edge fall på en plats genom att klicka på **Visa föreslagna exempel**. 

I Avancerat läge du kan lägga till en rad som ett exempel rad genom att dubbelklicka på raden i rutnätet. En en rad kopieras som en exempel-rad kan du också redigera data i källkolumner att göra en syntetisk exempel. Då kan du lägga till fall som inte är för närvarande finns i exempeldata.

Användaren kan växla mellan den **grundläggande läget** och **Avancerat läge** genom att klicka på länkarna i Redigeraren för transformeringen.

### <a name="editing-existing-transformation"></a>Redigera befintliga omvandling

En användare kan redigera en befintlig **härledd kolumn av exempel** transformera genom att välja **redigera** alternativ för steget omvandling. Klicka på **redigera** öppnar Redigeraren transformeringen i **Avancerat läge**, och alla exempel som angavs under skapandet av transformeringen visas.

## <a name="examples-of-string-transformations-by-example"></a>Exempel på Strängomvandlingar efter exempel


>[!NOTE] 
>Värdena i **fetstil** representerar exemplen som angavs för att kunna slutföra omvandling i datauppsättningen visas.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. Extraherar filnamn från sökvägar

Antal exempel som krävs för det här fallet: 2

|Indata|Resultat|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.PY|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|RGB.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.PY|2to3.PY|
|C:\Python35\Tools\Scripts\analyze_dxp.PY|**analyze_dxp.PY**|
|C:\Python35\Tools\Scripts\byext.PY|byext.PY|
|C:\Python35\Tools\Scripts\byteyears.PY|byteyears.PY|
|C:\Python35\Tools\Scripts\checkappend.PY|checkappend.PY|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. Case manipulering under Extraheringen av sträng

Antal exempel som krävs för det här fallet: 3

|Indata|Resultat|
|:-----|:-----|
|RENAR Datafält & DEAD avslutas.  NYA HANNOVER; Station 332; 2015-12-10 @ 17:10:52.|**Nya Hannover**|
|BRIAR sökväg & WHITEMARSH LN;  HATFIELD TOWNSHIP; Station 345; 2015-12-10 @ 17:29:21.|Hatfield Township|
|HAWS AVE; NORRISTOWN; 2015-12-10 @ 14:39:21-station: STA27;|**Norristown**|
|AIRY ST & ST RAPS;  NORRISTOWN; Station 308A; 2015-12-10 @ 16:47:36.|**Norristown**|
|KÖRSBÄRSTRÄ Datafält & DEAD avslutas.  LÄGRE POTTSGROVE; Station 329; 2015-12-10 @ 16:56:52.|Lägre Pottsgrove|
|CANNON AVE & W 9: e ST;  LANSDALE; Station 345; 2015-12-10 @ 15:39:04;|Lansdale|
|LAGER AVE & OAKDALE ARA;  HORSHAM; Station 352; 2015-12-10 @ 16:46:48;|Horsham|
|COLLEGEVILLE RD & LYWISKI RD;  SKIPPACK; Station 336; 2015-12-10 @ 16:17:05;|Skippack|
|MAIN ST & gamla SUMNEYTOWN GÄDDA;  LÄGRE SALFORD; Station 344; 2015-12-10 @ 16:51:42;|Lägre Salford|
|BLUEROUTE & RAMP I476 NB TILL KEMISKA RD; PLYMOUTH; 2015-12-10 @ 17:35:41;|Plymouth|
|RT202 PKWY & KNAPP RD; MONTGOMERY; 2015-12-10 @ 17:33:50;|Montgomery|
|ARTEN RD & COLWELL LN; PLYMOUTH; 2015-12-10 @ 16:32:10.|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. Formatet för datum manipulering under Extraheringen av sträng

Antal exempel som krävs för det här fallet: 1

|Indata|Resultat|
|:-----|:-----|
|MONTGOMERY AVE & WOODSIDE RD;  LÄGRE MERION; Station 313; 2015-12-11 @ 04:11:35.|**12 Nov 2015 04: 00**|
|DREYCOTT LN & W LANCASTER ARA;  LÄGRE MERION; Station 313; 2015-12-11 @ 01:29:52.|12 Nov 2015 1 AM|
|E LEVERING FABRIKEN RD & CONSHOHOCKEN TILLSTÅND RD; LÄGRE MERION; 2015-12-11 @ 07:29:58;|12 Nov 2015 7 AM|
|SANDBERG DAL RD & MANOR RD;  LÄGRE MERION; Station 313; 2015-12-10 @ 20:53:30.|12 dessa 2015 20: 00|
|BELMONT AVE & OVERHILL RD; LÄGRE MERION; 2015-12-10 @ 23:02:27;|12 dessa 2015 kl|
|W MONTGOMERY AVE & PENNSWOOD RD; LÄGRE MERION; 2015-12-10 @ 19:25:22.|12 dessa 2015 kl|
|ROSEMONT AVE & DEAD avslutas.  LÄGRE MERION; Station 313; 2015-12-10 @ 18:43:07;|12 dessa 2015 18: 00|
|AVIGNON DR & DEAD avslutas. LÄGRE MERION; 2015-12-10 @ 20:01:29-station: STA24;|12 dessa 2015 20: 00|

### <a name="s4-concatenating-strings"></a>S4. Sammanfoga strängar

Antal exempel som krävs för det här fallet: 1

>[!NOTE] 
>I det här exemplet specialtecken · representerar blanksteg i kolumnen utdata.

|Förnamn|Mellannamn|Efternamn|Resultat|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda·· Lohmann|
|Claudio|A|Huvudet|**Claudio· A· Huvudet**|
|Sara Jane|S|Smith|Sara Jane· S· Smith|
|Brandi||Blumenthal|Brandi·· Blumenthal|
|Jesusita|R|Resa|Jesusita· R· Resa|
|Hermina||Hults|Hermina·· Hults|
|Anna Marie|W|Karlsson|Anna Marie· W· Karlsson|
|Rico||Ropp|Rico·· Ropp|
|Lauren kan||Fullmer|Lauren May·· Fullmer|
|Pressrester|T|Maine|Marc· T· Maine|
|Angie||Adelman|Angie·· Adelman|
|John Paul||Smith|John Paul·· Smith|
|Låt|W|Staller|Song· W· Staller|
|Jill||Jefferies|Jill·· Jefferies|
|Ruby-Respit|M|Simmons|Ruby-Grace· M· Simmons|

### <a name="s5-generating-initials"></a>S5. Generera initialer

Antal exempel som krävs för det här fallet: 2

|Fullständigt namn|Resultat|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio huvudet|C.C.|
|Sara Jane Smith|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita resa|J.J.|
|Hermina Hults|H.H.|
|Anna Marie Karlsson|A.J.|
|Rico Ropp|R.R.|
|Lauren kan Fullmer|L.F.|
|Pressrester Maine|M.M.|
|Angie Adelman|A.A.|
|Paul John Smith|**J.S.**|
|Låt Staller|S.S.|
|Jill Jefferies|J.J.|
|Ruby Grace Simmons|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. Mappning konstanta värden

Antal exempel som krävs för det här fallet: 3

|Administrativa kön|Resultat|
|:-----|:-----:|
|man|**0**|
|hona|**1**|
|Okänd|**2**|
|hona|1|
|hona|1|
|man|0|
|Okänd|2|
|man|0|
|hona|1|

## <a name="examples-of-number-transformations-by-example"></a>Exempel på antalet omvandlingar efter exempel

>[!NOTE] 
>Värdena i **fetstil** representerar exemplen som angavs för att kunna slutföra omvandling i datauppsättningen visas.


### <a name="n1-rounding-to-nearest-10"></a>N1. Avrundas till närmaste 10

Antal exempel som krävs för det här fallet: 1

|Indata|Resultat|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. Avrundas nedåt till närmaste 10

Antal exempel som krävs för det här fallet: 2

|Indata|Resultat|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. Avrundas till närmaste 0,05

Antal exempel som krävs för det här fallet: 2

|Indata|Resultat|
|-----:|-----:|
|-75.5812935|**-75.60**|
|-75.2646799|-75.25|
|-75.3519752|-75.35|
|-75.343513|**-75.35**|
|-75.6033497|-75.60|
|-75.283245|-75.30|

### <a name="n4-binning"></a>N4. Diskretisering

Antal exempel som krävs för det här fallet: 1

|Indata|Resultat|
|-----:|:-----:|
|20.16|**20-25**|
|14.32|10-15|
|5.44|5-10|
|3.84|0-5|
|3.73|0-5|
|7.36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. Skalning av 1000

Antal exempel som krävs för det här fallet: 1

|Indata|Resultat|
|-----:|-----:|
|-243|**-243000**|
|-12.5|-12500|
|-2345.23292|-2345232.92|
|-1202.3433|-1202343.3|
|1202.3433|1202343.3|

### <a name="n6-padding"></a>N6. Utfyllnad

Antal exempel som krävs för det här fallet: 1

|Kod|Resultat|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>Exempel på Datumomvandlingar efter exempel

>[!NOTE] 
>Värdena i **fetstil** representerar exemplen som angavs för att kunna slutföra omvandling i datauppsättningen visas.


### <a name="d1-extracting-date-parts"></a>D1. Extrahera datumdelar

Dessa datumdelar extraherades med olika av exempel omformningar på samma datauppsättning. Fetstil strängar representerar exemplen som gavs i sina respektive omvandling.

|Datum och tid|veckodag|Date|Månad|År|Timme|Minut|Sekund|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**Fre**|**31**|**Jan**|**2031**|**5**|**54**|**18**|
|17-Jan-1990 13:32:01|Ons|17|Jan|1990|13|32|01|
|14-Feb-2034 05:36:07|TIS|14|Feb|2034|5|36|07|
|14-Mar-2002 13:16:16|Tor|14|Mar|2002|13|16|16|
|21-Jan-1985 05:44:43|Måndag|21|Jan|1985|5|44|**43**|
|16-augusti-1985 01:11:56|Fre|16|Augusti|1985|1|11|56|
|20-Dec-2033 18:36:29|TIS|20|Dec|2033|18|36|29|
|16-Jul-1984 10:21:59|Måndag|16|Jul|1984|10|21|59|
|13 januari 2038 10:59:36|Ons|13|Jan|2038|10|59|36|
|14-augusti-1982 15:13:54|SAT|14|Augusti|1982|15|13|54|
|22-Nov-2030 08:18:08|Fre|22|Nov|2030|8|18|08|
|21-dessa – 1997 08:42:58|TIS|21|Dessa|1997|8|42|58|
|28-Nov-2006 14:19:15|TIS|28|Nov|2006|14|19|15|
|29-april-2031 04:59:45|TIS|29|April|2031|4|59|45|
|29-Jan-2032 02:38:36|Tor|29|Jan|2032|2|38|36|
|11-maj-2028 15:31:52|Tor|11|Kan|2028|15|31|52|
|15-Jul-1977 12:45:39|Fre|15|Jul|1977|12|45|39|
|27-Jan-2029 05:55:41|SAT|27|Jan|2029|5|55|41|
|2024-03-Mar 10:17:49|Sun|3|Mar|2024|10|17|49|
|2010-14-april 00:23:13|Ons|14|April|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. Formatering av datum

Dessa datum formattings gjordes med olika av exempel omformningar på samma datauppsättning. Fetstil strängar representerar exemplen som gavs i sina respektive omvandling.

|Datum och tid|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**1/31/2031**|**Fredag den 31 januari 2031**|**01312031 5:54**|**31/1/2031 5:54 AM**|**F1 2031**|
|17-Jan-1990 13:32:01|1/17/1990|Onsdag 17 januari 1990|01171990 13:32|17/1/1990 1:32 PM|F1 1990|
|14-Feb-2034 05:36:07|2/14/2034|Tisdag 14 februari 2034|02142034 5:36|2034-14/2 5:36 AM|F1 2034
|14-Mar-2002 13:16:16|3/14/2002|Torsdag 14 mars 2002|03142002 13:16|2002-14-3 1:16 PM|F1 2002
|21-Jan-1985 05:44:43|1/21/1985|Måndagen den 21 januari 1985|01211985 5:44|21/1/1985 5:44 AM|F1 1985
|16-augusti-1985 01:11:56|8/16/1985|Fredag den 16 augusti 1985|08161985 1:11|16/8/1985 1:11:00|KVARTAL 3 1985
|20-Dec-2033 18:36:29|12/20/2033|Tisdag 20 December 2033|12202033 18:36|20/12/2033 6:36 PM|F4 2033
|16-Jul-1984 10:21:59|7/16/1984|Måndag 16 juli 1984|07161984 10:21|16/7/1984 10:21 AM|KVARTAL 3 1984
|13 januari 2038 10:59:36|1/13/2038|Onsdag 13 januari 2038|01132038 10:59|2038/1/13 10:59 AM|F1 2038
|14-augusti-1982 15:13:54|8/14/1982|Lördag 14 augusti 1982|08141982 15:13|1982-14/8 3:13 PM|KVARTAL 3 1982
|22-Nov-2030 08:18:08|11/22/2030|Fredag den 22 November 2030|11222030 8:18|2030-22/11 8:18:00|F4 2030
|21-dessa – 1997 08:42:58|10/21/1997|Tisdag 21 oktober 1997|10211997 8:42|21/10/1997 8:42 AM|KVARTAL 4 1997
|28-Nov-2006 14:19:15|11/28/2006|Tisdag 28 November 2006|11282006 14:19|28/11/2006 14:19:00|F4 2006
|29-april-2031 04:59:45|4/29/2031|Tisdag 29 April 2031|04292031 4:59|29/4/2031 04:59:00|K2 2031
|29-Jan-2032 02:38:36|1/29/2032|Torsdag 29 januari 2032|01292032 2:38|29/1/2032 2:38 AM|F1 2032
|11-maj-2028 15:31:52|5/11/2028|Torsdag 11 maj 2028|05112028 15:31|2028-11/5 3:31 PM|K2 2028
|15-Jul-1977 12:45:39|7/15/1977|Fredag den 15 juli 1977|07151977 12:45|15/7/1977 12:45 PM|KVARTAL 3 1977
|27-Jan-2029 05:55:41|1/27/2029|Lördag 27 januari 2029|01272029 5:55|27/1/2029 5:55 AM|F1 2029
|2024-03-Mar 10:17:49|3/3/2024|Söndag 3 mars 2024|03032024 10:17|2024-3/3 10:17 AM|F1 2024
|2010-14-april 00:23:13|4/14/2010|Onsdag 14 April 2010|04142010 0:23|2010-14-4 12:23:00|K2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. Mappning av tid tidsperioder

Dessa datum och tid till period mappningar som gjordes med olika av exempel omformningar på samma datauppsättning. Fetstil strängar representerar exemplen som gavs i sina respektive omvandling.

|Datum och tid|Period(seconds)|Period(minutes)|Tid (två timmar)|Tid (30 minuter)|
|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**0-20**|**45-60**|**5 AM - 7 AM**|**5:30-6:00**|
|17-Jan-1990 13:32:01|**0-20**|30-45|1 PM - 15: 00|13:30-14:00|
|14-Feb-2034 05:36:07|0-20|30-45|5 AM - 7 AM|5:30-6:00|
|14-Mar-2002 13:16:16|0-20|15-30|1 PM - 15: 00|13:00-13:30|
|21-Jan-1985 05:44:43|40-60|30-45|5 AM - 7 AM|5:30-6:00|
|16-augusti-1985 01:11:56|40-60|0-15|1 AM - 3 AM|1:00-1:30|
|20-Dec-2033 18:36:29|20-40|30-45|17: 00 - 7 PM|18:30-19:00|
|16-Jul-1984 10:21:59|40-60|15-30|9: 00 - 11: 00|10:00-10:30|
|13 januari 2038 10:59:36|20-40|45-60|9: 00 - 11: 00|10:30-11:00|
|14-augusti-1982 15:13:54|40-60|0-15|15: 00 – 17: 00|15:00-15:30|
|22-Nov-2030 08:18:08|0-20|15-30|7 AM - 9: 00|8:00-8:30|
|21-dessa – 1997 08:42:58|40-60|30-45|7 AM - 9: 00|8:30-9:00|
|28-Nov-2006 14:19:15|0-20|15-30|1 PM - 15: 00|14:00-14:30|
|29-april-2031 04:59:45|40-60|45-60|3 AM - 5 AM|4:30-5:00|
|29-Jan-2032 02:38:36|20-40|30-45|1 AM - 3 AM|2:30-3:00|
|11-maj-2028 15:31:52|40-60|30-45|15: 00 – 17: 00|15:30-16:00|
|15-Jul-1977 12:45:39|20-40|45-60|11: 00 - 1 PM|12:30-13:00|
|27-Jan-2029 05:55:41|40-60|45-60|5 AM - 7 AM|5:30-6:00|
|2024-03-Mar 10:17:49|40-60|15-30|9: 00 - 11: 00|10:00-10:30|
|2010-14-april 00:23:13|0-20|15-30|11 PM - 1 AM|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>Exempel på sammansatta omvandlingar efter exempel

|tripduration|StartTime|Starta station-id|Starta station latitud|Starta station longitud|UserType|Kolumn|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|Prenumeranten|**En prenumerant plockats en cykel från station 107, lat/lång (42.363,-71.088) 08 Jan 2016 cirka 4 PM. Varaktigheten för resa var 61 minuter**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|Kunden|En kund plockats en cykel från station 74, lat/lång (42.373,-71.119) 17 Jan 2016 cirka 9: 00. Varaktigheten för resa var 61 minuter|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|Prenumeranten|En prenumerant plockats en cykel från station 176, lat/lång (42.387,-71.119) 25 Jan 2016 cirka 8: 00. Varaktigheten för resa var 62 minuter|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|Prenumeranten|En prenumerant plockats en cykel från station 107, lat/lång (42.363,-71.088) 08 Jan 2016 kl cirka 10. Varaktigheten för resa var 63 minuter|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|Prenumeranten|En prenumerant plockats en cykel från station 68, lat/lång (42.365,-71.103), 15 Jan 2016 klockan cirka 7. Varaktigheten för resa var 64 minuter|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|Prenumeranten|En prenumerant plockats en cykel från station 115, lat/lång (42.388,-71.119) på 22 januari 2016 cirka 18: 00. Varaktigheten för resa var 64 minuter|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|Prenumeranten|En prenumerant plockats en cykel från station 178, lat/lång (42.360,-71.101) 18 Jan 2016 cirka 9: 00. Varaktigheten för resa var 68 minuter|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|Prenumeranten|En prenumerant plockats en cykel från station 176, lat/lång (42.387,-71.119), 14 Jan 2016 cirka 8: 00. Varaktigheten för resa var 69 minuter|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|Prenumeranten|En prenumerant plockats en cykel från station 141, lat/lång (42.364,-71.082) 13 januari 2016 klockan cirka 10. Varaktigheten för resa var 69 minuter|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|Prenumeranten|En prenumerant plockats en cykel från station 176, lat/lång (42.387,-71.119), 15 Jan 2016 cirka 8: 00. Varaktigheten för resa var 69 minuter|


## <a name="technical-notes"></a>Teknisk information

### <a name="conditional-transformations"></a>Villkorlig omvandlingar
I vissa fall kan kan en enda omvandling inte hittas som uppfyller de angivna exemplen. I sådana fall försöker härledd kolumn efter exempel transformera gruppera indata baserat på vissa mönster och Läs omvandling av separat för varje grupp. Vi kallar detta **villkorlig omvandling**. **Villkorlig omvandling** görs endast för omvandlingar med en enskild kolumn. 

### <a name="reference"></a>Referens
Mer information om omvandlingen sträng med exempel teknik finns i [publikationen](https://www.microsoft.com/en-us/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/).
