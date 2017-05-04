---
title: "Introduktion till Data Factory, en dataintegreringstjänst | Microsoft Docs"
description: "Detta är vad Azure Data Factory är: en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data."
keywords: dataintegrering, molnbaserad dataintegrering, azure data factory
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/21/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 260208e7c7a08110eb3c885ef86ec4c18ff42fc9
ms.openlocfilehash: 40552b5d3cea5b04826c08e7b4b1d046a9fcefba
ms.lasthandoff: 04/23/2017


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Introduktion till Azure Data Factory – en dataintegreringstjänst i molnet
## <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory?
Hur utnyttjas befintliga data i företag? Är det möjligt att utöka information som genereras i molnet med referensdata från lokala datakällor eller andra olika datakällor? Vad som behövs är en plattform för insamling och bearbetning av data från många olika källor. Azure Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar **förflyttning** och **omvandling** av data. Du kan skapa dataintegrationslösningar som kan mata in indata från olika datakällor, transformera/bearbeta data och publicera utdata till datalager. 

![Diagram: Data Factory-översikt, en dataintegreringstjänst](./media/data-factory-introduction/what-is-azure-data-factory.png)

**Figur 1.** För in data från olika datakällor, förbered, transformera och analysera data, och publicera sedan data som färdiga att använda.


## <a name="what-does-it-offer"></a>Vad erbjuder den? 
Traditionellt har dataintegreringsprojekt handlat om att skapa ETL-processer (extrahering, transformering och inläsning) som hämtar data från olika datakällor i en organisation, transformerar dessa data så att de överensstämmer med målschemat på ett företagsinformationslager och läser in dem i företagsinformationslagret (se bilden nedan). Företagsinformationslagret blir sedan den enda sanningskällan för BI-analyslösningar.

![Traditionell ETL](media/data-factory-introduction/traditional-etl.png)
**Traditionell ETL**

Informationens volym, mångfald och komplexitet ökar exponentiellt för företag (se bilden nedan). Informationen är mer diversifierad än någonsin med lokala data och molndata i olika former och hastigheter. Databearbetning måste kunna ske över geografiska platser. Och för att bearbeta data behövs en kombination av programvara med öppen källkod, kommersiella lösningar och tjänster för anpassad bearbetning som är dyra och svåra att integrera och underhålla. Idag krävs flexibilitet för att klara dagens utmaningar med ett föränderligt stordatalandskap. Det innebär en möjlighet att slå samman traditionella företagsinformationslager med nya funktioner som behövs för ett moderna informationsproduktionssystem. Azure Data Factory är en plattform för arbete över traditionella företagsinformationslager och föränderliga datalandskap som ger företag möjlighet att utnyttja alla tillgängliga data och fatta välgrundade beslut baserat på data.

![Nytt stordatalandskap](media/data-factory-introduction/new-big-data-landscape.png)
**Nytt stordatalandskap**

Med tjänsten Azure Data Factory kan företag utnyttja denna mångfald. Med tjänsten kan du tillhandahålla en plattform för att **slå samman tjänster för databearbetning, lagring och förflyttning till informationsproduktionspipelines** och hantera betrodda datatillgångar.

Med tjänsten Azure Data Factory kan du:
- **Enkelt arbeta med olika datalagrings- och bearbetningssystem**. 

    Företag har olika typer av data lagrade i olika källor. Det första steget när det gäller att skapa ett informationsproduktionssystem är att skapa en anslutning till alla nödvändiga data- och bearbetningskällor, exempelvis SaaS-tjänster, filresurser, FTP och webbtjänster och att kunna flytta data till en central plats för bearbetning när det behövs.

    Utan Data Factory måste företag skapa egna dataöverföringskomponenter eller skriva anpassade tjänster för att integrera dessa data- och bearbetningskällor. Det är dyrt och svårt att integrera och underhålla sådana system, och ofta saknas de funktioner för övervakning, avisering och kontroll som en fullt hanterad tjänst kan erbjuda.

    Med Data Factory kan du använda kopieringsaktiviteten i en datapipeline för att flytta data från datalager lokalt och molnet till ett centralt datalager i molnet där du kan analysera dem. Du kan till exempel samla in data i en Azure Data Lake Store och sedan omvandla dessa data med en Azure Data Lake Analytics-databearbetningstjänst. Eller så kan du samla data i en Azure Blob Storage och sedan omvandla data med ett Azure HDInsight Hadoop-kluster.
