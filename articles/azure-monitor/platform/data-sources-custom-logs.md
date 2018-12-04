---
title: Samla in anpassade loggar i Azure Log Analytics | Microsoft Docs
description: Log Analytics kan samla in händelser från textfiler på både Windows och Linux-datorer.  Den här artikeln beskriver hur du definierar en ny anpassad logg och information om de poster som skapas i Log Analytics-arbetsytan.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 099fe053f354f2773dfec1d3085c03d83671ed2a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833864"
---
# <a name="custom-logs-in-log-analytics"></a>Anpassade loggar i Log Analytics
Datakälla för anpassade loggar i Log Analytics kan du samla in händelser från textfiler på både Windows och Linux-datorer. Många program logga information till textfiler i stället för standardtjänster loggning, till exempel Windows händelselogg eller Syslog.  När samlats in, kan du parsa varje post i inloggningen till enskilda fält med hjälp av den [anpassade fält](../../log-analytics/log-analytics-custom-fields.md) funktion i Log Analytics.

![Anpassade Logginsamling](media/data-sources-custom-logs/overview.png)

Loggfiler ska samlas in måste matcha följande kriterier.

- Loggen måste antingen ha en enda post per rad eller använda en tidsstämpel som matchar en av följande format i början av varje post.

    ÅÅÅÅ-MM-DD: MM: SS<br>M/D/ÅÅÅÅ HH: MM: SS AM/PM<br>Mån DD, YYYY: mm: ss<br />ååmmdd: mm: ss<br />ddMMyy: mm: ss<br />MMM d: mm: ss<br />dd/mm/yyyy:HH:mm:ss zzz<br />åååå-MM-ddTHH:mm:ssK

- Loggfilen får inte tillåta cirkulär loggning eller loggrotation, där filen skrivs över med nya poster.
- Loggfilen måste använda ASCII- eller UTF-8-kodning.  Andra format, till exempel UTF-16 stöds inte.

>[!NOTE]
>Om det finns dubblettvärden i loggfilen, Log Analytics samlar in dem.  Men blir sökresultaten inkonsekvent där filterresultaten visar fler händelser än resultatantal.  Det är viktigt att du verifierar loggen för att fastställa om det program som skapar den som orsakar problemet och åtgärda detta om möjligt innan du skapar den anpassade loggen samling definitionen.  
>
  
>[!NOTE]
> Om ditt program skapar en ny loggfil skapas varje dag eller när den når en viss storlek, identifierar Log Analytics-agenten för Linux inte dem förrän den startas. Detta är eftersom agenten endast räknar upp och börjar övervakning för mönster med de angivna loggarna vid start och därför måste du planera runt den genom att automatisera omstarten av agenten.  Den här begränsningen finns inte med Log Analytics-agenten för Windows.  
>

## <a name="defining-a-custom-log"></a>Definiera en anpassad logg
Använd följande procedur för att definiera en anpassad loggfil.  Rulla ned till slutet av den här artikeln en genomgång över ett exempel på att lägga till en anpassad logg.

### <a name="step-1-open-the-custom-log-wizard"></a>Steg 1. Öppna guiden Anpassad logg
Guiden Anpassad logg körs i Azure portal och kan du definiera en ny anpassad logg att samla in.

1. I Azure-portalen väljer du **Log Analytics** > din arbetsyta > **avancerade inställningar**.
2. Klicka på **Data** > **anpassade loggar**.
3. Som standard skickas automatiskt alla konfigurationsändringar till alla agenter.  För Linux-agenter skickas en konfigurationsfil till Fluentd datainsamlaren.  Om du vill ändra den här filen manuellt på varje Linux-agenten och avmarkerar sedan kryssrutan *Använd konfigurationen nedan för Mina Linux-datorer*.
4. Klicka på **Lägg till +** att öppna guiden Anpassad logg.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Steg 2. Ladda upp och parsa en exempellogg
Du startar genom att ladda upp ett exempel på anpassad logg.  Guiden Parsar och visa posterna i den här filen för dig att verifiera.  Log Analytics använder avgränsaren som du anger för att identifiera varje post.

**Ny rad** är standardavgränsaren som och används för loggfiler som har en enda post per rad.  Om rad som börjar med ett datum och tid i någon av de tillgängliga formaten så kan du ange en **tidsstämpel** avgränsare som har stöd för poster som sträcker sig över flera rader.

Om en tidsstämpel avgränsare används fylls egenskapen TimeGenerated för varje post som lagras i Log Analytics med datum/tid som angetts för den posten i loggfilen.  Om en ny Radavgränsare används fylls TimeGenerated med datum och tid att Log Analytics som samlas in transaktionen.


