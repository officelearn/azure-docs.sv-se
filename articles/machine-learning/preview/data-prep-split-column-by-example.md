---
title: Dela upp kolumn efter exempel omvandling med Azure Machine Learning arbetsstationen
description: "Referensdokumentet för transformeringen 'Delad kolumn efter exempel'"
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
ms.openlocfilehash: 013c99045621e4651a44ab99c9f695fff6004654
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="split-column-by-example-transformation"></a>Delad kolumn av exempel omvandling
Den här transformeringen delar predictively innehållet i en kolumn i meningsfulla gränser utan indata från användaren. Dela algoritmen väljer gränserna när du har analyserat innehållet i kolumnen. Dessa gränser kan definieras av
* En fast avgränsare
* Flera valfri avgränsare som visas i synnerhet kontexter, eller,
* Datamönster eller vissa typer av enheter

Användare kan också styra delande i Avancerat läge där de kan ange avgränsarna eller som innehåller exempel på önskade dela.

Teoretiskt dela kan också att utföra åtgärder i arbetsstationen med hjälp av en serie *härledd kolumn efter exempel* transformeringar. Om det finns flera kolumner, kan som var och en av de individuellt även använder av exempel metod dock vara mycket lång tid. Förutsägande dela kan enkelt dela utan att användaren behöver ange exempel för varje kolumn. 

## <a name="how-to-perform-this-transformation"></a>Hur du utför den här omvandlingen

1. Markera den kolumn som du vill dela. 
2. Välj **delad kolumn efter exempel** från den **transformerar** menyn. Högerklicka på rubriken för den markerade kolumnen eller och välj **delad kolumn efter exempel** på snabbmenyn. Transformera redigeraren öppnas och nya kolumner läggs till bredvid den markerade kolumnen. Nu arbetsstationen analyserar indatakolumnen anger dela gränser och Syntetiserar ett program för att dela upp kolumnen som visas i rutnätet. Syntetiskt programmet körs mot alla rader i kolumnen. Avgränsare, undantas eventuella från slutresultatet.
3. Du kan klicka på den **Avancerat läge** får bättre kontroll över delade omvandling. 
4. Granska utdata och klicka på **OK** att acceptera transformeringen. 

Transformera syftet är att ge samma antal gällande kolumner för alla rader. Om en rad inte kan delas upp på gränserna som definieras den genererar *null* för alla kolumner som standard. Detta beteende kan ändras i den **Avancerat läge**.

### <a name="transform-editor-advanced-mode"></a>Transformera editor: Avancerat läge
**Avancerat läge** ger en bättre upplevelse för att dela kolumner. 

Att välja **hålla avgränsare kolumner** innehåller avgränsare i slutresultatet. Avgränsare ingår inte som standard.

Ange **avgränsare** åsidosätter automatisk avgränsare markeringen logik. Flera avgränsare, en på varje rad kan anges som **avgränsare**. Alla de tecken som används som avgränsare för att dela kolumnen.

Ibland kan dela ett värde på definieras gränser ger olika antal kolumner än flesta andra. I sådana fall kan **fyller riktning** används för att bestämma den ordning som kolumnerna ska fyllas.

Klicka på **Visa föreslagna exempel** visar representativt rader för vilka användare ska ge ett exempel på Dela. Användaren kan klicka på den **in** pilen till höger om raden föreslagna att flytta upp raden som exempel. 

Användaren kan **ta bort kolumnen** eller **infoga nya kolumner** genom att högerklicka på rubriken för den **exempel tabellen**.

Användaren kan kopiera och klistra in värden från en Cell till en annan för att ge ett exempel på Dela.

Användaren kan växla mellan den **grundläggande läget** och **Avancerat läge** genom att klicka på länkarna i Redigeraren för transformeringen.

### <a name="editing-an-existing-transformation"></a>Redigera en befintlig omvandling

En användare kan redigera en befintlig **dela kolumn med hjälp av exempel** transformera genom att välja **redigera** alternativ för steget omvandling. Klicka på **redigera** öppnar Redigeraren transformeringen i **Avancerat läge**, och alla exempel som angavs under skapandet av transformeringen visas.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>Exempel på delning på en avgränsare för en fast, tecken

Det är vanligt för datafält skiljs åt av en enda fast avgränsare, till exempel ett kommatecken i ett CSV-format. Dela transformeringen försöker att härleda avgränsarna automatiskt. Till exempel i följande scenario den automatiskt härleder den ””. som avgränsare.

### <a name="splitting-ip-addresses"></a>Dela IP-adresser

Värdena i den första kolumnen delas predictively upp i fyra kolumner.

|IP-adress|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>Exempel på delning på flera avgränsare i viss kontexter

