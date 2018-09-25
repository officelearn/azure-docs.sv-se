---
title: Härled kolumner med exempel omvandling med hjälp av Azure Machine Learning Workbench
description: Referensdokument för transformeringen Härled kolumn efter exempel
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 311fd39792274ef01b1b03fdf8252eb7ac93c922
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978870"
---
# <a name="derive-column-by-example-transformation"></a>Härled kolumner med exempel transformering

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Den **Härled kolumner med exempel** transformeringen kan du skapa ett derivat av en eller flera befintliga kolumner med exempel anges av användaren på härledda resultatet. Härledda kan vara valfri kombination av stöds sträng, datum och taltransformeringar. 

Följande sträng, datum och antal transformationer stöds:

**Strängomvandlingar:** 

Delsträngen inklusive intelligenta extrahering av tal och datum, sammanfoga fallet manipulering mappning konstantvärden.

**Datumtransformeringar:** 

Datumformatet ändras och extrahera datumets delar mappning vid tid lagerplatser.

Datum-transformationer är ganska Allmänt med några viktiga begränsningar:
* Tidszoner stöds inte.
* Vissa vanliga format som inte stöds:
    * ISO 8601 vecka på året format (till exempel ”2009-W53-7”) 
    * UNIX epoktid.
* Alla format är skiftlägeskänsliga (särskilt ”4 am” identifieras inte som en tid även om ”4 AM”).

**Taltransformeringar:** 

Avrunda Floor, tak, Datagruppering, fylla ut med nollor eller mellanslag, Division eller multiplikation med 1000.

**Sammansatta omvandlingar:** 

En kombination av sträng, nummer eller Datumtransformeringar.

## <a name="how-to-use-this-transformation"></a>Hur du använder den här omvandlingen

Följ dessa steg om du vill utföra den här transformeringen:
1. Välj en eller flera kolumner som du vill erhålla värde från. 
2. Välj **Härled kolumner med exempel** från den **omvandlar** menyn. Eller högerklicka på rubriken för någon av de markerade kolumnerna och välj **Härled kolumner med exempel** på snabbmenyn. Omvandla redigeringsprogrammet öppnas och en ny kolumn läggs bredvid den högra mest markerade kolumnen. Valda kolumner kan identifieras av kryssrutorna i en kolumnrubrik. Tillägg och borttagning av kolumner från markeringen kan göras med hjälp av kryssrutorna i en kolumnrubrik.
3. Skriv ett exempel på den *utdata* mot en rad och trycker på Ange. Arbetsstationen analyserar nu indatakolumnen samt från resultatet att syntetisera ett program som kan omvandla de angivna indata till utdata. Det syntetiserade programmet körs mot alla rader i rutnätet. Tvetydig och komplicerade fall kan flera exempel behövas. Flera exempel kan anges på olika sätt beroende på om du är i grundläggande eller Avancerat läge.
4. Granska utdata och klicka på **OK** att acceptera omvandlingen.

### <a name="transform-editor-basic-mode"></a>Omvandla Redigerare: grundläggande läge

Grundläggande läge tillhandahåller en infogad redigering i rutnätet. Du kan ge exempel på utdata genom att gå till cellen intressanta och att skriva värdet. 

Arbetsstationen analyserar data och försöker identifiera gränsfall som bör granskas av användaren. När data analyseras **analysera Data** visas i rubriken för transformera-redigeraren. En analysen är klar, antingen **nr förslag** eller **granska nästa föreslagna rad** visas i rubriken. Du kan navigera genom gränsfall genom att klicka på **granska nästa föreslagna rad**. Om värdet är felaktigt för en rad, bör du ange det korrekta värdet som ytterligare exempel. 

### <a name="transform-editor-advanced-mode"></a>Omvandla Redigerare: Avancerat läge

Avancerat läge tillhandahåller en rikare upplevelse för härledning kolumner med exempel. Alla exempel visas på samma ställe. Du kan också granska alla gränsfall på en plats genom att klicka på **Visa exempel**. 

