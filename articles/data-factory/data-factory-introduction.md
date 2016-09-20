<properties 
    pageTitle="Vad är Data Factory? Dataintegreringstjänst | Microsoft Azure" 
    description="Detta är vad Azure Data Factory är: en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data." 
    keywords="dataintegrering, molnbaserad dataintegrering, azure data factory"
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="07/12/2016" 
    ms.author="spelluru"/>

# Introduktion till Azure Data Factory – en dataintegreringstjänst i molnet

## Vad är Azure Data Factory? 
Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data. Precis som en fabrik som använder maskiner till att omvandla råmaterial till färdiga produkter, samordnar Data Factory befintliga tjänster som samlar in rådata och transformerar dem till information som är klar att använda. 

Data Factory fungerar både lokalt samt med molndatakällor och SaaS för att mata in, förbereda, transformera, analysera och publicera dina data.  Använd Data Factory till att skapa tjänster till pipelines med hanterade dataflöden och omvandla data med hjälp av tjänster som [Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) och [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) för stora databeräkningsbehov och med [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) för att använda analyslösningar.  Nöj dig inte med en övervakningsvy i tabellform, utan använd detaljerade visualiseringar av Data Factory till att snabbt visa härkomst och beroenden mellan dina datapipelines. Övervaka alla dina dataflödespipelines från en enda enhetlig vy för att enkelt hitta problem och konfigurera övervakningsaviseringar.

![Diagram: Data Factory-översikt, en dataintegreringstjänst](./media/data-factory-introduction/what-is-azure-data-factory.png)

**Figur 1.** Samla in data från flera olika lokala datakällor, mata in och förbered den, sortera och analysera den med olika transformeringar och publicera sedan data som är klar att använda.

Du kan använda Data Factory varje gång du behöver samla in data i olika former och storlekar, transformera den och publicera den för att få djupgående information – allt med ett tillförlitligt schema. Data Factory används för att skapa dataflödespipelines med hög tillgänglighet för många scenarier i olika branscher som behöver analyspipelines.  Onlineåterförsäljare använder det för att skapa anpassade [produktrekommendationer](data-factory-product-reco-usecase.md) baserat på kundens webbeteende. Speltillverkare använder det för att se hur [effektiva deras marknadsförings](data-factory-customer-profiling-usecase.md)kampanjer är. Lär dig direkt från våra kunder hur och varför de använder Data Factory genom att läsa våra [fallstudier](data-factory-customer-case-studies.md). 

> [AZURE.VIDEO azure-data-factory-overview]

## Viktiga begrepp

Azure Data Factory har några viktiga entiteter som samverkar för att definiera indata och utdata, bearbeta händelser, samt de scheman och resurser som krävs för att köra det önskade dataflödet.

![Diagram: Data Factory, en molnbaserad dataintegreringstjänst – nyckelbegrepp](./media/data-factory-introduction/data-integration-service-key-concepts.png)

**Figur 2.** Relationer mellan datauppsättning, aktivitet, pipeline och länkad tjänst


### Aktiviteter
Aktiviteter definierar de åtgärder som ska utföras på dina data. Varje aktivitet har noll eller flera [datauppsättningar](data-factory-create-datasets.md) som indata och skapar en eller flera datauppsättningar som utdata. En aktivitet är en samordningsenhet i Azure Data Factory. Du kan till exempel använda en [kopieringsaktivitet](data-factory-data-movement-activities.md) till att samordna kopiering av data från en datauppsättning till en annan. På samma sätt kan du använda en [Hive-aktivitet](data-factory-data-transformation-activities.md) som kör en Hive-fråga på ett Azure HDInsight-kluster för att transformera eller analysera dina data. Azure Data Factory innehåller en mängd olika datatransformerings-, analys- och dataförflyttningsaktiviteter. 

### Pipelines
[Pipelines](data-factory-create-pipelines.md) är en logisk gruppering av aktiviteter. De används för att gruppera aktiviteter till en enhet som tillsammans utför en aktivitet. Till exempel kan en sekvens med flera transformeringsaktiviteter behövas när man ska rensa loggfildata.  Den här sekvensen kan ha ett komplext schema och beroenden som måste samordnas och automatiseras. Alla dessa aktiviteter kan grupperas i en enda pipeline med namnet ”CleanLogFiles”.  ”CleanLogFiles” kan sedan distribueras, schemaläggas eller tas bort som en enskild enhet i stället för att hantera varje enskild aktivitet separat.

