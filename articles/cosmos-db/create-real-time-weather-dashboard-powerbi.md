---
title: Skapa en instrumentpanel i realtid med Azure Cosmos DB, Azure Analysis Services och Power BI
description: Lär dig hur du skapar en instrumentpanel för levande väder i Power BI med Azure Cosmos DB och Azure Analysis Services.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70376598"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Skapa en instrumentpanel i realtid med Azure Cosmos DB och Power BI

I den här artikeln beskrivs de steg som krävs för att skapa en instrumentpanel för levande väder i Power BI med Azure Cosmos DB och Azure Analysis Services. Power BI-instrumentpanelen visar diagram för att visa realtidsinformation om temperatur och nederbörd i en region.

## <a name="reporting-scenarios"></a>Rapporteringsscenarier

Det finns flera sätt att konfigurera rapporteringsinstrumentpaneler för data som lagras i Azure Cosmos DB. Beroende på kraven för inaktuella och datastorleken beskrivs i följande tabell rapporteringsinställningarna för varje scenario:


|Scenario |Installation |
|---------|---------|
|1. Generera ad hoc-rapporter (ingen uppdatering)    |  [Power BI Azure Cosmos DB-anslutning med importläge](powerbi-visualize.md)       |
|2. Generera ad hoc-rapporter med periodisk uppdatering   |  [Power BI Azure Cosmos DB-anslutning med importläge (schemalagd periodisk uppdatering)](powerbi-visualize.md)       |
|3. Rapportering om stora datamängder (< 10 GB)     |  Power BI Azure Cosmos DB-anslutning med inkrementell uppdatering       |
|4. Rapportering i realtid på stora datamängder    |  Power BI Azure Analysis Services-anslutning med direktfråga + Azure Analysis Services (Azure Cosmos DB-anslutning)       |
|5. Rapportering om livedata med aggregat     |  [Power BI Spark-anslutning med direktfråga + Azure Databricks + Cosmos DB Spark-anslutning.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Rapportering av livedata med aggregat på stora datamängder   |  Power BI Azure Analysis Services-anslutning med direktfråga + Azure Analysis Services + Azure Databricks + Cosmos DB Spark-anslutning.       |

Scenarier 1 och 2 kan enkelt konfigureras med Azure Cosmos DB Power BI-anslutningsappen. I den här artikeln beskrivs inställningarna för scenarierna 3 och 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI med inkrementell uppdatering

Power BI har ett läge där inkrementell uppdatering kan konfigureras. Det här läget eliminerar behovet av att skapa och hantera Azure Analysis Services-partitioner. Inkrementell uppdatering kan ställas in för att filtrera endast de senaste uppdateringarna i stora datauppsättningar. Det här läget fungerar dock bara med Power BI Premium-tjänst som har en datauppsättningsbegränsning på 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis connector + Azure Analysis Services

Azure Analysis Services tillhandahåller en fullständigt hanterad plattform som en tjänst som är värd för datamodeller i företagsklass i molnet. Massiva datauppsättningar kan läsas in från Azure Cosmos DB till Azure Analysis Services. För att undvika att fråga hela datauppsättningen hela tiden kan datauppsättningarna delas in i Azure Analysis Services-partitioner, som kan uppdateras oberoende av olika frekvenser.

## <a name="power-bi-incremental-refresh"></a>Power BI-stegvis uppdatering

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingest väderdata i Azure Cosmos DB

Konfigurera en inmatningspipeline för att läsa in [väderdata](https://catalog.data.gov/dataset/local-weather-archive) till Azure Cosmos DB. Du kan ställa in ett [ADF-jobb (Azure Data Factory)](../data-factory/connector-azure-cosmos-db.md) för att regelbundet läsa in de senaste väderdata i Azure Cosmos DB med hjälp av HTTP Source och Cosmos DB sink.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Ansluta Power BI till Azure Cosmos DB

1. **Anslut Azure Cosmos-kontot till Power BI** – Öppna Power BI Desktop och använd Azure Cosmos DB-anslutningsappen för att välja rätt databas och behållare.

   ![Azure Cosmos DB Power BI-anslutningsprogram](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Konfigurera inkrementell uppdatering** – Följ stegen i [inkrementell uppdatering med Power BI-artikel](/power-bi/service-premium-incremental-refresh) för att konfigurera inkrementell uppdatering för datauppsättningen. Lägg till parametrarna **RangeStart** och **RangeEnd** enligt följande skärmbild:

   ![Konfigurera intervallparametrar](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Eftersom datauppsättningen har en datumkolumn i textform bör parametrarna **RangeStart** och **RangeEnd** omvandlas för att använda följande filter. I fönstret **Avancerad redigerare** ändrar du frågan lägga till följande text för att filtrera raderna baserat på parametrarna RangeStart och RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   Beroende på vilken kolumn och datatyp som finns i källdatauppsättningen kan du ändra fälten RangeStart och RangeEnd i enlighet med

   
   |Egenskap  |Datatyp  |Filter  |
   |---------|---------|---------|
   |_ts     |   Numerisk      |  [_ts] > Duration.TotalSeconds(RangeStart – #datetime(1970, 1, 0, 0, 0)) och [_ts] < Duration.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0, 0)))       |
   |Datum (till exempel:- 2019-08-19)     |   String      | [Document.date]> DateTime.ToText(RangeStart,"yyyy-MM-dd") och [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |Datum (till exempel:- 2019-08-11 12:00:00)   |  String       |  [Document.date]> DateTime.ToText(RangeStart", yyyy-mm-dd HH:mm:ss") och [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-dd HH:mm:ss")       |


1. **Definiera uppdateringsprincipen** - Definiera uppdateringsprincipen genom att navigera till fliken **Inkrementell uppdatering** på **snabbmenyn** för tabellen. Ange uppdateringsprincipen så att den uppdateras **varje dag** och lagra den senaste månadens data.

   ![Definiera uppdateringsprincip](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Ignorera varningen som säger att *M-frågan inte kan bekräftas vara vikt*. Azure Cosmos DB-kopplingen viker filterfrågor.

1. **Ladda data och generera rapporterna** - Genom att använda de data som du har läst in tidigare, skapa diagram för att rapportera om temperatur och nederbörd.

   ![Ladda data och generera rapport](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Publicera rapporten till Power BI premium** - Eftersom inkrementell uppdatering är en premiumfunktion tillåter publiceringsdialogrutan endast val av en arbetsyta med Premium-kapacitet. Den första uppdateringen kan ta längre tid eftersom historiska data importeras. Efterföljande datauppdateringar går mycket snabbare eftersom de använder inkrementell uppdatering.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis connector + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingest väderdata i Azure Cosmos DB 

Konfigurera en inmatningspipeline för att läsa in [väderdata](https://catalog.data.gov/dataset/local-weather-archive) till Azure Cosmos DB. Du kan ställa in ett Azure Data Factory(ADF) jobb för att regelbundet läsa in de senaste väderdata i Azure Cosmos DB med hjälp av HTTP Source och Cosmos DB Sink.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Ansluta Azure Analysis Services till Azure Cosmos-konto

1. **Skapa ett nytt Azure Analysis Services-kluster** - [Skapa en instans av Azure Analysis-tjänster](../analysis-services/analysis-services-create-server.md) i samma region som Azure Cosmos-kontot och Databricks-klustret.

1. **Skapa ett nytt Analysis Services-tabellprojekt i Visual Studio** -  [Installera SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) och skapa ett analysis services-tabellprojekt i Visual Studio.

   ![Skapa Azure Analysis Services-projekt](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Välj den **integrerade arbetsytan** och ange kompatibilitetsnivå till **SQL Server 2017 / Azure Analysis Services (1400)**

   ![Tabellmodelldesignern i Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Lägg till Azure Cosmos DB-datakällan** - Navigera till **modeller**> **datakällor** > **Ny datakälla** och lägg till Azure Cosmos DB-datakällan enligt följande skärmbild:

   ![Lägg till Cosmos DB-datakälla](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Anslut till Azure Cosmos DB genom att ange **kontot URI,** **databasnamn**och **behållarnamnet**. Du kan nu se data från Azure Cosmos-behållaren importeras till Power BI.

   ![Förhandsgranska Azure Cosmos DB-data](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Konstruera Analysis Services-modellen** - Öppna frågeredigeraren, utför de åtgärder som krävs för att optimera den inlästa datauppsättningen:

   * Extrahera endast väderrelaterade kolumner (temperatur och nederbörd)

   * Extrahera månadsinformationen från tabellen. Dessa data är användbara när du skapar partitioner enligt beskrivningen i nästa avsnitt.

   * Konvertera temperaturkolumnerna till tal

   Det resulterande M-uttrycket är följande:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Ändra dessutom datatypen för temperaturkolumnerna till Decimal för att se till att dessa värden kan ritas i Power BI.

1. **Skapa Azure Analysis-partitioner** - Skapa partitioner i Azure Analysis Services för att dela upp datauppsättningen i logiska partitioner som kan uppdateras oberoende av oberoende och vid olika frekvenser. I det här exemplet skapar du två partitioner som skulle dela upp datauppsättningen i den senaste månadens data och allt annat.

   ![Skapa partitioner för analystjänster](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Skapa följande två partitioner i Azure Analysis Services:

   * **Senaste månaden** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Historiska** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Distribuera modellen till Azure Analysis Server** - Högerklicka på Azure Analysis Services-projektet och välj **Distribuera**. Lägg till servernamnet i **egenskapsfönstret Distributionsserver.**

   ![Distribuera Azure Analysis Services-modellen](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Konfigurera partitionsuppdateringar och sammanslagningar** – Azure Analysis Services tillåter oberoende bearbetning av partitioner. Eftersom vi vill att den **senaste månaden** partitionen ska uppdateras kontinuerligt med de senaste uppgifterna, ställa in uppdateringsintervallet till 5 minuter. Det krävs inte för att uppdatera data i historisk partition. Dessutom måste du skriva lite kod för att konsolidera den senaste månadspartitionen till den historiska partitionen och skapa en ny senaste månadspartition.


## <a name="connect-power-bi-to-analysis-services"></a>Ansluta Power BI till Analystjänster

1. **Anslut till Azure Analysis Server med Azure Analysis Services-databasanslutningen** – Välj **liveläge** och anslut till Azure Analysis Services-instansen enligt följande skärmbild:

   ![Hämta data från Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Ladda data och generera rapporter** - Genom att använda de data som du har läst in tidigare, skapa diagram för att rapportera om temperatur och nederbörd. Eftersom du skapar en live-anslutning bör frågorna köras på data i Azure Analysis Services-modellen som du har distribuerat i föregående steg. Temperaturdiagrammen uppdateras inom fem minuter efter att de nya data har lästs in i Azure Cosmos DB.

   ![Läsa in data och generera rapporter](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om Power BI finns i [Komma igång med Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Anslut Qlik Sense till Azure Cosmos DB och visualisera dina data](visualize-qlik-sense.md)