---
title: Kontinuerlig export av telemetri från Application Insights | Microsoft-dokument
description: Exportera diagnostik- och användningsdata till lagring i Microsoft Azure och hämta dem därifrån.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: f6afe42e483ab7ad5810169fc301946c75308c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298291"
---
# <a name="export-telemetry-from-application-insights"></a>Exportera telemetri från Application Insights
Vill du behålla telemetrin längre än standardlagringsperioden? Eller bearbeta det på något specialiserat sätt? Kontinuerlig export är idealisk för detta. De händelser som visas i application insights-portalen kan exporteras till lagring i Microsoft Azure i JSON-format. Därifrån kan du ladda ner dina data och skriva vilken kod du behöver för att bearbeta den.  

Innan du ställer in kontinuerlig export finns det några alternativ som du kanske vill överväga:

* Med knappen Exportera högst upp på en mått- eller sökflik kan du överföra tabeller och diagram till ett Excel-kalkylblad.

* [Analytics](../../azure-monitor/app/analytics.md) ger ett kraftfullt frågespråk för telemetri. Det kan också exportera resultat.
* Om du vill [utforska dina data i Power BI](../../azure-monitor/app/export-power-bi.md )kan du göra det utan att använda Kontinuerlig export.
* Med [REST API:et](https://dev.applicationinsights.io/) för dataåtkomst kan du komma åt telemetrin programmässigt.
* Du kan också komma åt kontinuerlig [export genom Powershell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

När kontinuerlig export har kopierat dina data till lagring (där de kan stanna så länge du vill) är de fortfarande tillgängliga i Application Insights för den vanliga [kvarhållningsperioden](../../azure-monitor/app/data-retention-privacy.md).

## <a name="continuous-export-advanced-storage-configuration"></a>Avancerad lagringskonfiguration för kontinuerlig export

Kontinuerlig export **stöder inte** följande Azure-lagringsfunktioner/konfigurationer:

* Användning av [VNET/Azure Storage-brandväggar](https://docs.microsoft.com/azure/storage/common/storage-network-security) tillsammans med Azure Blob-lagring.

* [Oföränderlig lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) för Azure Blob-lagring.

* [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="create-a-continuous-export"></a><a name="setup"></a>Skapa en kontinuerlig export

1. Öppna Kontinuerlig export i application insights-resursen för appen under konfigurera till vänster och välj **Lägg till:**

2. Välj de telemetridatatyper som du vill exportera.

3. Skapa eller välj ett [Azure-lagringskonto](../../storage/common/storage-introduction.md) där du vill lagra data. Mer information om lagringspriseralternativ finns på den [officiella prissidan](https://azure.microsoft.com/pricing/details/storage/).

     Klicka på Lägg till, Exportera mål, lagringskonto och skapa sedan en ny butik eller välj en befintlig butik.

    > [!Warning]
    > Som standard anges lagringsplatsen till samma geografiska region som application insights-resursen. Om du lagrar i en annan region kan du debiteras överföringsavgifter.

4. Skapa eller markera en behållare i lagringen.

När du har skapat din export börjar den gå. Du får bara data som kommer när du har skapat exporten.

Det kan finnas en fördröjning på ungefär en timme innan data visas i lagringen.

När den första exporten är klar hittar du en struktur som liknar följande i din Azure Blob-lagringsbehållare: (Detta varierar beroende på vilka data du samlar in.)

|Namn | Beskrivning |
|:----|:------|
| [Tillgänglighet](export-data-model.md#availability) | Rapporterar [webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md).  |
| [Händelse](export-data-model.md#events) | Anpassade händelser som genereras av [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent). 
| [Undantag](export-data-model.md#exceptions) |Rapporterar [undantag](../../azure-monitor/app/asp-net-exceptions.md) på servern och i webbläsaren.
| [Meddelanden](export-data-model.md#trace-messages) | Skickas av [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)och av [loggningsadaptrarna](../../azure-monitor/app/asp-net-trace-logs.md).
| [Statistik](export-data-model.md#metrics) | Genereras av mått API-anrop.
| [PerformanceCounters (PerformanceCounters)](export-data-model.md) | Prestandaräknare som samlas in av Application Insights.
| [Begäranden](export-data-model.md#requests)| Skickas av [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Standardmodulerna använder detta för att rapportera serverns svarstid, mätt på servern.| 

### <a name="to-edit-continuous-export"></a>Så här redigerar du kontinuerlig export

Klicka på kontinuerlig export och välj lagringskontot att redigera.

### <a name="to-stop-continuous-export"></a>Så här stoppar du kontinuerlig export

Om du vill stoppa exporten klickar du på Inaktivera. När du klickar på Aktivera igen startas exporten om med nya data. Du får inte de data som kom i portalen när exporten inaktiverades.

Om du vill stoppa exporten permanent tar du bort den. Om du gör det tas inte dina data bort från lagringen.

### <a name="cant-add-or-change-an-export"></a>Kan du inte lägga till eller ändra en export?
* Om du vill lägga till eller ändra exporten behöver du åtkomsträttigheter för ägare, deltagare eller programinsikter. [Läs mer om roller][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a>Vilka händelser får du?
De exporterade data är den råa telemetri vi får från ditt program, förutom att vi lägger till platsdata, som vi beräknar från klientens IP-adress.

Uppgifter som har kasserats genom [provtagning](../../azure-monitor/app/sampling.md) ingår inte i de exporterade uppgifterna.

Andra beräknade mått ingår inte. Vi exporterar till exempel inte genomsnittlig CPU-användning, men vi exporterar den råa telemetri som medelvärdet beräknas från.

Data innehåller också resultaten av alla [webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md) som du har ställt in.

> [!NOTE]
> **Provtagning.** Om ditt program skickar mycket data kan samplingsfunktionen fungera och skicka endast en bråkdel av den genererade telemetrin. [Läs mer om sampling.](../../azure-monitor/app/sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a>Kontrollera data
Du kan inspektera lagringen direkt i portalen. Klicka på startsidan i menyn längst till vänster, högst upp där det står "Azure-tjänster" väljer **du Lagringskonton**, väljer lagringskontonamnet, väljer **Blobbar** under tjänster på översiktssidan och väljer slutligen behållarnamnet.

Om du vill granska Azure-lagring i Visual Studio öppnar du **Visa**, **Cloud Explorer**. (Om du inte har det menykommandot måste du installera Azure SDK: Öppna dialogrutan **Nytt projekt,** expandera Visual C#/Cloud och välja **Hämta Microsoft Azure SDK för .NET**.)

När du öppnar blob-arkivet visas en behållare med en uppsättning blob-filer. URI för varje fil som härleds från ditt Application Insights-resursnamn, dess instrumenteringsnyckel, telemetrityp/datum/tid. (Resursnamnet är gemener och instrumenteringsnyckeln utelämnar streck.)

![Inspektera blob-förrådet med ett lämpligt verktyg](./media/export-telemetry/04-data.png)

Datum och tid är UTC och är när telemetrin deponerades i butiken - inte den tid det genererades. Så om du skriver kod för att ladda ner data, kan det flytta linjärt genom data.

Här är formen på sökvägen:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Var

* `blobCreationTimeUtc`är den tid då blob skapades i den interna mellanlagringslagringen
* `blobDeliveryTimeUtc`är den tidpunkt då blob kopieras till lagring av exportmål

## <a name="data-format"></a><a name="format"></a>Dataformat
* Varje blob är en textfil som innehåller flera '\n'-separerade rader. Den innehåller telemetri som bearbetas under en tidsperiod på ungefär en halv minut.
* Varje rad representerar en telemetridatapunkt, till exempel en begäran eller sidvy.
* Varje rad är ett oformaterat JSON-dokument. Om du vill sitta och stirra på den öppnar du den i Visual Studio och väljer Redigera, Avancerad, Formatera fil:

![Visa telemetrin med ett lämpligt verktyg](./media/export-telemetry/06-json.png)

Tidslängder är i fästingar, där 10 000 fästingar = 1 ms. Dessa värden visar till exempel en tid på 1 ms för att skicka en begäran från webbläsaren, 3 ms för att ta emot den och 1,8 s för att bearbeta sidan i webbläsaren:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Detaljerad datamodellreferens för egenskapstyper och värden.](export-data-model.md)

## <a name="processing-the-data"></a>Behandling av data
I liten skala kan du skriva en del kod för att dra isär dina data, läsa dem i ett kalkylblad och så vidare. Ett exempel:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Ett större kodexempel finns [i använda en arbetsroll][exportasa].

## <a name="delete-your-old-data"></a><a name="delete"></a>Ta bort dina gamla data
Du ansvarar för att hantera din lagringskapacitet och ta bort gamla data om det behövs.

## <a name="if-you-regenerate-your-storage-key"></a>Om du återskapar lagringsnyckeln...
Om du ändrar nyckeln till ditt lagringsutrymme slutar kontinuerlig export att fungera. Du ser en avisering i ditt Azure-konto.

Öppna fliken Kontinuerlig export och redigera exporten. Redigera exportmålet, men lämna bara samma lagringsutrymme markerat. Klicka på OK för att bekräfta.

Den kontinuerliga exporten startas om.

## <a name="export-samples"></a>Exportera prover

* [Exportera till SQL med Stream Analytics][exportasa]
* [Exempel på Stream Analytics 2](export-stream-analytics.md)

På större skalor bör du tänka på [HDInsight](https://azure.microsoft.com/services/hdinsight/) - Hadoop-kluster i molnet. HDInsight tillhandahåller en mängd olika tekniker för att hantera och analysera stordata, och du kan använda den för att bearbeta data som har exporterats från Application Insights.

## <a name="q--a"></a>Vanliga frågor och svar
* *Men allt jag vill ha är en engångsnedladdning av ett diagram.*  

    Ja, det kan du göra. Klicka på **Exportera data**högst upp på fliken .
* *Jag har ordnat en export, men det finns inga data i mitt lager.*

    Har Application Insights fått någon telemetri från din app sedan du konfigurerade exporten? Du får bara nya data.
* *Jag försökte skapa en export, men nekades åtkomst*

    Om kontot ägs av din organisation måste du vara medlem i ägar- eller deltagargrupperna.
* *Kan jag exportera direkt till min egen butik?*

    Nej, förlåt. Vår exportmotor fungerar för närvarande bara med Azure-lagring just nu.  
* *Finns det någon gräns för hur mycket data du lägger i min butik?*

    Nej. Vi fortsätter att skicka in data tills du tar bort exporten. Vi slutar om vi når de yttre gränserna för bloblagring, men det är ganska stort. Det är upp till dig att kontrollera hur mycket lagringsutrymme du använder.  
* *Hur många blobbar ska jag se i lagret?*

  * För varje datatyp som du har valt att exportera skapas en ny blob varje minut (om data är tillgängliga).
  * Dessutom, för program med hög trafik, ytterligare partitionsenheter tilldelas. I det här fallet skapar varje enhet en blob varje minut.
* *Jag återskapade nyckeln till min lagring eller ändrade namnet på behållaren, och nu fungerar inte exporten.*

    Redigera exporten och öppna exportmålfliken. Lämna samma lagringsutrymme markerat som tidigare och klicka på OK för att bekräfta. Exporten startas om. Om ändringen var inom de senaste dagarna kommer du inte att förlora data.
* *Kan jag pausa exporten?*

    Ja. Klicka på Inaktivera.

## <a name="code-samples"></a>Kodexempel

* [Exempel på Stream Analytics](export-stream-analytics.md)
* [Exportera till SQL med Stream Analytics][exportasa]
* [Detaljerad datamodellreferens för egenskapstyper och värden.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