Användarens data kan omfatta många olika avgränsare separera olika fält. Dessutom kanske vissa förekomster av en avgränsande sträng avgränsare, men inte alla. Till exempel i följande fall avgränsare som krävs är ”-” ”,,” och ””: till önskat resultat. Dock inte alla förekomster av de ””: måste vara en delad punkt eftersom vi inte vill dela tiden men behålla det i en kolumn. Dela transformeringen härleder avgränsare i sammanhang där de förekommer i indata i stället för alla möjliga förekomst av avgränsaren. Transformeringen är också känna till vanliga datatyper, till exempel datum och tid.   

### <a name="splitting-store-opening-timings"></a>Dela store öppnas tidsinställningar

Värdena i följande *tidsinställningar* kolumnen hämta predictively delas upp i nio kolumner som visas i tabellen i den.

|Tidsinställningar|
|:-----|
|Måndag - fredag: 7:00:00 – 18:00:00 lördag: 9:00:00 – 17:00:00, söndag: stängda|
|Måndag - fredag: 9:00:00 – 18:00:00 lördag: 4:00 am - 4:00:00 söndag: stängda|
|Måndag - fredag: 8:30:00 - 7:00:00 lördag: 3:00:00 - 2:30 pm, söndag: stängda|
|Måndag - fredag: 8:00:00 – 18:00:00 lördag: 2:00:00 - 15:00:00, söndag: stängda|
|Måndag - fredag: 4:00:00 - 7:00:00 lördag: 9:00:00 – 4:00:00 söndag: stängda|
|Måndag - fredag: 8:30 am - 4:30 pm, lördag: 9:00:00 – 17:00:00, söndag: stängda|
|Måndag - fredag: 5:30 am - 18:30:00 lördag: 5:00:00 – 4:00:00 söndag: stängda|
|Måndag - fredag: 8:30:00 - 8:30 pm, lördag: 06:00:00 – 17:00:00, söndag: stängda|
|Måndag - fredag: 8:00:00 – 9:00:00 lördag: 9:00:00 - 8:00:00 söndag: stängda|
|Måndag - fredag: 10:00:00 – 9:30 pm, lördag: 9:30 am - 3:00:00 söndag: stängda|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|Måndag|Fredag|7:00:00|6:00 pm|Lördag|09:00|17:00:00|Söndag|Stängd|
|Måndag|Fredag|09:00|6:00 pm|Lördag|4:00 am|4:00 pm|Söndag|Stängd|
|Måndag|Fredag|8:30 am|7:00 pm|Lördag|3:00 am|2:30 pm|Söndag|Stängd|
|Måndag|Fredag|8:00:00|6:00 pm|Lördag|2:00:00|3:00 pm|Söndag|Stängd|
|Måndag|Fredag|4:00 am|7:00 pm|Lördag|09:00|4:00 pm|Söndag|Stängd|
|Måndag|Fredag|8:30 am|4:30 pm|Lördag|09:00|17:00:00|Söndag|Stängd|
|Måndag|Fredag|5:30 am|6:30 pm|Lördag|5:00:00|4:00 pm|Söndag|Stängd|
|Måndag|Fredag|8:30 am|8:30 pm|Lördag|6:00:00|17:00:00|Söndag|Stängd|
|Måndag|Fredag|8:00:00|9:00 pm|Lördag|09:00|8:00 pm|Söndag|Stängd|
|Måndag|Fredag|10:00:00|9:30 pm|Lördag|9:30 am|3:00 pm|Söndag|Stängd|

### <a name="splitting-iis-log"></a>Delar upp IIS-logg

Här är ett annat exempel på flera valfri avgränsare. Det här exemplet innehåller också en avgränsare för en kontextuella ”/”, som inte måste delas upp i URL: er eller filen sökvägar. Det är tråkigt att utföra den här uppdelningen med många *härledd kolumn efter exempel* transformeringar och ge exempel för varje fält. Med hjälp av delade Transformation utföra vi förutsägande uppdelningen av utan att några exempel.