I Avancerat läge kan du lägga till rader som en exempel-rad genom att dubbelklicka på rad i rutnätet. En rad kopieras som en exempel-rad, du kan också redigera data i källkolumner att göra en syntetisk exempel. Då kan du lägga till fall som inte är för närvarande finns i exempeldata.

Användare kan växla mellan den **standardläge** och **Avancerat läge** genom att klicka på länkarna i Redigeraren för att omvandla.

### <a name="transform-editor-send-feedback"></a>Omvandla Redigerare: skicka Feedback

När du klickar på den **skicka feedback** länken öppnas den **Feedback** dialogen med kommentarrutan innehåller exempel användaren har angetts. Användaren bör granska innehållet i kommentarrutan och ge mer information som hjälper oss att förstå problemet. Om användaren inte vill dela data med Microsoft användaren bör ta bort förifyllda exempeldata innan du klickar på den **skicka Feedback** knappen. 

### <a name="editing-existing-transformation"></a>Redigera befintliga omvandling

En användare kan redigera en befintlig **härleda av Kolumnexempel** omvandla genom att välja **redigera** alternativ i steget omvandling. När du klickar på **redigera** öppnas redigeraren transformera i **Avancerat läge**, och alla exempel som angavs under skapandet av transformeringen visas.

## <a name="examples-of-string-transformations-by-example"></a>Exempel på sträng transformationer med exempel


>[!NOTE] 
>Värdena i **fetstil** representerar exemplen som har angetts för att slutföra omvandlingen i datauppsättningen visas.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. Extrahera filnamn från sökvägar

Antal exempel som krävs för det här fallet: 2

|Indata|Resultat|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.PY|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|RGB.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.PY|2to3.PY|
|C:\Python35\Tools\Scripts\analyze_dxp.py|**analyze_dxp.PY**|
|C:\Python35\Tools\Scripts\byext.PY|byext.PY|
|C:\Python35\Tools\Scripts\byteyears.PY|byteyears.PY|
|C:\Python35\Tools\Scripts\checkappend.PY|checkappend.PY|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. Case manipulering under extrahering av sträng

Antal exempel som krävs för det här fallet: 3

|Indata|Resultat|
|:-----|:-----|
|RENAR CT & DEAD mål.  NY HANNOVER; Station 332; 2015-12-10 \@ 17:10:52.|**Ny Hannover**|
|BRIAR sökväg & WHITEMARSH LN;  HATFIELD TOWNSHIP; Station 345; 2015-12-10 \@ 17:29:21.|Hatfield Township|
|HAWS PARA; NORRISTOWN; 2015-12-10 \@ 14:39:21-station: STA27;|**Norristown**|
|AIRY ST & RAPS STA;  NORRISTOWN; Station 308A; 2015-12-10 \@ 16:47:36.|**Norristown**|
|KÖRSBÄRSTRÄ CT & DEAD mål.  LÄGRE POTTSGROVE; Station 329; 2015-12-10 \@ 16:56:52.|Lägre Pottsgrove|
|CANNON para & W-9 ST;  LANSDALE; Station 345; 2015-12-10 \@ 15:39:04;|Lansdale|
|LAUREL para & OAKDALE ARA;  HORSHAM; Station 352; 2015-12-10 \@ 16:46:48;|Horsham|
|COLLEGEVILLE RD & LYWISKI RD;  SKIPPACK; Station 336; 2015-12-10 \@ 16:17:05;|Skippack|
|MAIN ST & gamla SUMNEYTOWN PIKE;  LÄGRE SALFORD; Station 344; 2015-12-10 \@ 16:51:42;|Lägre Salford|
|BLUEROUTE &AMP; RAMP I476 NB TILL KEMISKA RD; PLYMOUTH; 2015-12-10 \@ 17:35:41;|Plymouth|
|RT202 PKWY &AMP; KNAPP RD; MONTGOMERY; 2015-12-10 \@ 17:33:50;|Montgomery|
|ARTEN RD &AMP; COLWELL LN; PLYMOUTH; 2015-12-10 \@ 16:32:10.|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. Datumformat manipulering under extrahering av sträng