### Datauppsättningar
[Datauppsättningar](data-factory-create-datasets.md) är namngivna referenser/pekare för de data som du vill använda som in- eller utdata för en aktivitet. Datauppsättningar identifierar datastrukturer inom olika datalager, inklusive tabeller, filer, mappar och dokument.

### Länkad tjänst
Länkade tjänster definierar den information som behövs för att Data Factory ska kunna ansluta till externa resurser.  Länkade tjänster används för två syften i Data Factory:

- Att representera ett datalager inklusive, men inte begränsat till, en lokal SQL Server, Oracle DB, filresurs eller ett Azure-blobblagringskonto.   Som beskrivs ovan visar datauppsättningar strukturer i de datalager som är anslutna till Data Factory via en länkad tjänst.
- Så här visar du en beräkningsresurs som kan vara värd för körningen av en aktivitet.  ”HDInsightHive-aktiviteten” körs till exempel på ett HDInsight Hadoop-kluster.

Med de fyra enkla datauppsättningarna, aktiviteterna, pipelines och länkade tjänster är du redo att sätta igång!  Du kan [skapa din första pipeline](data-factory-build-your-first-pipeline.md) från grunden, eller distribuera ett färdigt exempel genom att följa instruktionerna i artikeln [Data Factory-exempel](data-factory-samples.md). 

## Regioner som stöds
Du kan skapa datafabriker i regionerna **västra USA**, **östra USA** och **Norra Europa** för närvarande. Dock kan en datafabrik ha åtkomst till datalager och beräkna tjänster i andra Azure-regioner för att flytta data mellan datalager eller bearbeta data med hjälp av beräkningstjänster. 

Azure Data Factory lagrar inte själv några data. Du kan använda den för att skapa datadrivna flöden som samordnar flödet av data mellan [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores) och bearbetning av data med hjälp av [beräkningstjänster](data-factory-compute-linked-services.md) i andra regioner eller i en lokal miljö. Du kan också [övervaka och hantera arbetsflöden](data-factory-monitor-manage-pipelines.md) med både program- och användargränssnittsmetoder. 

Observera att även om Azure Data Factory bara finns i regionerna **västra USA**, **östra USA** och **norra Europa**, är dataförflyttning i Data Factory tillgängligt [globalt](data-factory-data-movement-activities.md#global) i flera regioner. Om ett datalager finns bakom en brandvägg kan en [gateway för datahantering](data-factory-move-data-between-onprem-and-cloud.md) som installerats i din lokala miljö flytta datan i stället. 

Exempelvis kan vi anta att dina beräkningsmiljöer, som t.ex. Azure HDInsight-kluster och Azure Machine Learning, körs utanför Västeuropas region. Du kan skapa och använda en Azure Data Factory-instans i norra Europa och använda den för att schemalägga jobb i dina beräkningsmiljöer i Västeuropa. Det tar några millisekunder för Data Factory att utlösa jobbet i din beräkningsmiljö, men den tid det tar för att köra jobbet ändras inte.

Vi planerar att ha Azure Data Factory i varje geografisk region som stöds av Azure i framtiden.
  
## Nästa steg
Följ de stegvisa anvisningarna i följande självstudier om du vill lära dig hur man skapar datafabriker med datapipeliner. 

Självstudier | Beskrivning
-------- | -----------
[Skapa en datapipeline som bearbetar data via ett Hadoop-kluster](data-factory-build-your-first-pipeline.md) | I den här självstudien får du skapa din första Azure Data Factory med en datapipeline som **bearbetar data** genom att köra Hive-skriptet på ett Azure HDInsight-kluster (Hadoop). |
[Skapa en datapipeline som flyttar data mellan två molndatalager](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | I den här självstudien skapar du en datafabrik med en pipeline som **flyttar data** från Blob Storage till SQL Database.
[Skapa en datapipeline som flyttar data mellan ett lokalt datalager och ett molndatalager med hjälp av Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) |  I den här självstudien skapar du en datafabrik med en pipeline som **flyttar data** från en **lokal** SQL Server-databas till en Azure-blob. Som en del av den här genomgången kommer du att installera och konfigurera Data Management Gateway på din dator. 


<!--HONumber=sep16_HO1-->


