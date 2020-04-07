---
title: Skapa prestandaspårningar på klientsidan
description: Metodtips för prestandaprofilering på klientsidan med WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1f4207a11f3ae3664023fccf6178b6db7cf253b9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681316"
---
# <a name="create-client-side-performance-traces"></a>Skapa prestandaspårningar på klientsidan

Det finns många anledningar till att prestanda för Azure Remote Rendering kanske inte är så bra som önskat. Bortsett från ren renderingsprestanda på molnservern, särskilt kvaliteten på nätverksanslutningen har en betydande inverkan på upplevelsen. Information om hur du profilerar serverns prestanda finns i [prestandafrågor på serversidan](../overview/features/performance-queries.md)på serversidan .

Det här kapitlet fokuserar på hur du identifierar potentiella flaskhalsar på klientsidan genom *prestandaspårningar*.

## <a name="getting-started"></a>Komma igång

Om du inte har en ny användning av prestandaspårningsfunktionen i Windows kommer det här avsnittet att nämna de mest grundläggande termer och program som hjälper dig att komma igång.

### <a name="installation"></a>Installation

De program som används för att spåra med ARR är verktyg för allmänna ändamål som kan användas för all Windows-utveckling. De tillhandahålls via [Windows Performance Toolkit](https://docs.microsoft.com/windows-hardware/test/wpt/). Hämta Windows Assessment and Deployment Kit för att hämta den här [verktygslådan](https://docs.microsoft.com/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminologi

När du söker efter information om prestandaspår, kommer du oundvikligen att stöta på en rad olika termer. De viktigaste är:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** står för [ **E**vent **T**racing för **W**indows](https://docs.microsoft.com/windows/win32/etw/about-event-tracing). Det är helt enkelt det övergripande namnet för den effektiva spårningsanläggningen på kärnnivå som är inbyggd i Windows. Det kallas *händelsespårning,* eftersom program som stöder ETW kommer att avge händelser för att logga åtgärder som kan hjälpa till att spåra prestandaproblem. Som standard avger operativsystemet redan händelser för saker som diskåtkomster, uppgiftsväxlar och sådant. Program som ARR avger dessutom anpassade händelser, till exempel om tappade ramar, nätverksfördröjning etc.

**ETL** står för **E**vent **T**race **L**ogging. Det betyder helt enkelt att en spårning har samlats in (loggat) och därför vanligtvis används som filnamnstillägg för filer som lagrar spårningsdata. Således när du gör ett spår, \*kommer du vanligtvis att ha en .etl fil efteråt.

**WPR** står för [ **W**indows **P**erformance **R**ecorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) och är namnet på det program som startar och stoppar registreringen av händelsespårningar. WPR tar en\*profilfil ( .wprp) som konfigurerar vilka exakta händelser som ska loggas. En `wprp` sådan fil är försedd med ARR SDK. När du gör spår på en stationär dator kan du starta WPR direkt. När du gör ett spår på HoloLens går du vanligtvis igenom webbgränssnittet istället.

**WPA** står för [ **W**indows **P**erformance **A**nalyzer](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) och är \*namnet på GUI-programmet som används för att öppna .etl-filer och sålla igenom data för att identifiera prestandaproblem. WPA kan du sortera data efter olika kriterier, visa data på flera sätt, gräva ner i detaljer och korrelera information.

Medan ETL-spår kan skapas på alla Windows-enheter (lokal dator, HoloLens, molnserver osv.), sparas de vanligtvis på disken och analyseras med WPA på en stationär dator. ETL filer kan skickas till andra utvecklare för dem att ta en titt. Tänk på att känslig information, till exempel filsökvägar och IP-adresser, kan fångas in i ETL-spår. Du kan använda ETW på två sätt: för att registrera spårningar eller för att analysera spårningar. Inspelningsspårningar är rakt fram och kräver minimal installation. Analysera spår å andra sidan kräver en anständig förståelse av både WPA verktyget och problemet som du undersöker. Allmänt material för lärande WPA kommer att ges nedan, samt riktlinjer för hur man ska tolka ARR-specifika spår.

## <a name="recording-a-trace-on-a-local-pc"></a>Spela in ett spår på en lokal dator

För att identifiera ARR-prestandaproblem bör du föredra att göra ett spår direkt på en HoloLens, eftersom det är det enda sättet att få en ögonblicksbild av de verkliga prestandaegenskaperna. Men om du specifikt vill göra ett spår utan HoloLens prestandabegränsningar eller om du bara vill lära dig att använda WPA och inte behöver ett realistiskt spår, här är hur man gör det.

### <a name="wpr-configuration"></a>WPR-konfiguration

1. Starta [Prestandainspelaren](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) för Windows från *startmenyn*.
1. Expandera **fler alternativ**
1. Klicka på **Lägg till profiler...**
1. Välj filen *AzureRemoteRenderingNetworkProfiling.wprp*. Du hittar den här filen i ARR SDK under *Verktyg/ETLProfiles*.
   Profilen kommer nu att listas i WPR under *Anpassade mått*. Kontrollera att det är den enda aktiverade profilen.
1. Expandera *första nivån triage:*
    * Om allt du vill göra är att fånga en snabb spårning av ARR-nätverkshändelser **inaktiverar du** det här alternativet.
    * Om du behöver korrelera ARR-nätverkshändelser med andra systemegenskaper, till exempel CPU eller minnesanvändning, **aktiverar** du det här alternativet.
    * Om du aktiverar det här alternativet kommer spårningen troligen att vara flera gigabyte i storlek och ta lång tid att spara och öppna i WPA.

Efteråt din WPR konfiguration ska se ut så här:

![WPR-konfiguration](./media/wpr.png)

### <a name="recording"></a>Inspelning

Klicka på **Start** för att börja spela in en spårning. Du kan starta och stoppa inspelningen när som helst. du behöver inte stänga programmet innan du gör det. Som du kan se behöver du inte ange vilket program som ska spåras, eftersom ETW alltid kommer att spela in en spårning för hela systemet. Filen `wprp` anger vilka typer av händelser som ska postas.

Klicka på **Spara** om du vill stoppa inspelningen och ange var ETL-filen ska lagras.

Du har nu en ETL-fil som du antingen kan öppna direkt i WPA eller skicka till någon annan.

## <a name="recording-a-trace-on-a-hololens"></a>Spela in ett spår på en HoloLens

Om du vill spela in ett spår på en HoloLens startar du upp enheten och anger dess IP-adress i en webbläsare för att öppna *Enhetsportalen*.

![Enhetsportal](./media/wpr-hl.png)

1. Till vänster navigerar du till *Prestanda > Prestandaspårning*.
1. Välj **anpassade profiler**
1. Klicka på **Bläddra...**
1. Välj filen *AzureRemoteRenderingNetworkProfiling.wprp*. Du hittar den här filen i ARR SDK under *Verktyg/ETLProfiles*.
1. Klicka på **Starta spårning**
1. HoloLens spelar nu in ett spår. Se till att utlösa prestandaproblem som du vill undersöka. Klicka sedan på **Stoppa spårning**.
1. Spårningen visas längst ned på webbsidan. Klicka på diskikonen till höger för att hämta ETL-filen.

Du har nu en ETL-fil som du antingen kan öppna direkt i WPA eller skicka till någon annan.

## <a name="analyzing-traces-with-wpa"></a>Analysera spår med WPA

### <a name="wpa-basics"></a>Grunderna i WPA

Windows Performance Analyzer är standardverktyget för att öppna ETL-filer och inspektera spårningarna. En förklaring hur WPA fungerar är utanför räckvidden för den här artikeln. Kom igång genom att ta en titt på dessa resurser:

* Titta på [introduktionsvideorna](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) för en första översikt.
* WPA har själv en komma igång-flik, vilket förklarar vanliga steg. *Getting Started* Ta en titt på de tillgängliga ämnena. Speciellt under "Visa data" får du en snabb introduktion hur du skapar grafer för specifika data.
* Det finns utmärkt information [på denna webbplats,](https://randomascii.wordpress.com/2015/09/24/etw-central/)men inte allt är relevant för nybörjare.

### <a name="graphing-data"></a>Diagramdata

För att komma igång med ARR spårning, följande bitar är bra att veta.

![Diagram över prestanda](./media/wpa-graph.png)

Bilden ovan visar en tabell med spårningsdata och en diagramrepresentation av samma data.

I tabellen längst ner noterar du den gula (gyllene) stapeln och den blå stapeln. Du kan dra dessa barer och placera dem på någon position.

Alla **kolumner till vänster om den gula stapeln** tolkas som **tangenter**. Nycklar används för att strukturera trädet i det övre vänstra fönstret. Här har *key* vi två nyckelkolumner, "Provider Name" och "Uppgiftsnamn". Därför är trädstrukturen i det övre vänstra fönstret två nivåer djupa. Om du ändrar ordning på kolumnerna eller lägger till eller tar bort kolumner från nyckelområdet ändras strukturen i trädvyn.

**Kolumner till höger om den blå stapeln** används för **grafvisningen** i det övre högra fönstret. För det mesta används bara den första kolumnen, men vissa diagramlägen kräver flera kolumner med data. För att linjediagram ska fungera måste *aggregeringsläget* på den kolumnen ställas in. Använd "Medel" eller "Max". Aggregeringsläget används för att bestämma diagrammets värde vid en viss pixel när en pixel täcker ett område med flera händelser. Detta kan observeras genom att ställa in aggregering till "Summa" och sedan zooma in och ut.

Kolonnerna i mitten har ingen speciell betydelse.

![Vy över händelser](./media/wpa-event-view.png)

I *Redigeraren för allmänna händelservy* kan du konfigurera alla kolumner så att de visas, aggregeringsläget, sortering och vilka kolumner som används som nycklar eller för grafering. I exemplet ovan är **fält 2** aktiverat och fält 3 - 6 är inaktiverade. Fält 2 är vanligtvis det första *anpassade datafältet* i en ETW-händelse och därmed för ARR "FrameStatistics"-händelser, som representerar ett visst nätverksfördröjningsvärde. Aktivera andra fältkolumner för att se ytterligare värden för den här händelsen.

### <a name="presets"></a>Förinställningar

För att korrekt analysera en spårning måste du ta reda på ditt eget arbetsflöde och önskad datavisning. Men för att kunna få en snabb överblick över ARR-specifika händelser, inkluderar vi Windows Software Protection Platform profil och förinställningar filer i mappen *Tools / ETLProfiles*. Om du vill läsa in en hel profil väljer du *Profiler > Använd...* på WPA-menyraden eller öppnar panelen *Mina förinställningar* (*Fönster > Mina förinställningar)* och väljer *Importera*. Den förra kommer att ställa in en komplett WPA konfiguration som i bilden nedan. Den senare kommer bara att göra förinställningar för de olika vykonfigurationerna tillgängliga och gör att du snabbt kan öppna en vy för att titta på en viss del av ARR-händelsedata.

![Förinställningar](./media/wpa-arr-trace.png)

Bilden ovan visar vyer av olika ARR-specifika händelser plus en vy över den totala CPU-användningen.

## <a name="next-steps"></a>Nästa steg

* [Prestandafrågor på serversidan](../overview/features/performance-queries.md)
