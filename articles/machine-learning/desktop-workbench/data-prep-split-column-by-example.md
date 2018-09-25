---
title: Dela upp kolumn efter exempel omvandling med hjälp av Azure Machine Learning Workbench
description: Referensdokument för ”dela kolumner med exempel”-transformering
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
ms.openlocfilehash: 3edf49484e5bc05a297b8d8969632fb902aa1714
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953743"
---
# <a name="split-column-by-example-transformation"></a>Dela upp kolumn efter exempel transformering

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Den här transformeringen predictively delar upp innehållet i en kolumn i meningsfulla gränser utan användarindata från. Split-algoritmen väljer gränserna när du har analyserat innehållet i kolumnen. Dessa gränser kan definieras av
* En fast avgränsare
* Flera, valfri avgränsare som visas i synnerhet när det gäller kan eller,
* Datamönster eller vissa typer av enheter

Användare kan också styra beteendet delande i Avancerat läge där de kan ange avgränsare eller för att ge exempel på önskade dela.

I teorin dela går också att utföra åtgärder i Workbench med hjälp av en serie *Härled kolumner med exempel* omvandlar. Men om det finns flera kolumner, kan som härleds var och en av de individuellt även använder av exemplet metoden vara tidskrävande. Förutsägande dela kan enkelt dela utan att användaren behöver att ge exempel för var och en av kolumnerna. 

## <a name="how-to-perform-this-transformation"></a>Hur du utför den här omvandlingen

1. Välj den kolumn som du vill dela. 
2. Välj **dela kolumner med exempel** från den **omvandlar** menyn. Eller, högerklicka på rubriken för den markerade kolumnen och välj **dela kolumner med exempel** på snabbmenyn. Omvandla redigeringsprogrammet öppnas och nya kolumner läggs bredvid den markerade kolumnen. Nu arbetsstationen analyserar indatakolumnen avgör dela gränser och Syntetiserar program att dela upp kolumnen som visas i rutnätet. Det syntetiserade programmet körs mot alla rader i kolumnen. Avgränsare, undantas om sådana finns, från slutresultatet.
3. Du kan klicka på den **Avancerat läge** får bättre kontroll över delade transformeringen. 
4. Granska utdata och klicka på **OK** att acceptera omvandlingen. 

Transformering syftar till att producera samma antal kolumner som resulterande för alla rader. Om en rad inte kan delas upp på definieras gränserna den genererar *null* för alla kolumner som standard. Det här beteendet kan ändras i den **Avancerat läge**.

### <a name="transform-editor-advanced-mode"></a>Omvandla Redigerare: Avancerat läge
**Avancerat läge** ger en rikare upplevelse för att dela kolumner. 

Att välja **hålla avgränsare kolumner** innehåller avgränsare i slutresultatet. Avgränsare undantas som standard.

Ange **avgränsare** åsidosätter automatisk avgränsare val av logiken. Flera avgränsare, en i varje rad, kan anges som **avgränsare**. Alla dessa tecken används som avgränsare för kolumnen delas.

Ibland kan dela ett värde på definieras gränser ger olika antal kolumner än flesta andra. I sådana fall **Fyll riktning** används för att bestämma den ordning som kolumnerna ska fyllas.

När du klickar på **Visa exempel** visar representativt rader för vilka användare ska ge ett exempel på Dela. Användaren kan klicka på den **upp** pilen till höger om den föreslagna raden att flytta upp rad som ett exempel. 

Användaren kan **ta bort kolumn** eller **infoga nya kolumner** genom att högerklicka på rubriken för den **exempel tabell**.

Användaren kan kopiera och klistra in värden i en Cell till en annan för att ge ett exempel på Dela.

Användare kan växla mellan den **standardläge** och **Avancerat läge** genom att klicka på länkarna i Redigeraren för att omvandla.

### <a name="transform-editor-send-feedback"></a>Omvandla Redigerare: skicka Feedback

När du klickar på den **skicka feedback** länken öppnas den **Feedback** dialogen med kommentarrutan som innehåller parameterval och exempel användaren har angetts. Användaren bör granska innehållet i kommentarrutan och ge mer information som hjälper oss att förstå problemet. Om användaren inte vill dela data med Microsoft användaren bör ta bort förifyllda exempeldata innan du klickar på den **skicka Feedback** knappen. 


### <a name="editing-an-existing-transformation"></a>Redigera en befintlig transformering

En användare kan redigera en befintlig **delning av Kolumnexempel** omvandla genom att välja **redigera** alternativ i steget omvandling. När du klickar på **redigera** öppnas redigeraren transformera i **Avancerat läge**, och alla exempel som angavs under skapandet av transformeringen visas.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>Exempel på delning på en fast, tecken avgränsare

