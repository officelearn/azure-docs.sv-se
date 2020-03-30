---
title: Samla in anpassade loggar i Azure Monitor | Microsoft-dokument
description: Azure Monitor kan samla in händelser från textfiler på både Windows- och Linux-datorer.  I den här artikeln beskrivs hur du definierar en ny anpassad logg och information om de poster som de skapar i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/26/2019
ms.openlocfilehash: 1e889aaef7cd01cd743e8063a8a1dd5138ba9d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670601"
---
# <a name="custom-logs-in-azure-monitor"></a>Anpassade loggar i Azure Monitor

Med datakällan Custom Logs i Azure Monitor kan du samla in händelser från textfiler på både Windows- och Linux-datorer. Många program loggar information till textfiler i stället för standardloggningstjänster som Windows Event Log eller Syslog. När du har samlat in kan du antingen tolka data i enskilda fält i dina frågor eller extrahera data under insamlingen till enskilda fält.

![Anpassad loggsamling](media/data-sources-custom-logs/overview.png)

Loggfilerna som ska samlas in måste matcha följande kriterier.

- Loggen måste antingen ha en enda post per rad eller använda en tidsstämpel som matchar något av följande format i början av varje post.

    YYYY-MM-DD HH:MM:SS<br>M/D/YYYY HH:MM:SS AM/PM<br>Mån DD, YYYY HH:MM:SS<br />yyMMdd HH:mm:ss<br />ddMMyy HH:mm:ss<br />MMM d hh:mm:ss<br />dd/MMM/yyyy:HH:mm:ss zzz<br />yyyy-MM-ddTHH:mm:ssK

- Loggfilen får inte tillåta cirkelloggning eller loggrotation, där filen skrivs över med nya poster.
- Loggfilen måste använda ASCII- eller UTF-8-kodning.  Andra format som UTF-16 stöds inte.

>[!NOTE]
> Om det finns dubblettposter i loggfilen samlar Azure Monitor in dem. Frågeresultaten blir dock inkonsekventa om filterresultaten visar fler händelser än resultatantalet. Det är viktigt att du validerar loggen för att avgöra om programmet som skapar den orsakar det här beteendet och åtgärda den om möjligt innan du skapar den anpassade logginsamlingsdefinitionen.  
>

>[!NOTE]
> En Log Analytics-arbetsyta stöder följande gränser:
> 
> * Endast 500 anpassade loggar kan skapas.
> * En tabell stöder bara upp till 500 kolumner. 
> * Det maximala antalet tecken för kolumnnamnet är 500. 
>

>[!IMPORTANT]
>Anpassad loggsamling kräver att programmet som skriver loggfilen tömmer logginnehållet till disken med jämna mellanrum. Detta beror på att den anpassade loggsamlingen förlitar sig på filsystemändringsmeddelanden för loggfilen som spåras.

## <a name="defining-a-custom-log"></a>Definiera en anpassad logg
Använd följande procedur för att definiera en anpassad loggfil.  Bläddra till slutet av den här artikeln för en genomgång av ett exempel på att lägga till en anpassad logg.

### <a name="step-1-open-the-custom-log-wizard"></a>Steg 1. Öppna guiden Anpassad logg
Guiden Anpassad logg körs i Azure-portalen och låter dig definiera en ny anpassad logg att samla in.

1. I Azure-portalen väljer du **Log Analytics-arbetsytor** > arbetsytan > **avancerade inställningar**.
2. Klicka på **Data** > **Anpassade loggar**.
3. Som standard skjuts alla konfigurationsändringar automatiskt till alla agenter. För Linux-agenter skickas en konfigurationsfil till Datainsamlaren för Fluentd.
4. Öppna guiden Anpassad logg genom att klicka **på Lägg** till+ .

### <a name="step-2-upload-and-parse-a-sample-log"></a>Steg 2. Ladda upp och tolka en exempellogg
Du börjar med att ladda upp ett exempel på den anpassade loggen.  Guiden tolkar och visar posterna i den här filen som du kan validera.  Azure Monitor använder avgränsaren som du anger för att identifiera varje post.

**Ny rad** är standardavgränsare och används för loggfiler som har en enda post per rad.  Om raden börjar med ett datum och en tid i ett av de tillgängliga formaten kan du ange en **tidsstämpelavgränsare** som stöder poster som sträcker sig över mer än en rad.