Antal exempel som krävs för det här fallet: 1

|Indata|Resultat|
|:-----|:-----|
|MONTGOMERY para & WOODSIDE RD;  LÄGRE MERION; Station 313; 2015-12-11 \@ 04:11:35.|**Den 12 november 2015 4 AM**|
|DREYCOTT LN & W LANCASTER ARA;  LÄGRE MERION; Station 313; 2015-12-11 \@ 01:29:52.|Den 12 november 2015 01: 00|
|E LEVERING FABRIKEN RD &AMP; CONSHOHOCKEN TILLSTÅND RD; LÄGRE MERION; 2015-12-11 \@ 07:29:58;|Den 12 november 2015 07.00 på|
|PENN VALLEY RD & MANOR RD;  LÄGRE MERION; Station 313; 2015-12-10 \@ 20:53:30.|12 oktober 2015 20: 00|
|BELMONT PARA &AMP; OVERHILL RD; LÄGRE MERION; 2015-12-10 \@ 23:02:27;|12 oktober 2015 23: 00|
|W MONTGOMERY PARA &AMP; PENNSWOOD RD; LÄGRE MERION; 2015-12-10 \@ 19:25:22.|12 oktober 2015 19: 00|
|ROSEMONT para & DEAD mål.  LÄGRE MERION; Station 313; 2015-12-10 \@ 18:43:07;|12 oktober 2015 18: 00|
|AVIGNON DR & DEAD mål. LÄGRE MERION; 2015-12-10 \@ 20:01:29-station: STA24;|12 oktober 2015 20: 00|

### <a name="s4-concatenating-strings"></a>S4. Sammanfoga strängar

Antal exempel som krävs för det här fallet: 1

>[!NOTE] 
>I det här exemplet, specialtecken · representerar blanksteg i utdatakolumnen.

|Förnamn|Mellannamn|Efternamn|Resultat|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda·· Lohmann|
|Claudio|A|Huvudet|**Claudio·A·Chew**|
|Sarah Jane|S|Smith|Sarah Jane· S· Smith|
|Brandi||Blumenthal|Brandi·· Blumenthal|
|Jesusita|R|Resa|Jesusita· R· Resa|
|Hermina||Hults|Hermina·· Hults|
|Anne Marie|W|Jones|Anne Marie· W· Jones|
|Rico||Ropp|Rico·· Ropp|
|Lauren maj||Fullmer|Lauren May·· Fullmer|
|Marc|T|Maine|Marc· T· Maine|
|Angie||Adelman|Angie··Adelman|
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
|Sarah Jane Smith|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita resa|J.J.|
|Hermina Hults|H.H.|
|Anne Marie Jones|A.J.|
|Rico Ropp|R.R.|
|Lauren maj Fullmer|L.F.|
|Marc Maine|M.M.|
|Angie Adelman|A.A.|
|John Paul Smith|**J.S.**|
|Låt Staller|S.S.|
|Jill Jefferies|J.J.|
|Ruby-respittid Simmons|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. Mappningen konstanta värden

Antal exempel som krävs för det här fallet: 3

|Administrativa kön|Resultat|
|:-----|:-----:|
|Man|**0**|
|Kvinna|**1**|
|Okänt|**2**|
|Kvinna|1|
|Kvinna|1|
|Man|0|
|Okänt|2|
|Man|0|
|Kvinna|1|

## <a name="examples-of-number-transformations-by-example"></a>Exempel på taltransformeringar efter exempel

>[!NOTE] 
>Värdena i **fetstil** representerar exemplen som har angetts för att slutföra omvandlingen i datauppsättningen visas.