- **Omvandla data till betrodd information**. 

    När data finns på ett centralt datalager i molnet kan du skapa och distribuera datapipelines för att skapa omvandlade data på ett tillförlitligt sätt enligt ett anpassningsbart schema och fylla på med betrodda data i produktionsmiljöer. Dataomvandlingen i Azure Data Factory utförs med transformeringsaktiviteter som Hive, Pig, MapReduce, Azure Machine Learning Batch Execution och anpassade C#-aktiviteter som körs i ett Azure HDInsight Hadoop-kluster, virtuella Azure Machine Learning-datorer eller en Azure Batch-pool med virtuella datorer.
- **Övervaka datapipelines på en plats**.

    Med en varierad dataportfölj är det viktigt att ha en pålitlig och komplett vy över lagrings-, bearbetnings- och datarörelsetjänster. Data Factory hjälper dig att snabbt få tillgång till end-to-end-datapipelinehälsa, identifiera problem och vidta rätt åtgärder vid behov. Spåra datahärkomst och relationerna mellan dina data mellan dina källor visuellt. Visa fullständig historik över jobbkörning, systemhälsa och beroenden från en och samma instrumentpanel för övervakning.

## <a name="how-does-it-work"></a>Hur fungerar det? 
Det finns tre informationsproduktionsfaser i en Azure Data Factory:

![Tre informationsproduktionsfaser](media/data-factory-introduction/three-information-production-stages.png)

- **Anslut och samla in**: I den här fasen samlas data från olika datakällor på en plats.
- **Transformera och utöka**: I den här fasen bearbetas eller omvandlas insamlade data.
- **Publicera**. I den här fasen publiceras data så att de kan användas av BI-verktyg, analysverktyg och andra program.

## <a name="key-components"></a>Nyckelkomponenter
En Azure-prenumeration kan ha en eller flera Azure Data Factory-instanser (eller datafabriker). Azure Data Factory består av fyra nyckelkomponenter som samverkar för att tillhandahålla en plattform för att skapa enkla till komplexa dataflyttningar och omvandlingsorkestreringar för ditt dataflöde.

### <a name="activity"></a>Aktivitet
Aktiviteter definierar de åtgärder som ska utföras på dina data. Du kan till exempel använda en kopieringsaktivitet för att kopiera data från ett datalager till ett annat. På samma sätt kan du använda en Hive-aktivitet som kör en Hive-fråga på ett Azure HDInsight-kluster för att transformera eller analysera dina data. Data Factory stöder två typer av aktiviteter: aktiviteter för dataförflyttning och datatransformering.

Varje aktivitet kan ha noll eller flera indatauppsättningar och kan producera en eller flera utdatauppsättningar. 


### <a name="data-movement-activities"></a>Dataförflyttningsaktiviteter
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory har stöd för följande datalager. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Mer information finns i artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Datatransformeringsaktiviteter
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Mer information finns i artikeln [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md).

Om du behöver flytta data till/från ett datalager som Kopiera aktivitet inte stöder, eller transformera data med egen logik, kan du skapa en **anpassad .NET-aktivitet**. Mer information om att skapa och använda en anpassad aktivitet finns i [Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)](data-factory-use-custom-activities.md).

### <a name="pipeline"></a>Pipeline
En pipeline är en grupp av aktiviteter. Aktiviteterna i en pipeline utför en uppgift tillsammans. En pipeline kan till exempel innehålla en grupp med aktiviteter som matar in data från en Azure-blob och som sedan kör en Hive-fråga på ett HDInsight-kluster för att partitionera loggdata. Fördelen med detta är att pipelinen låter dig hantera aktiviteterna som en grupp i stället för enskilt. Du kan till exempel distribuera och schemalägga en hel pipeline i stället för att göra detta separat för varje aktivitet.