Om en tidsstämpel avgränsare används fylls egenskapen TimeGenerated för varje post som lagras i Azure Monitor med det datum/den tid som angetts för posten i loggfilen.  Om en ny radavgränsare används fylls TimeGenerated med datum och tid då Azure Monitor samlade in posten.

1. Klicka på **Bläddra** och bläddra till en exempelfil.  Observera att den här knappen kan vara märkt **Välj fil** i vissa webbläsare.
2. Klicka på **Nästa**.
3. Guiden Anpassad logg överför filen och listar de poster som den identifierar.
4. Ändra avgränsaren som används för att identifiera en ny post och välj den avgränsare som bäst identifierar posterna i loggfilen.
5. Klicka på **Nästa**.

### <a name="step-3-add-log-collection-paths"></a>Steg 3. Lägga till sökvägar för loggsamling
Du måste definiera en eller flera sökvägar på agenten där den kan hitta den anpassade loggen.  Du kan antingen ange en specifik sökväg och ett visst namn för loggfilen eller ange en sökväg med ett jokertecken för namnet. Detta stöder program som skapar en ny fil varje dag eller när en fil når en viss storlek. Du kan också ange flera sökvägar för en enda loggfil.

Ett program kan till exempel skapa en loggfil varje dag med det datum som ingår i namnet som i log20100316.txt. Ett mönster för en sådan logg kan vara *log\*.txt* som skulle gälla för alla loggfiler enligt programmets namngivningsschema.

Följande tabell innehåller exempel på giltiga mönster för att ange olika loggfiler.

| Beskrivning | Sökväg |
|:--- |:--- |
| Alla filer i *C:\Loggar* med TXT-tillägget på Windows-agenten |C:\Loggar\\\*.txt |
| Alla filer i *C:\Loggar* med ett namn som börjar med logg och ett TXT-tillägg på Windows-agent |C:\Loggar\log\*.txt |
| Alla filer i */var/log/audit* med .txt-tillägg på Linux-agenten |/var/log/audit/*.txt |
| Alla filer i */var/log/audit* med ett namn som börjar med logg och ett .txt-tillägg på Linux-agent |/var/log/audit/log\*.txt |

1. Välj Windows eller Linux för att ange vilket sökvägsformat du lägger till.
2. Skriv in banan och **+** klicka på knappen.
3. Upprepa processen för ytterligare sökvägar.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Steg 4. Ange ett namn och en beskrivning för loggen
Namnet som du anger används för loggtypen enligt beskrivningen ovan.  Det kommer alltid att sluta med _CL att skilja det som en anpassad logg.

1. Skriv in ett namn på loggen.  CL-suffixet tillhandahålls automatiskt. ** \_**
2. Lägg till en valfri **beskrivning**.
3. Klicka på **Nästa** om du vill spara den anpassade loggdefinitionen.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Steg 5. Verifiera att de anpassade loggarna samlas in
Det kan ta upp till en timme innan de första data från en ny anpassad logg visas i Azure Monitor.  Det börjar samla in poster från loggarna som finns i sökvägen som du angav från den punkt där du definierade den anpassade loggen.  Den kommer inte att behålla de poster som du laddade upp under den anpassade loggen skapas, men det kommer att samla in redan befintliga poster i loggfiler som den hittar.

När Azure Monitor börjar samla in från den anpassade loggen är dess poster tillgängliga med en loggfråga.  Använd namnet som du gav den anpassade loggen som **typ** i frågan.

> [!NOTE]
> Om rawdata-egenskapen saknas i frågan kan du behöva stänga och öppna webbläsaren igen.

### <a name="step-6-parse-the-custom-log-entries"></a>Steg 6. Tolka de anpassade loggposterna
Hela loggposten lagras i en enda egenskap som heter **RawData**.  Du kommer sannolikt att vilja separera de olika delarna av information i varje post i enskilda egenskaper för varje post. Se [Textdata för parsa i Azure Monitor](../log-query/parse-text.md) för alternativ för att tolka **RawData** i flera egenskaper.

## <a name="removing-a-custom-log"></a>Ta bort en anpassad logg
Använd följande process i Azure-portalen för att ta bort en anpassad logg som du tidigare har definierat.