### <a name="n1-rounding-to-nearest-10"></a>N1. Avrundat till närmaste 10

Antal exempel som krävs för det här fallet: 1

|Indata|Resultat|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. Avrundat nedåt till närmaste 10

Antal exempel som krävs för det här fallet: 2

|Indata|Resultat|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. Avrundat till närmaste 0,05

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

## <a name="examples-of-date-transformations-by-example"></a>Exempel på datumtransformeringar efter exempel

>[!NOTE] 
>Värdena i **fetstil** representerar exemplen som har angetts för att slutföra omvandlingen i datauppsättningen visas.


### <a name="d1-extracting-date-parts"></a>D1. Extrahera datumets delar

Dessa datum-delar extraherades med olika av exempel transformationer på samma datauppsättning. Fet strängar representerar exemplen som har beviljats i sin respektive omvandling.

|DateTime|Veckodag|Date|Månad|År|Timme|Minut|Sekund|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**Fre**|**31**|**Jan**|**2031**|**5**|**54**|**18**|
|17-Jan-1990 13:32:01|Ons|17|Jan|1990|13|32|01|
|14-Feb-2034 05:36:07|Tis|14|Feb|2034|5|36|07|
|14-Mar-2002 13:16:16|Tor|14|Mar|2002|13|16|16|
|21-Jan-1985 05:44:43|Mån|21|Jan|1985|5|44|**43**|
|16-Aug-1985 01:11:56|Fre|16|Aug|1985|1|11|56|
|20-Dec-2033 18:36:29|Tis|20|Dec|2033|18|36|29|
|16-Jul-1984 10:21:59|Mån|16|Jul|1984|10|21|59|
|13 januari 2038 10:59:36|Ons|13|Jan|2038|10|59|36|
|14 – augusti-1982 15:13:54|Lör|14|Aug|1982|15|13|54|
|22-Nov-2030 08:18:08|Fre|22|Nov|2030|8|18|08|
|21-oktober – 1997 08:42:58|Tis|21|Okt|1997|8|42|58|
|28-Nov – 2006 14:19:15|Tis|28|Nov|2006|14|19|15|
|29-Apr-2031 04:59:45|Tis|29|Apr|2031|4|59|45|
|29-Jan-2032 02:38:36|Tor|29|Jan|2032|2|38|36|
|11 – maj-2028 15:31:52|Tor|11|maj|2028|15|31|52|
|15-Jul-1977 12:45:39|Fre|15|Jul|1977|12|45|39|
|27-Jan-2029 05:55:41|Lör|27|Jan|2029|5|55|41|
|03-Mar-2024 10:17:49|Sön|3|Mar|2024|10|17|49|
|2010-14-Apr 00:23:13|Ons|14|Apr|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. Formatering av datum

Dessa datum formattings har utförts med hjälp av olika av exempel transformationer på samma datauppsättning. Fet strängar representerar exemplen som har beviljats i sin respektive omvandling.