### <a name="datasets"></a>Datauppsättningar
Datauppsättningar representerar datastrukturer i datalager som pekar på eller refererar till de data som du vill använda i dina aktiviteter som indata eller utdata. En Azure Blob-datauppsättning anger till exempel blobbehållaren och -mappen i Azure Blob Storage som pipelinen ska läsa data från. 

### <a name="linked-services"></a>Länkade tjänster
Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Man kan se det som att datamängden representerar strukturen för data och den länkade tjänsten definierar anslutningen till datakällan.  Länkade tjänster används för två syften i Data Factory:

* Att representera ett **datalager** inklusive, men inte begränsat till, en lokal SQL Server, Oracle-databas, filresurs eller ett Azure Blob Storage-konto. I avsnittet [Dataförflyttningsaktiviteter](#data-movement-activities) finns en lista över datalager som stöds.
* Så här visar du en **beräkningsresurs** som kan vara värd för körningen av en aktivitet. HDInsightHive-aktiviteten körs till exempel på ett HDInsight Hadoop-kluster. Se avsnittet [Datatransformeringsaktiviteter](#data-transformation-activities), för en lista över compute-miljöer som stöds.

### <a name="relationship-between-data-factory-entities"></a>Förhållande mellan Data Factory-enheter
![Diagram: Data Factory, en molnbaserad dataintegreringstjänst – nyckelbegrepp](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figur 2.** Relationer mellan datauppsättning, aktivitet, pipeline och länkad tjänst

## <a name="supported-regions"></a>Regioner som stöds
För närvarande kan du skapa datafabriker i regionerna **västra USA**, **östra USA** och **Norra Europa**. Dock kan en datafabrik ha åtkomst till datalager och beräkna tjänster i andra Azure-regioner för att flytta data mellan datalager eller bearbeta data med hjälp av beräkningstjänster.

Azure Data Factory lagrar inte själv några data. Du kan använda den för att skapa datadrivna flöden som samordnar flödet av data mellan [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) och bearbetning av data med hjälp av [beräkningstjänster](data-factory-compute-linked-services.md) i andra regioner eller i en lokal miljö. Du kan också [övervaka och hantera arbetsflöden](data-factory-monitor-manage-pipelines.md) med både program- och användargränssnittsmetoder.

Även om Data Factory bara finns i regionerna **USA, västra**, **USA, östra** och **Europa, norra**, är dataförflyttning i Data Factory tillgängligt [globalt](data-factory-data-movement-activities.md#global) i flera regioner. Om ett datalager finns bakom en brandvägg kan en [gateway för datahantering](data-factory-move-data-between-onprem-and-cloud.md) som installerats i din lokala miljö flytta datan i stället.

Exempelvis kan vi anta att dina beräkningsmiljöer, som t.ex. Azure HDInsight-kluster och Azure Machine Learning, körs utanför Västeuropas region. Du kan skapa och använda en Azure Data Factory-instans i norra Europa och använda den för att schemalägga jobb i dina beräkningsmiljöer i Västeuropa. Det tar några millisekunder för Data Factory att utlösa jobbet i din beräkningsmiljö, men den tid det tar för att köra jobbet ändras inte.

Vi planerar att ha Azure Data Factory i varje geografisk region som stöds av Azure i framtiden.

## <a name="next-steps"></a>Nästa steg
Följ de stegvisa anvisningarna i följande självstudier om du vill lära dig hur du skapar datafabriker med datapipeliner:

| Självstudier | Beskrivning |
| --- | --- |
| [Flytta data mellan två molndatalager](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |I de här självstudierna skapar du en datafabrik med en pipeline som **flyttar data** från Blob Storage till SQL Database. |
| [Omvandla data med Hadoop-kluster](data-factory-build-your-first-pipeline.md) |I de här självstudierna skapar du din första Azure Data Factory med en datapipeline som **bearbetar data** genom att köra Hive-skriptet på ett Azure HDInsight-kluster (Hadoop). |
| [Flytta data mellan ett lokalt datalager och ett molndatalager med hjälp av Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) |I de här självstudierna skapar du en datafabrik med en pipeline som **flyttar data** från en **lokal** SQL Server-databas till en Azure-blob. Som en del av den här genomgången kommer du att installera och konfigurera Data Management Gateway på din dator. |