1. På **Data-menyn** på **arbetsytan Avancerade inställningar** väljer du **Anpassade loggar** för att visa alla dina anpassade loggar.
2. Klicka på **Ta bort** bredvid den anpassade loggen som du vill ta bort.

## <a name="data-collection"></a>Datainsamling
Azure Monitor samlar in nya poster från varje anpassad logg ungefär var femte minut.  Agenten registrerar sin plats i varje loggfil som den samlar in från.  Om agenten går offline under en viss tid samlar Azure Monitor in poster från där den senast slutade, även om dessa poster skapades medan agenten var offline.

Hela innehållet i loggposten skrivs till en enda egenskap som heter **RawData**.  Se [Tolka textdata i Azure Monitor](../log-query/parse-text.md) för metoder för att tolka varje importerad loggpost till flera egenskaper.

## <a name="custom-log-record-properties"></a>Egenskaper för anpassad loggpost
Anpassade loggposter har en typ med det loggnamn som du anger och egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| TimeGenerated |Datum och tid då posten samlades in av Azure Monitor.  Om loggen använder en tidsbaserad avgränsare är det här den tid som samlas in från transaktionen. |
| SourceSystem |Typ av agent som posten hämtades från. <br> OpsManager – Windows-agent, antingen direktanslutning eller System Center Operations Manager <br> Linux – Alla Linux-agenter |
| RawData (rådata) |Fulltext av den insamlade posten. Du kommer troligen att vilja [tolka dessa data i enskilda egenskaper](../log-query/parse-text.md). |
| ManagementGroupName |Namn på hanteringsgruppen för System Center Operations Manage-agenter.  För andra agenter är detta\<AOI- arbetsyte-ID\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Exempel på genomgången för att lägga till en anpassad logg
I följande avsnitt går du igenom ett exempel på hur du skapar en anpassad logg.  Exempelloggen som samlas in har en enda post på varje rad som börjar med ett datum och en tid och sedan kommaavgränsade fält för kod, status och meddelande.  Flera exempelposter visas nedan.

    2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2019-08-27 01:38:22 302,Error,Application could not connect to database
    2019-08-27 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Ladda upp och tolka en exempellogg
Vi tillhandahåller en av loggfilerna och kan se de händelser som den kommer att samla in.  I detta fall är ny linje en tillräcklig avgränsare.  Om en enskild post i loggen kan sträcka sig över flera rader måste en tidsstämpel avgränsare användas.

![Ladda upp och tolka en exempellogg](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Lägga till sökvägar för loggsamling
Loggfilerna finns i *C:\MyApp\Logs*.  En ny fil skapas varje dag med ett namn som innehåller datumet i *mönsterappenYYYYMMDD.log*.  Ett tillräckligt mönster för den här loggen skulle vara *C:\MyApp\Logs\\\*.log*.

![Sökväg till loggsamling](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Ange ett namn och en beskrivning för loggen
Vi använder ett namn *på MyApp_CL* och skriver in en **beskrivning**.

![Loggnamn](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Verifiera att de anpassade loggarna samlas in
Vi använder en enkel fråga *om MyApp_CL* för att returnera alla poster från den insamlade loggen.

![Loggfråga utan anpassade fält](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternativ till anpassade loggar
Även om anpassade loggar är användbara om dina data passar de kriterier som anges om, men det finns fall som följande där du behöver en annan strategi:

- Data passar inte den struktur som krävs, till exempel att ha tidsstämpeln i ett annat format.
- Loggfilen uppfyller inte krav som filkodning eller en mappstruktur som inte stöds.
- Data kräver förbearbetning eller filtrering före insamling. 

I de fall där dina data inte kan samlas in med anpassade loggar bör du tänka på följande alternativa strategier:

- Använd ett anpassat skript eller annan metod för att skriva data till [Windows-händelser](data-sources-windows-events.md) eller [Syslog](data-sources-syslog.md) som samlas in av Azure Monitor. 
- Skicka data direkt till Azure Monitor med [HTTP Data Collector API](data-collector-api.md). 

## <a name="next-steps"></a>Nästa steg
* Se [Tolka textdata i Azure Monitor](../log-query/parse-text.md) för metoder för att tolka varje importerad loggpost till flera egenskaper.
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) för att analysera data som samlas in från datakällor och lösningar.