|DateTime|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**1/31/2031**|**Fredag den 31 januari 2031**|**01312031 5:54**|**31/1/2031 5:54 AM**|**Q1 2031**|
|17-Jan-1990 13:32:01|1/17/1990|Onsdag 17 januari 1990|01171990 13:32|17/1/1990 1:32 PM|Q1 1990|
|14-Feb-2034 05:36:07|2/14/2034|Tisdagen den 14 februari 2034|02142034 5:36|14/2/2034 05:36:00|Q1 2034
|14-Mar-2002 13:16:16|3/14/2002|Torsdag den 14 mars 2002|03142002 13:16|2002/3/14 1:16 PM|Q1 2002
|21-Jan-1985 05:44:43|1/21/1985|Måndagen den 21 januari 1985|01211985 5:44|21/1/1985 05:44:00|Q1 1985
|16-Aug-1985 01:11:56|8/16/1985|Fredag den 16 augusti 1985|08161985 1:11|16/8/1985 01:11:00|Q3 1985
|20-Dec-2033 18:36:29|12/20/2033|Tisdagen den 20 December 2033|12202033 18:36|20/12/2033 18:36:00|KVARTAL 4 2033
|16-Jul-1984 10:21:59|7/16/1984|Måndag 16 juli 1984|07161984 10:21|16/7/1984 KL. 10:21|Q3 1984
|13 januari 2038 10:59:36|1/13/2038|Onsdag 13 januari 2038|01132038 10:59|2038/1/13 KL. 10:59|Q1 2038
|14 – augusti-1982 15:13:54|8/14/1982|Lördag 14 augusti 1982|08141982 15:13|14/8/1982 15:13:00|Q3 1982
|22-Nov-2030 08:18:08|11/22/2030|Fredag den 22 November 2030|11222030 8:18|22/11/2030 KL. 8:18|KVARTAL 4 2030
|21-oktober – 1997 08:42:58|10/21/1997|Tisdagen den 21 oktober 1997|10211997 8:42|21/10/1997 KL. 8:42|KVARTAL 4 1997
|28-Nov – 2006 14:19:15|11/28/2006|Tisdagen den 28 November 2006|11282006 14:19|28/11/2006 14:19:00|KVARTAL 4 2006
|29-Apr-2031 04:59:45|4/29/2031|Tisdagen den 29 April 2031|04292031 4:59|29/4/2031 4:59 AM|Q2 2031
|29-Jan-2032 02:38:36|1/29/2032|Torsdag den 29 januari 2032|01292032 2:38|29/1/2032 2:38 AM|Q1 2032
|11 – maj-2028 15:31:52|5/11/2028|Torsdag den 11 maj 2028|05112028 15:31|11/5/2028 3:31 PM|Q2 2028
|15-Jul-1977 12:45:39|7/15/1977|Fredag 15 juli 1977|07151977 12:45|15/7/1977 12:45 PM|Q3 1977
|27-Jan-2029 05:55:41|1/27/2029|Lördag 27 januari 2029|01272029 5:55|27/1/2029 05:55:00|Q1 2029
|03-Mar-2024 10:17:49|3/3/2024|Söndag 3 mars 2024|03032024 10:17|3/3/2024 KL. 10:17|Q1 2024
|2010-14-Apr 00:23:13|4/14/2010|Onsdag 14 April 2010|04142010 0:23|14/4/2010 KL. 12:23|Q2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. Mappning av tid till tidsperioder

Dessa datum och tid till mappningar för perioden har utförts med hjälp av olika av exempel transformationer på samma datauppsättning. Fet strängar representerar exemplen som har beviljats i sin respektive omvandling.

|DateTime|Period(seconds)|Period(minutes)|Period (två timmar)|Period (30 minuter)|
|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**0-20**|**45-60**|**5AM-7AM**|**5:30-6:00**|
|17-Jan-1990 13:32:01|**0-20**|30-45|1PM-3PM|13:30-14:00|
|14-Feb-2034 05:36:07|0-20|30-45|05: 00 - 7 AM|5:30-6:00|
|14-Mar-2002 13:16:16|0-20|15-30|1PM-3PM|13:00-13:30|
|21-Jan-1985 05:44:43|40-60|30-45|05: 00 - 7 AM|5:30-6:00|
|16-Aug-1985 01:11:56|40-60|0-15|01: 00 - 3: 00|1:00-1:30|
|20-Dec-2033 18:36:29|20-40|30-45|5PM-7PM|18:30-19:00|
|16-Jul-1984 10:21:59|40-60|15-30|9: 00 - 11: 00|10:00-10:30|
|13 januari 2038 10:59:36|20-40|45-60|9: 00 - 11: 00|10:30-11:00|
|14 – augusti-1982 15:13:54|40-60|0-15|3PM-5PM|15:00-15:30|
|22-Nov-2030 08:18:08|0-20|15-30|7 AM - 9: 00|8:00-8:30|
|21-oktober – 1997 08:42:58|40-60|30-45|7 AM - 9: 00|8:30-9:00|
|28-Nov – 2006 14:19:15|0-20|15-30|1PM-3PM|14:00-14:30|
|29-Apr-2031 04:59:45|40-60|45-60|3: 00 - 05: 00|4:30-5:00|
|29-Jan-2032 02:38:36|20-40|30-45|01: 00 - 3: 00|2:30-3:00|
|11 – maj-2028 15:31:52|40-60|30-45|3PM-5PM|15:30-16:00|
|15-Jul-1977 12:45:39|20-40|45-60|11AM-1PM|12:30-13:00|
|27-Jan-2029 05:55:41|40-60|45-60|05: 00 - 7 AM|5:30-6:00|
|03-Mar-2024 10:17:49|40-60|15-30|9: 00 - 11: 00|10:00-10:30|
|2010-14-Apr 00:23:13|0-20|15-30|23: 00 - 01: 00|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>Exempel på sammansatta transformationer med exempel

