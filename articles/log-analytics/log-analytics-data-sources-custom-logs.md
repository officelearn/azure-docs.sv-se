---
title: Samla in anpassade loggar i Azure Log Analytics | Microsoft Docs
description: Logganalys kan samla in händelser från textfiler på Windows- och Linux-datorer.  Den här artikeln beskriver hur du definierar en ny anpassad logg och information om poster skapas i logganalys-arbetsytan.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: bwren
ms.openlocfilehash: bf9acd5d7130a5e35182271f07593adab19d448b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="custom-logs-in-log-analytics"></a>Anpassade loggar i logganalys
Datakälla för anpassade loggar i logganalys kan du samla in händelser från textfiler på Windows- och Linux-datorer. Många program loggar information till textfiler i stället för standardtjänster loggning, till exempel Windows-händelseloggen eller Syslog.  När samlas in, analyserar du varje post i inloggningen till enskilda fält med hjälp av den [anpassade fält](log-analytics-custom-fields.md) funktion i logganalys.

![Anpassade Logginsamling](media/log-analytics-data-sources-custom-logs/overview.png)

Loggfilerna ska hämtas måste matcha följande kriterier.

- Loggen måste antingen ha en enda post per rad eller använda en tidsstämpel som matchar ett av följande format i början av varje post.

    ÅÅÅÅ-MM-DD: MM: SS<br>M/D/ÅÅÅÅ HH: MM: SS TID <br>MON DD, YYYY: mm: ss

- Loggfilen får inte tillåta cirkulär loggning eller loggrotationen, där filen skrivs över med nya poster.
- Loggfilen måste använda ASCII- eller UTF-8-kodning.  Andra format, till exempel UTF-16 stöds inte.

>[!NOTE]
>Om det finns dubblettvärden i loggfilen, logganalys samlar in dem.  Dock blir sökresultaten inkonsekvent där filterresultaten visa fler händelser än resultatantal.  Kommer att vara viktigt att du verifierar att inloggningskontot för att fastställa om det program som skapar den orsakar problemet och åtgärda det. Om det är möjligt innan du skapar en anpassad logg samling definition.  
>
  
## <a name="defining-a-custom-log"></a>Definiera en anpassad logg
Använd följande procedur för att definiera en anpassad loggfil.  Rulla till slutet av den här artikeln en genomgång av ett prov för att lägga till en anpassad logg.

### <a name="step-1-open-the-custom-log-wizard"></a>Steg 1. Öppna guiden Anpassad logg
Guiden Anpassad logg körs i Azure-portalen och kan du definiera en ny anpassad logg att samla in.

1. Välj i Azure-portalen **logganalys** > din arbetsyta > **avancerade inställningar**.
2. Klicka på **Data** > **anpassade loggar**.
3. Som standard pushas alla konfigurationsändringar automatiskt till alla agenter.  Linux-agenter skickas en konfigurationsfil till Fluentd datainsamlaren.  Om du vill ändra den här filen manuellt på varje Linux-agenten och avmarkera sedan kryssrutan *Använd konfigurationen nedan för Mina Linux-datorer*.
4. Klicka på **Lägg till +** att öppna guiden Anpassad logg.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Steg 2. Överföra och parsa en exempellogg
Börja med att ladda upp ett exempel på anpassad logg.  Guiden kommer att parsa och visa posterna i den här filen att verifiera.  Log Analytics använder avgränsaren som du anger för att identifiera varje post.

**Ny rad** är standard-avgränsare och används för loggfiler som har en post per rad.  Om rad som börjar med ett datum och tid i något av formaten sedan kan du ange en **tidsstämpel** avgränsare som har stöd för transaktioner som sträcker sig över flera rader.

Om en avgränsare för en tidsstämpel används fylls egenskapen TimeGenerated för varje post i logganalys med datum/tid som angetts för posten i loggfilen.  Om en Radavgränsare för en ny används fylls TimeGenerated med datum och tid att logganalys samlas in transaktionen.