1. Klicka på **Bläddra** och bläddra till en exempelfil.  Observera att detta kan knappen betecknas **Välj fil** i vissa webbläsare.
2. Klicka på **Nästa**.
3. Guiden Anpassad logg ska överföra filen och visa de poster som identifieras.
4. Ändra den avgränsare som används för att identifiera en ny post och välj den avgränsare som bäst identifierar poster i loggfilen.
5. Klicka på **Nästa**.

### <a name="step-3-add-log-collection-paths"></a>Steg 3. Lägg till sökvägar till loggsamling
Du måste definiera en eller flera sökvägar på agenten där den kan hitta den anpassade loggen.  Du kan antingen ange en specifik sökväg och ett namn för loggfilen eller du kan ange en sökväg med ett jokertecken för namnet. Detta har stöd för program som skapar en ny fil varje dag eller när en fil når en viss storlek. Du kan också ange flera sökvägar för en enda loggfil.

Till exempel kan ett program skapa en loggfil skapas varje dag med det datum som ingår i namnet som log20100316.txt. Ett mönster för sådana en logg kan vara *log\*.txt* som skulle gälla för alla loggfiler efter programmet namngivning av schemat.

>[!NOTE]
> Om ditt program skapar en ny loggfil skapas varje dag eller när den når en viss storlek, identifierar Log Analytics-agenten för Linux inte dem förrän den startas. Detta är eftersom agenten endast räknar upp och börjar övervakning för mönster med de angivna loggarna vid start och därför måste du planera runt den genom att automatisera omstarten av agenten.  Den här begränsningen finns inte med Log Analytics-agenten för Windows.  
>

Följande tabell innehåller exempel på giltiga att ange olika loggfilerna.

| Beskrivning | Sökväg |
|:--- |:--- |
| Alla filer i *C:\Logs* med filnamnstillägget .txt på Windows-agent |C:\Logs\\\*.txt |
| Alla filer i *C:\Logs* med ett namn som börjar med logg filnamnstillägget .txt på Windows-agent |C:\Logs\log\*.txt |
| Alla filer i */var/log/audit* med filnamnstillägget .txt på Linux-agenten |/var/log/audit/*.txt |
| Alla filer i */var/log/audit* med ett namn som börjar med logg filnamnstillägget .txt på Linux-agenten |/var/log/audit/log\*.txt |

1. Välj Windows eller Linux för att ange vilka Sökvägsformatet du lägger till.
2. Anger sökvägen och klicka på den **+** knappen.
3. Upprepa processen för eventuella ytterligare sökvägar.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Steg 4. Ange ett namn och en beskrivning av loggen
Det namn som du anger ska användas för loggtypen enligt beskrivningen ovan.  Det slutar alltid med _CL att skilja den som en anpassad logg.

1. Skriv ett namn på loggen.  Den  **\_CL** suffix anges automatiskt.
2. Lägg till en valfri **beskrivning**.
3. Klicka på **nästa** att spara den anpassade logg-definitionen.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Steg 5. Verifiera att de anpassade loggarna samlas in
Det kan ta upp till en timme för den ursprungliga data från en ny anpassad logg visas i Log Analytics.  Den börjar samla in poster från loggar som finns i sökvägen som du angav från det datum då du definierat anpassad logg.  Det kommer inte att ha de poster som du laddade upp när den anpassade loggen skapas, men den samlar in befintliga poster i loggfilerna som den hittar.

När Log Analytics börjar samla in från den anpassade loggen, är dess poster tillgängliga med en Loggsökning.  Använd det namn som du gav den anpassade loggen som den **typ** i frågan.

> [!NOTE]
> Om egenskapen \data saknas från sökningen kan du behöva stänga och öppna din webbläsare.
>
>

### <a name="step-6-parse-the-custom-log-entries"></a>Steg 6. Parsa anpassade loggposter
Hela logg kommer att lagras i en enskild egenskap med namnet **\data**.  Du kommer troligen vill separera olika typer av informationen i varje post i enskilda egenskaperna som lagrats i posten.  Du gör detta med hjälp av den [anpassade fält](../../log-analytics/log-analytics-custom-fields.md) funktion i Log Analytics.

Detaljerade anvisningar för parsning av anpassad logg tillhandahålls inte här.  Finns det [anpassade fält](../../log-analytics/log-analytics-custom-fields.md) dokumentationen för den här informationen.