|tripduration|StartTime|startstation-ID|Latitud för startstation|longitud|UserType|Kolumn|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|Prenumerant|**En prenumerant plockas upp en cykel från station 107, lat/lång (42.363,-71.088) 08 Jan 2016 cirka 4 kl. Resans varaktighet var 61 minuter**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|Kund|En kund plockas upp en cykel från station 74, lat/lång (42.373,-71.119), på 17 Jan 2016 cirka 9: 00. Resans varaktighet var 61 minuter|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|Prenumerant|En prenumerant plockas upp en cykel från station 176, lat/lång (42.387,-71.119), 25 januari 2016 kl cirka 8. Resans varaktighet var 62 minuter|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|Prenumerant|En prenumerant plockas upp en cykel från station 107, lat/lång (42.363,-71.088) 08 Jan 2016 kl. cirka 10. Resans varaktighet var 63 minuter|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|Prenumerant|En prenumerant plockas upp en cykel från station 68, lat/lång (42.365,-71.103), 15 Jan 2016 cirka 19: 00. Resans varaktighet har 64 minuter|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|Prenumerant|En prenumerant plockas upp en cykel från station 115, lat/lång (42.388,-71.119) på den 22 januari 2016 på cirka 18: 00. Resans varaktighet har 64 minuter|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|Prenumerant|En prenumerant plockas upp en cykel från station 178, lat/lång (42.360,-71.101), den 18 januari 2016 cirka 9: 00. Resans varaktighet var 68 minuter|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|Prenumerant|En prenumerant plockas upp en cykel från station 176, lat/lång (42.387,-71.119), 14 Jan 2016 kl cirka 8. Resans varaktighet var 69 minuter|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|Prenumerant|En prenumerant plockas upp en cykel från station 141, lat/lång (42.364,-71.082) på 13 januari 2016 på cirka 10 PM. Resans varaktighet var 69 minuter|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|Prenumerant|En prenumerant plockas upp en cykel från station 176, lat/lång (42.387,-71.119), 15 Jan 2016 kl cirka 8. Resans varaktighet var 69 minuter|


## <a name="technical-notes"></a>Teknisk information

### <a name="conditional-transformations"></a>Villkorlig omvandlingar
Att i vissa fall kan en enda omvandling går inte hitta som uppfyller de angivna exemplen. I sådana fall kan försöker Härled kolumner med exempel transformera gruppera indata baserat på vissa mönster och lär dig vid omvandling av separat för varje grupp. Vi kallar detta **villkorlig omvandling**. **Villkorlig omvandling** görs endast för transformationer med en kolumn som innehåller indata. 

### <a name="reference"></a>Referens
Mer information om omvandlingen sträng med exempel teknik finns i [denna publikation](https://www.microsoft.com/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/).