|logtext|
|:-----|
|192.128.138.20--[2016-16-dessa 16:22:33-0200] ”GET /images/picture.gif HTTP/1.1” 234 343 www.yahoo.com ”http://www.example.com/” ”Mozilla/4.0 (kompatibel; MSIE 4) ””-”|
|10.128.72.213--[17 dessa 2016/12:43:12 +0300] ”GET /news/stuff.html HTTP/1.1” 200 6233 www.aol.com ”http://www.sample.com/” ”Mozilla/5.0 (MSIE)” ”-”|
|192.165.71.165--[2016/12/Nov 14:22:44-0500] ”GET /sample.ico HTTP/1.1” 342 7342 www.facebook.com ”-” ”Mozilla/5.0 (Windows. U; Windows NT 5.1; rv:1.7.3) ””-”|
|10.166.64.165--[2016-23-Nov 01:52:45-0800] ”GET /style.css HTTP/1.1” 200 2552 www.google.com ”http://www.test.com/index.html” ”Mozilla/5.0 (Windows)” ”-”|
|192.167.1.193--[2017-16/Jan 22:34:56 +0200] ”GET /js/ads.js HTTP/1.1” 200 23462 www.microsoft.com ”http://www.illustration.com/index.html” ”Mozilla/5.0 (Windows)” ”-”|
|192.147.76.193--2017-28/Jan 26:36:16 +0800 ”GET /search.php HTTP/1.1” 400 1777 www.bing.com ”-” ”Mozilla/4.0 (kompatibel; MSIE 6.0; Windows NT 5.1) ””-”|
|192.166.64.165--[2017-23/Mar 01:55:25-0800] ”GET /style.css HTTP/1.1” 200 2552 www.google.com ”http://www.test.com/index.html” ”Mozilla/5.0 (Windows)” ”-”|
|11.167.1.193--[2017-16/april 11:34:36 +0200] ”GET /js/ads.js HTTP/1.1” 200 23462 www.microsoft.com ”http://www.illustration.com/index.html” ”Mozilla/5.0 (Windows)” ”-”|

Hämtar upp på:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|Dessa/16/2016|16:22:33|-0200|HÄMTA|Images/Picture.gif|HTTP|1.1|234|343|www.Yahoo.com|http://www.example.com/|Mozilla|4.0|kompatibla. MSIE 4|
|10.128.72.213|2016-17-dessa|12:43:12|+0300|HÄMTA|News/stuff.HTML|HTTP|1.1|200|6233|www.AOL.com|http://www.Sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|2016/12/Nov|14:22:44|-0500|HÄMTA|Sample.ico|HTTP|1.1|342|7342|www.Facebook.com|-|Mozilla|5.0|Windows. U; Windows NT 5.1; rv:1.7.3|
|10.166.64.165|2016-Nov-23|01:52:45|-0800|HÄMTA|Style.CSS|HTTP|1.1|200|2552|www.Google.com|http://www.test.com/index.HTML|Mozilla|5.0|Windows|
|192.167.1.193|2017-16/Jan|22:34:56|+0200|HÄMTA|js/ADS.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.HTML|Mozilla|5.0|Windows|
|192.147.76.193|2017-28/Jan|26:36:16|+0800|HÄMTA|Search.php|HTTP|1.1|400|1777|www.Bing.com|-|Mozilla|4.0|kompatibla. MSIE 6.0; Windows NT 5.1|
|192.166.64.165|2017-23/Mar|01:55:25|-0800|HÄMTA|Style.CSS|HTTP|1.1|200|2552|www.Google.com|http://www.test.com/index.HTML|Mozilla|5.0|Windows|
|11.167.1.193|2017-16/april|11:34:36|+0200|HÄMTA|js/ADS.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.HTML|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>Exempel på Dela utan avgränsare
I vissa fall finns ingen faktisk avgränsare och datafält kan inträffa kontinuerligt bredvid varandra. I det här fallet upptäcker dela transformeringen mönster i data för att härleda förmodligen brytpunkter. Till exempel i följande scenario vill vi separata beloppet från currency-typen och dela härleder automatiskt gränsen mellan numeriska och icke-numeriska data som delad punkt.

### <a name="splitting-amount-with-currency-symbol"></a>Dela storlek med valutasymbol

|Mängd|Amount_1|Amount_2|
|:-----|:-----|:-----|
|\$14|$|14|
|£9|£|9|
|\$34|$|34|
|€ 18|€ |18|
|\$42|$|42|
|\$7|$|7|
|£42|£|42|
|\$16|$|16|
|€ 16|€ |16|
|\$15|$|15|
|\$16|$|16|
|€ 64|€ |64|

I följande exempel vill vi för att avgränsa vikt från enheterna. Igen dela härledning identifierar meningsfulla gränsen automatiskt och föredrar över andra möjliga avgränsare som den ””. tecken. 

### <a name="splitting-weights-with-units"></a>Dela vikterna med enheter

|Vikt|Weight_1|Weight_2|
|:-----|:-----|:-----|
|2.27KG|2.27|KG|
|1L|1|L|
|2.5 KG|2.5|KG|
|2KG|2|KG|
|1.7KGA|1.7|KGA|
|3KG|3|KG|
|2KG|2|KG|
|125G|125|G|
|500G|500|G|
|1.5KGA|1.5|KGA|

## <a name="technical-notes"></a>Teknisk information

Dela transform-funktionen är baserad på den **förutsägande Program sammanfattande** teknik. Den här tekniken lärs data transformation program automatiskt baserat på indata. Programmen syntetiskt i en domän-specifika språk. DSL baseras på avgränsarna och fält som sker särskilt sammanhang reguljärt uttryck. Mer information om den här tekniken finns i en [senaste publikation på det här avsnittet](https://www.microsoft.com/en-us/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 