Det är vanligt att datafält som ska avgränsas med en enda fast avgränsare, till exempel ett kommatecken CSV-format. Dela vi försöker att härleda avgränsarna automatiskt. Till exempel i följande scenario det automatiskt härleder de ””. som avgränsare.

### <a name="splitting-ip-addresses"></a>Dela upp IP-adresser

Värdena i den första kolumnen predictively dela upp till fyra kolumner.

|IP-adress|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>Exempel på delning på flera avgränsare i viss kontexter

Användarens data kan omfatta många olika avgränsare att separera olika fält. Dessutom kanske vissa förekomster av en avgränsande sträng en avgränsare men inte alla. Till exempel i följande fall uppsättningen avgränsare som krävs är ”-” ”,,” och ””: till önskat resultat. Men inte alla förekomster av den ””: ska vara en delad punkt, eftersom vi inte vill dela upp tiden men behåll dem i en enda kolumn. Dela transformering härleder avgränsare inom kontexter de ligger i indata i stället för alla möjliga förekomst av avgränsaren. Vi har även om vanliga datatyper, till exempel datum och tider.   

### <a name="splitting-store-opening-timings"></a>Dela upp store öppnas tidsinställningar

Värdena i följande *tidsinställningar* kolumnen få predictively att dela upp i nio kolumner som visas i tabellen under den.

|Tidsinställningar|
|:-----|
|Måndag - fredag: 7:00:00 – 18:00:00, lördag: 9:00:00 – 17:00:00, söndag: stängda|
|Måndag – fredag: 9:00:00 – 18:00:00, lördag: 4:00 am - 4:00 pm, söndag: stängda|
|Måndag - fredag: 8:30:00 - 7:00 pm, lördag: 3:00 am - 2:30 pm, söndag: stängda|
|Måndag - fredag: 8:00:00 – 18:00:00, lördag: 02:00:00 - 3:00 pm, söndag: stängda|
|Måndag - fredag: 4:00:00 – 19:00:00, lördag: 9:00 am - 4:00 pm, söndag: stängda|
|Måndag - fredag: 8:30 am - 4:30 pm, lördag: 9:00:00 – 17:00:00, söndag: stängda|
|Måndag - fredag: 05:30:00 – 18:30:00, lördag: 05:00:00 - 4:00 pm, söndag: stängda|
|Måndag - fredag: 8:30:00 - 8:30 pm, lördag: 06:00:00 – 17:00:00, söndag: stängda|
|Måndag - fredag: 8:00:00 - 9:00 pm, lördag: 9:00:00 - 8:00 pm, söndag: stängda|
|Måndag - fredag: 10:00:00 - 9:30 pm, lördag: 9:30:00 - 3:00 pm, söndag: stängda|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|måndag|fredag|7:00:00|18:00:00|lördag|09:00|17:00:00|söndag|Stängd|
|måndag|fredag|09:00|18:00:00|lördag|4:00:00|4:00 pm|söndag|Stängd|
|måndag|fredag|8:30 am|19:00:00|lördag|3:00 am|2:30 pm|söndag|Stängd|
|måndag|fredag|8:00:00|18:00:00|lördag|2:00:00|3:00 pm|söndag|Stängd|
|måndag|fredag|4:00:00|19:00:00|lördag|09:00|4:00 pm|söndag|Stängd|
|måndag|fredag|8:30 am|4:30 pm|lördag|09:00|17:00:00|söndag|Stängd|
|måndag|fredag|05:30:00|18:30:00|lördag|5:00:00|4:00 pm|söndag|Stängd|
|måndag|fredag|8:30 am|8:30 pm|lördag|06:00:00|17:00:00|söndag|Stängd|
|måndag|fredag|8:00:00|9:00 pm|lördag|09:00|20:00 EST|söndag|Stängd|
|måndag|fredag|10:00:00|9:30 pm|lördag|kl. 9:30|3:00 pm|söndag|Stängd|

### <a name="splitting-iis-log"></a>Delar upp IIS-logg

Här är ett annat exempel på flera valfri avgränsare. Det här exemplet innehåller också en sammanhangsberoende avgränsare ”/”, som inte måste delas upp i URL: er eller filsökvägar. Det är tråkigt att utföra den här uppdelningen använder många *Härled kolumner med exempel* transformeringar och ger exempel för varje fält. Med hjälp av dela Transformation utföra vi förebyggande uppdelningen av utan att några exempel.