## <a name="removing-a-custom-log"></a>Ta bort en anpassad logg
Ta bort en anpassad logg som du tidigare har definierat med hjälp av följande process i Azure-portalen.

1. Från den **Data** menyn i den **avancerade inställningar** arbetsytan och välj **anpassade loggar** att lista alla anpassade loggar.
2. Klicka på **ta bort** bredvid den anpassade loggen att ta bort.


## <a name="data-collection"></a>Datainsamling
Log Analytics samlar in nya poster från varje anpassad logg ungefär var femte minut.  Agenten att registrera sin plats i varje loggfil som det samlar in från.  Om agenten kopplas från för en viss tidsperiod, sedan Log Analytics samlar in poster från där den senast slutade, även om de posterna som skapades när agenten var offline.

Hela innehållet i loggposten skrivs till en enskild egenskap med namnet **\data**.  Du kan parsa detta till flera egenskaper som kan analyseras och söks igenom separat genom att definiera [anpassade fält](../../log-analytics/log-analytics-custom-fields.md) när du har skapat den anpassade loggen.

## <a name="custom-log-record-properties"></a>Anpassade egenskaper för posten
Poster för den anpassade loggen har en typ med namnet på loggen som du anger och egenskaperna i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| TimeGenerated |Datum och tid då posten har samlats in av Log Analytics.  Om loggen använder en tidsbaserad avgränsare är den tid som samlas in från posten. |
| SourceSystem |Typ av posten har samlats in från agenten. <br> Ansluta OpsManager – Windows-agenten, antingen direkt eller System Center Operations Manager <br> Linux – alla Linux-agenter |
| \Data |Fulltextsökning i posten som samlas in. |
| ManagementGroupName |Namnet på hanteringsgruppen för System Center Operations Manager-agenter.  För andra agenter är detta AOI -\<arbetsyte-ID\> |

## <a name="log-searches-with-custom-log-records"></a>Loggsökningar med anpassade loggposter
Poster från anpassade loggar lagras i Log Analytics-arbetsytan precis som poster från andra datakällor.  De har en typ som matchar det namn som du anger när du definierar loggen, så du kan använda egenskapen Type i sökningen för att hämta poster som samlas in från en viss loggning.

I följande tabell innehåller olika exempel på sökningar i loggen som hämtar poster från anpassade loggar.

| Söka i data | Beskrivning |
|:--- |:--- |
| MyApp_CL |Alla händelser från en anpassad logga namngivna MyApp_CL. |
| MyApp_CL &#124; där Severity_CF == ”error” |Alla händelser från en anpassad logga namngivna MyApp_CL med värdet *fel* i ett anpassat fält med namnet *Severity_CF*. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Exempelgenomgång för att lägga till en anpassad logg
Följande avsnitt går igenom ett exempel för att skapa en anpassad logg.  Exempellogg som samlas in har en enda post på varje rad som börjar med ett datum och tid och sedan avgränsat fält för kod, status och meddelandet.  Flera Exempelposter visas nedan.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Ladda upp och parsa en exempellogg
Vi tillhandahåller en av filerna och kan se de händelser som kommer samla in.  Ny rad är i det här fallet en tillräcklig avgränsare.  Om en enda post i loggen kan dock innehålla flera rader, behöver en tidsstämpel avgränsare som ska användas.

![Ladda upp och parsa en exempellogg](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Lägg till sökvägar till loggsamling
Loggfilerna finns i *C:\MyApp\Logs*.  En ny fil skapas varje dag med ett namn som innehåller datum i mönstret *appYYYYMMDD.log*.  Arbetsprofilen tillräcklig för den här loggen är *C:\MyApp\Logs\\\*.log*.

![Samling loggsökväg](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Ange ett namn och en beskrivning av loggen
Vi använder ett namn på *MyApp_CL* och skriver i en **beskrivning**.

![Loggnamn](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Verifiera att de anpassade loggarna samlas in
Vi använder en fråga med *typ = MyApp_CL* och alla poster från insamlade loggen.

![Loggfråga utan anpassade fält](media/data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Parsa anpassade loggposter
Vi använder anpassade fält för att definiera den *EventTime*, *kod*, *Status*, och *meddelande* fält och vi kan se skillnaden mellan poster som returneras av frågan.

![Loggfråga med ytterligare anpassade fält](media/data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Nästa steg
* Använd [anpassade fält](../../log-analytics/log-analytics-custom-fields.md) att parsa posterna i anpassade inloggningen till enskilda fält.
* Lär dig mer om [loggsökningar](../../azure-monitor/log-query/log-query-overview.md) att analysera data som samlas in från datakällor och lösningar.