1. Klicka på **Bläddra** och bläddra till en exempelfil.  Observera att detta kan knappen betecknas **Välj fil** i vissa webbläsare.
2. Klicka på **Nästa**.
3. Guiden Anpassad logg ska överföra filen och visa en lista med poster som identifieras.
4. Ändra avgränsare som används för att identifiera en ny post och avgränsare som bäst identifierar poster i loggfilen.
5. Klicka på **Nästa**.

### <a name="step-3-add-log-collection-paths"></a>Steg 3. Lägg till loggsamling
Du måste definiera en eller flera sökvägar på agenten där den kan hitta anpassad logg.  Du kan antingen ange en specifik sökväg och ett namn på loggfilen eller du kan ange en sökväg med jokertecken för namnet.  Detta stöder program som skapar en ny fil varje dag eller när en fil når en viss storlek.  Du kan också ange flera sökvägar för en enda loggfil.

Till exempel kan ett program skapa en loggfil skapas varje dag med det datumet i namn som log20100316.txt. Ett mönster för sådana loggen kan vara *loggen\*.txt* som gäller för alla loggfilen efter programmet naming schemat.

Följande tabell innehåller exempel på giltiga att ange olika loggfilerna.

| Beskrivning | Sökväg |
|:--- |:--- |
| Alla filer i *C:\Logs* med filnamnstillägget .txt på Windows-agenten |C:\Logs\\\*.txt |
| Alla filer i *C:\Logs* med ett namn som börjar med loggen och filnamnstillägget .txt på Windows-agenten |C:\Logs\log\*.txt |
| Alla filer i */var/log/audit* med filnamnstillägget .txt på Linux-agent |/var/log/audit/*.txt |
| Alla filer i */var/log/audit* med ett namn som börjar med loggen och filnamnstillägget .txt på Linux-agent |/var/log/audit/log\*.txt |

1. Välj Windows eller Linux för att ange vilka sökvägsformat du lägger till.
2. Ange sökvägen och klicka på den **+** knappen.
3. Upprepa processen för eventuella ytterligare sökvägar.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Steg 4. Ange ett namn och en beskrivning av loggen
Det namn som du anger ska användas för typ som beskrivs ovan.  Den slutar alltid med _CL att skilja den som en anpassad logg.

1. Skriv ett namn på loggen.  Den  **\_CL** suffix anges automatiskt.
2. Lägg till en valfri **beskrivning**.
3. Klicka på **nästa** spara definition för anpassad logg.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Steg 5. Verifiera att de anpassade loggarna samlas in
Det kan ta upp till en timme innan den första informationen från en ny anpassad logg ska visas i logganalys.  Den börjar samla in poster från loggar finns i sökvägen som du angav från den punkt som du definierat anpassad logg.  Poster som du har överfört när du skapar anpassade loggen behåller inte, men den samlar in redan befintliga poster i de loggfiler som påträffas.

När logganalys startar har samlats in från anpassade loggen är dess poster tillgängliga med en logg-sökning.  Använd det namn som du gav anpassad logg som den **typen** i frågan.

> [!NOTE]
> Om egenskapen \data saknas från sökningen kan du behöva stänga och öppna webbläsaren.
>
>

### <a name="step-6-parse-the-custom-log-entries"></a>Steg 6. Parsa anpassade loggposter
Hela loggposten kommer att lagras i en enda egenskap som kallas **\data**.  Du kommer troligen vill separera olika delar av informationen i varje post i enskilda egenskaper lagras i posten.  Du gör detta med hjälp av den [anpassade fält](log-analytics-custom-fields.md) funktion i logganalys.

Detaljerade anvisningar för parsning av den anpassa loggposten finns inte här.  Mer information finns i [anpassade fält](log-analytics-custom-fields.md) dokumentationen för den här informationen.

## <a name="removing-a-custom-log"></a>Tar bort en anpassad logg
Ta bort en anpassad logg som du tidigare har definierats med hjälp av följande process i Azure-portalen.

1. Från den **Data** -menyn i den **avancerade inställningar** arbetsytan, Välj **anpassade loggar** att lista alla anpassade loggar.
2. Klicka på **ta bort** bredvid anpassade loggen att ta bort.


