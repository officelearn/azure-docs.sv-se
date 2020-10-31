---
title: Skapa en instrument panel i real tid med hjälp av Azure Cosmos DB, Azure Analysis Services och Power BI
description: Lär dig hur du skapar en Live väder instrument panel i Power BI att använda Azure Cosmos DB och Azure Analysis Services.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/04/2019
ms.reviewer: sngun
ms.openlocfilehash: fc285599176057c57621dc6bfefbe9188d3badd7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096894"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Skapa en instrument panel i real tid med hjälp av Azure Cosmos DB och Power BI
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I den här artikeln beskrivs de steg som krävs för att skapa en Live väder instrument panel i Power BI att använda Azure Cosmos DB och Azure Analysis Services. Instrument panelen för Power BI visar diagram för att visa information i real tid om temperatur-och Rainfall i en region.

## <a name="reporting-scenarios"></a>Rapporterings scenarier

Det finns flera sätt att ställa in rapporterings instrument paneler på data som lagras i Azure Cosmos DB. Beroende på föråldrade krav och storleken på data, beskriver följande tabell rapporterings konfigurationen för varje scenario:


|Scenario |Installation |
|---------|---------|
|1. genererar ad hoc-rapporter (ingen uppdatering)    |  [Power BI Azure Cosmos DB anslutning med import läge](powerbi-visualize.md)       |
|2. genererar ad hoc-rapporter med periodisk uppdatering   |  [Power BI Azure Cosmos DB anslutning med import läge (schemalagd Periodisk uppdatering)](powerbi-visualize.md)       |
|3. rapportering på stora data mängder (< 10 GB)     |  Power BI Azure Cosmos DB anslutning med stegvis uppdatering       |
|4. rapportera real tid för stora data mängder    |  Power BI Azure Analysis Services-anslutning med direkt fråga + Azure Analysis Services (Azure Cosmos DB koppling)       |
|5. rapportering om real tids data med agg regeringar     |  [Power BI Spark-anslutning med direkt fråga + Azure Databricks + Cosmos DB Spark-anslutning.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. rapportering om real tids data med agg regeringar för stora data mängder   |  Power BI Azure Analysis Services Connector med direkt fråga + Azure Analysis Services + Azure Databricks + Cosmos DB Spark-anslutning.       |

Scenarier 1 och 2 kan enkelt konfigureras med hjälp av Azure Cosmos DB Power BI-anslutningen. I den här artikeln beskrivs inställningarna för scenarierna 3 och 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI med stegvis uppdatering

Power BI har ett läge där stegvis uppdatering kan konfigureras. Det här läget eliminerar behovet av att skapa och hantera Azure Analysis Services partitioner. Stegvis uppdatering kan ställas in så att endast de senaste uppdateringarna i stora data mängder filtreras. Det här läget fungerar dock bara med Power BI Premium tjänst som har en begränsning på 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis Connector + Azure Analysis Services

Azure Analysis Services tillhandahåller en fullständigt hanterad plattform som en tjänst som är värd för data modeller i företags klass i molnet. Enorma data uppsättningar kan läsas in från Azure Cosmos DB till Azure Analysis Services. För att undvika att fråga hela data uppsättningen hela tiden kan data uppsättningarna delas upp i Azure Analysis Services partitioner, vilka kan uppdateras oberoende av olika frekvenser.

## <a name="power-bi-incremental-refresh"></a>Power BI stegvis uppdatering

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Mata in väder data i Azure Cosmos DB

Konfigurera en pipeline för inmatning för att läsa in [väder data](https://catalog.data.gov/dataset/local-weather-archive/resource/c28974a2-fc83-4722-8977-9a701323f729) till Azure Cosmos dB. Du kan konfigurera ett [Azure Data Factory (ADF)-](../data-factory/connector-azure-cosmos-db.md) jobb för att regelbundet läsa in de senaste väder uppgifterna i Azure Cosmos dB med http-källan och Cosmos DB mottagare.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Anslut Power BI till Azure Cosmos DB

1. **Anslut Azure Cosmos-kontot till Power BI** – öppna Power BI Desktop och Använd Azure Cosmos DB Connector för att välja rätt databas och behållare.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png" alt-text="Azure Cosmos DB Power BI-anslutningsprogram":::

1. **Konfigurera stegvis uppdatering** – Följ stegen i [stegvis uppdatering med Power BI](/power-bi/service-premium-incremental-refresh) artikel för att konfigurera stegvis uppdatering för data uppsättningen. Lägg till parametrarna **Ranging** och **RangeEnd** enligt följande skärm bild:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png" alt-text="Azure Cosmos DB Power BI-anslutningsprogram" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   Beroende på vilken kolumn och datatyp som finns i käll data uppsättningen kan du ändra fälten rang Est art och RangeEnd enligt detta

   
   |Egenskap  |Datatyp  |Filtrera  |
   |---------|---------|---------|
   |_ts     |   Numeriskt      |  [_ts] > varaktighet. TotalSeconds (rang-The-#datetime (1970, 1,0) och [_ts] < duration. TotalSeconds (RangeEnd-#datetime (1970, 1, 0, 0)))       |
   |Datum (till exempel:-2019-08-19)     |   Sträng      | [Document. Date] > DateTime. ToText (rang, "åååå-MM-DD") och [Document. Date] < DateTime. ToText (RangeEnd, "åååå-MM-DD")        |
   |Datum (till exempel:-2019-08-11 12:00:00)   |  Sträng       |  [Document. Date] > DateTime. ToText (rang, "åååå-mm-dd HH: mm: SS") och [Document. Date] < DateTime. ToText (RangeEnd, "åååå-mm-dd HH: mm: SS")       |


1. **Definiera uppdaterings principen** – definiera uppdaterings principen genom att gå till fliken **stegvis uppdatering** på **snabb** menyn för tabellen. Ange att uppdaterings principen ska uppdateras **varje dag** och lagra senaste månads information.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png" alt-text="Azure Cosmos DB Power BI-anslutningsprogram":::

   Ignorera varningen som säger att *M-frågan inte kan bekräftas vara viktad* . Azure Cosmos DB Connector viker filter frågor.

1. **Läs in data och generera rapporter** – genom att använda de data som du har läst in tidigare skapar du diagrammen för att rapportera om temperatur-och Rainfall.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Azure Cosmos DB Power BI-anslutningsprogram":::

1. **Publicera rapporten till Power BI Premium** – eftersom den stegvisa uppdateringen är en Premium-funktion kan du bara välja en arbets yta på Premium-kapacitet i dialog rutan publicera. Den första uppdateringen kan ta längre tid eftersom historiska data importeras. Efterföljande data uppdateringar är mycket snabbare eftersom de använder stegvis uppdatering.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis Connector + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Mata in väder data i Azure Cosmos DB 

Konfigurera en pipeline för inmatning för att läsa in [väder data](https://catalog.data.gov/dataset/local-weather-archive/resource/c28974a2-fc83-4722-8977-9a701323f729) till Azure Cosmos dB. Du kan konfigurera ett Azure Data Factory (ADF)-jobb för att regelbundet läsa in de senaste väder uppgifterna i Azure Cosmos DB med HTTP-källan och Cosmos DB mottagare.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Anslut Azure Analysis Services till Azure Cosmos-konto

1. **Skapa ett nytt Azure Analysis Services-kluster**  -  [Skapa en instans av Azure Analysis Services](../analysis-services/analysis-services-create-server.md) i samma region som Azure Cosmos-kontot och Databricks-klustret.

1. **Skapa ett nytt Analysis Services tabell projekt i Visual Studio**  -   [Installera SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017&preserve-view=true) och skapa ett Analysis Services tabell projekt i Visual Studio.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png" alt-text="Azure Cosmos DB Power BI-anslutningsprogram":::

   Välj den **integrerade arbets ytans** instans och Ange kompatibilitetsnivån till **SQL Server 2017/Azure Analysis Services (1400)**

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png" alt-text="Azure Cosmos DB Power BI-anslutningsprogram":::

1. **Lägg till Azure Cosmos db data källa** – navigera till **modeller** >  **data källor**  >  **ny data källa** och Lägg till Azure Cosmos db data källan som visas på följande skärm bild:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/add-data-source.png" alt-text="Azure Cosmos DB Power BI-anslutningsprogram":::

   Anslut till Azure Cosmos DB genom att ange **konto-URI** , **databas namn** och **behållar namn** . Nu kan du se data från Azure Cosmos-behållaren som importeras till Power BI.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png" alt-text="Azure Cosmos DB Power BI-anslutningsprogram":::

1. **Skapa Analysis Servicess modellen** – öppna Frågeredigeraren och utför de åtgärder som krävs för att optimera den inlästa data uppsättningen:

   * Extrahera endast väder relaterade kolumner (temperatur och Rainfall)

   * Extrahera månads informationen från tabellen. Dessa data är användbara när du skapar partitioner enligt beskrivningen i nästa avsnitt.

   * Omvandla temperatur kolumnerna till tal

   Det resulterande M-uttrycket ser ut så här:

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

   Ändra dessutom data typen för temperatur kolumnerna till decimal för att se till att dessa värden kan ritas i Power BI.

1. **Skapa Azure Analysis-partitioner** – skapa partitioner i Azure Analysis Services för att dela upp data uppsättningen i logiska partitioner som kan uppdateras oberoende av varandra och med olika frekvenser. I det här exemplet skapar du två partitioner som delar upp data uppsättningen i den senaste månadens data och allt annat.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png" alt-text="Azure Cosmos DB Power BI-anslutningsprogram" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Historik** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Distribuera modellen till Azure Analysis Server** -högerklicka på Azure Analysis Services projektet och välj **distribuera** . Lägg till Server namnet i rutan **Egenskaper för distributions Server** .

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png" alt-text="Azure Cosmos DB Power BI-anslutningsprogram":::

1. **Konfigurera partition uppdateringar och sammanfogningar** – Azure Analysis Services tillåter oberoende bearbetning av partitioner. Eftersom vi vill att den **senaste månads** partitionen ska uppdateras kontinuerligt med de senaste data ställer du in uppdaterings intervallet på 5 minuter. Du kan uppdatera data med hjälp av [REST API](../analysis-services/analysis-services-async-refresh.md), [Azure Automation](../analysis-services/analysis-services-refresh-azure-automation.md)eller med en [Logic app](../analysis-services/analysis-services-refresh-logic-app.md). Det är inte nödvändigt att uppdatera data i historisk partition. Dessutom måste du skriva kod för att konsolidera den senaste month-partitionen till den historiska partitionen och skapa en ny senaste månads partition.

## <a name="connect-power-bi-to-analysis-services"></a>Anslut Power BI till Analysis Services

1. **Anslut till Azure-Analysis Server med hjälp av Azure Analysis Services Database Connector** – Välj **Live-läge** och Anslut till Azure Analysis Services-instansen som visas på följande skärm bild:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png" alt-text="Azure Cosmos DB Power BI-anslutningsprogram":::

1. **Läs in data och generera rapporter** – genom att använda de data som du har läst in tidigare skapar du diagram för att rapportera om temperatur-och Rainfall. Eftersom du skapar en Live-anslutning ska frågorna köras på data i den Azure Analysis Services modell som du har distribuerat i föregående steg. Temperatur diagram kommer att uppdateras inom fem minuter efter att de nya data har lästs in i Azure Cosmos DB.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Azure Cosmos DB Power BI-anslutningsprogram":::

## <a name="next-steps"></a>Nästa steg

* Mer information om Power BI finns i [Kom igång med Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Anslut Qlik Sense till Azure Cosmos DB och visualisera dina data](visualize-qlik-sense.md)