|logtext|
|:-----|
|192.128.138.20--[16 oktober 2016/16:22:33-0200] ”GET /images/picture.gif HTTP/1.1” 234 343 www.yahoo.com ”http://www.example.com/” ”Mozilla/4.0 (kompatibel; MSIE 4) ””-”|
|10.128.72.213--[17 oktober 2016/12:43:12 +0300] ”GET /news/stuff.html HTTP/1.1” 200 6233 www.aol.com ”http://www.sample.com/” ”Mozilla/5.0 (MSIE)” ”-”|
|192.165.71.165--[2016/12/november 44:22:14-0500] ”GET /sample.ico HTTP/1.1” 342 7342 www.facebook.com ”-” ”Mozilla/5.0 (Windows; U; Windows NT 5.1; rv:1.7.3) ””-”|
|10.166.64.165--[23 november 2016/01:52:45-0800] ”GET /style.css HTTP/1.1” 200 2552 www.google.com ”http://www.test.com/index.html” ”Mozilla/5.0 (Windows)” ”-”|
|192.167.1.193--[16/Jan/2017 22:34:56 +0200] ”GET /js/ads.js HTTP/1.1” 200 23462 www.microsoft.com ”http://www.illustration.com/index.html” ”Mozilla/5.0 (Windows)” ”-”|
|192.147.76.193--28/Jan/2017 26:36:16 +0800 ”GET /search.php HTTP/1.1” 400 1777 www.bing.com ”-” ”Mozilla/4.0 (kompatibel; MSIE 6.0; Windows NT 5.1) ””-”|
|192.166.64.165--[23 mars 2017/01:55:25-0800] ”GET /style.css HTTP/1.1” 200 2552 www.google.com ”http://www.test.com/index.html” ”Mozilla/5.0 (Windows)” ”-”|
|11.167.1.193--[16/Apr/2017 11:34:36 +0200] ”GET /js/ads.js HTTP/1.1” 200 23462 www.microsoft.com ”http://www.illustration.com/index.html” ”Mozilla/5.0 (Windows)” ”-”|

Hämtar upp på:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|16 oktober/2016|16:22:33|-0200|HÄMTA|Images/Picture.gif|HTTP|1.1|234|343|www.Yahoo.com|http://www.example.com/|Mozilla|4.0|kompatibla. MSIE 4|
|10.128.72.213|Oktober/17/2016|12:43:12|+0300|HÄMTA|News/stuff.HTML|HTTP|1.1|200|6233|www.AOL.com|http://www.sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|2016/12/Nov|14:22:44|-0500|HÄMTA|Sample.ico|HTTP|1.1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; rv:1.7.3|
|10.166.64.165|23 november 2016 /|01:52:45|-0800|HÄMTA|Style.CSS|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|16/Jan/2017|22:34:56|+0200|HÄMTA|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|28/Jan/2017|26:36:16|+0800|HÄMTA|Search.php|HTTP|1.1|400|1777|www.Bing.com|-|Mozilla|4.0|kompatibla. MSIE 6.0; Windows NT 5.1|
|192.166.64.165|Mars/23/2017|01:55:25|-0800|HÄMTA|Style.CSS|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|16/Apr/2017|11:34:36|+0200|HÄMTA|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>Exempel på Dela utan avgränsare
I vissa fall kan det finns inga faktiska avgränsare och datafält som kan uppstå sammanhängande över tid bredvid varandra. Dela transformering identifierar i det här fallet automatiskt mönster i data för att härleda förmodligen dela punkter. Till exempel i följande scenario vi vill separera beloppet från valutatypen och dela härleder automatiskt gränsen mellan numeriska och icke-numeriska data som den delade punkten.

### <a name="splitting-amount-with-currency-symbol"></a>Dela upp storlek med valutasymbol

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

I följande exempel vill vi separera viktningsvärden från enheterna. Igen dela inferens identifierar meningsfulla gränsen automatiskt och föredrar över andra möjliga avgränsare som den ””. tecken. 

### <a name="splitting-weights-with-units"></a>Dela upp vikterna med enheter

|Vikt|Weight_1|Weight_2|
|:-----|:-----|:-----|
|2.27KG|2.27|KG|
|1L|1|L|
|2,5 KG|2.5|KG|
|2KG|2|KG|
|1.7KGA|1.7|KGA|
|3KG|3|KG|
|2KG|2|KG|
|125G|125|G|
|500G|500|G|
|1.5KGA|1.5|KGA|

## <a name="technical-notes"></a>Teknisk information

Funktionen Split transformeringen baserat på den **förutsägande Program syntes** teknik. Den här tekniken lärs data transformation program automatiskt baserat på indata. Programmen syntetiskt på ett domänspecifika språk. DSL baseras på avgränsare och fält som uppstår i synnerhet när det gäller reguljärt uttryck. Mer information om den här tekniken finns i en [senaste publikationen på det här avsnittet](https://www.microsoft.com/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 