## <a name="data-collection"></a>Datainsamling
Logganalys samlar in nya poster från varje anpassad logg ungefär var 5: e minut.  Agenten att registrera sin plats i varje loggfil som samlas in från.  Om agenten tas offline under en tidsperiod, sedan logganalys samlar in poster från där den senast slutade, även om de posterna som skapades när agenten var offline.

Hela innehållet i loggposten skrivs till en enda egenskap som kallas **\data**.  Du kan parsa detta till flera egenskaper som kan analyseras och genomsöks separat genom att definiera [anpassade fält](log-analytics-custom-fields.md) när du har skapat anpassade loggen.

## <a name="custom-log-record-properties"></a>Egenskaper för anpassad logg-post
Anpassade loggposter har en typ med namnet på loggen som du anger och egenskaper i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| TimeGenerated |Datum och tid då posten samlades in av logganalys.  Om loggen använder en avgränsare för en baseras på tidpunkt är den tid som samlas in från posten. |
| SourceSystem |Typ av agenten posten samlats in från. <br> Ansluta OpsManager – Windows-agenten, antingen direkt eller System Center Operations Manager <br> Linux – alla Linux-agenter |
| RawData |Fullständig text i posten som samlas in. |
| ManagementGroupName |Namnet på hanteringsgruppen för System Center Operations hantera agenter.  För andra agenter är AOI -\<arbetsyte-ID\> |

## <a name="log-searches-with-custom-log-records"></a>Loggen sökningar med anpassade loggposter
Poster från anpassade loggar lagras i logganalys-arbetsytan precis som poster från andra datakällor.  De har en typ som matchar namnet som du anger när du definierar loggen, så du kan använda egenskapen Type i sökningen för att hämta poster som samlas in från en viss loggning.

Följande tabell innehåller olika exempel på loggen sökningar som hämtar poster från anpassade loggar.

| Fråga | Beskrivning |
|:--- |:--- |
| MyApp_CL |Alla händelser från en anpassad logga namngiven MyApp_CL. |
| MyApp_CL &#124; where Severity_CF=="error" |Alla händelser från en anpassad logga namngivna MyApp_CL med värdet *fel* i ett anpassat fält med namnet *Severity_CF*. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Exempel genomgången för att lägga till en anpassad logg
Följande avsnitt beskriver hur ett exempel på hur du skapar en anpassad logg.  Exempellogg som samlas in har en enda post för varje rad som börjar med ett datum och tid och sedan kommaavgränsad fält för koden, status och meddelandet.  Flera exempel poster visas nedan.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Överföra och parsa en exempellogg
Vi tillhandahåller någon loggfilerna och kan se de händelser som kommer samla in.  I det här fallet är ny rad tillräcklig avgränsare.  Om en enda post i loggen kan sträcka sig över flera rader om, behöver en avgränsare för en tidsstämpel som ska användas.

![Överföra och parsa en exempellogg](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Lägg till loggsamling
Loggfilerna finns i *C:\MyApp\Logs*.  En ny fil skapas varje dag med ett namn som innehåller datum i mönstret *appYYYYMMDD.log*.  Ett tillräckligt mönster för den här loggfilen skulle vara *C:\MyApp\Logs\\\*.log*.

![Loggsökvägen för samlingen](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Ange ett namn och en beskrivning av loggen
Vi använder ett namn på *MyApp_CL* och skriver i en **beskrivning**.

![Loggnamn](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Verifiera att de anpassade loggarna samlas in
Vi använder en fråga med *typ = MyApp_CL* och alla poster från insamlade loggen.

![Loggen fråga utan anpassade fält](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Parsa anpassade loggposter
Vi använder anpassade fält för att definiera den *EventTime*, *kod*, *Status*, och *meddelandet* fält och vi kan se skillnaden mellan de poster som returneras av frågan.

![Loggen frågan med anpassade fält](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Nästa steg
* Använd [anpassade fält](log-analytics-custom-fields.md) att analysera poster i anpassade inloggningen till enskilda fält.
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar.
