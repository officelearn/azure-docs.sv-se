---
title: Azure Data Factory - vanliga frågor och svar
description: Vanliga frågor om Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 387b5e944b0fd198e37a35d7bf1c54f7795c9f21
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory - vanliga frågor och svar
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [vanliga fråga - Data Factory version 2](../frequently-asked-questions.md).

## <a name="general-questions"></a>Allmänna frågor
### <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory?
Data Factory är en molnbaserad dataintegrering tjänsten som **automatiserar flytt och transformering av data**. Precis som en fabrik som kör utrustning för att ta raw material och omvandla dem till färdiga varor samordnar Data Factory befintliga tjänster som samlar in rådata och omvandla det till färdiga att använda information.

Data Factory kan du skapa datadrivna arbetsflöden för att flytta data mellan både lokalt och molnet datalager samt processen/Transformera data med hjälp av beräknings-tjänster som Azure HDInsight och Azure Data Lake Analytics. När du har skapat en pipeline som utför den åtgärd som du behöver schemalägga du den körs med jämna mellanrum (varje timme, varje dag, varje vecka osv.).   

Mer information finns i [översikt & nyckelkoncept](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Var hittar jag prisinformation för Azure Data Factory?
Se [Data Factory-prisinformation sidan] [ adf-pricing-details] för prisinformation för Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Hur kommer jag igång med Azure Data Factory?
* En översikt över Azure Data Factory finns [introduktion till Azure Data Factory](data-factory-introduction.md).
* En självstudiekurs om hur du **kopiera/flytta data** med Kopieringsaktivitet kan visa [kopiera data från Azure Blobblagring till Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* En självstudiekurs om hur du **Transformera data** använder HDInsight Hive-aktivitet. Se [bearbeta data genom att köra Hive-skript på Hadoop-kluster](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Vad är den Data Factory regional tillgänglighet?
Data Factory finns i **oss West** och **Nordeuropa**. Beräknings- och tjänsterna som används av datafabriker kan vara i andra regioner. Se [regioner som stöds](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Vilka är gränserna för antalet data fabriker/pipelines/aktiviteter/datauppsättningar?
Se **Azure Data Factory gränser** avsnitt i den [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../../azure-subscription-service-limits.md#data-factory-limits) artikel.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Vad är redigering/utvecklare erfarenhet av Azure Data Factory-tjänsten?
Du kan redigera/skapa datafabriker med någon av följande verktyg/SDK:

* **Azure-portalen** The Data Factory-blad i Azure-portalen ger omfattande användargränssnitt att skapa data fabriker ad länkade tjänster. Den **Data Factory-redigeraren**, som också är en del av portalen, kan du enkelt skapa länkade tjänster, tabeller, datauppsättningar och pipelines genom att ange JSON definitioner för dessa artefakter. Se [skapa din första pipeline för data med hjälp av Azure portal](data-factory-build-your-first-pipeline-using-editor.md) ett exempel på hur du använder portalen och redigerare att skapa och distribuera en datafabrik.
* **Visual Studio** du kan använda Visual Studio för att skapa ett Azure data factory. Se [skapa din första pipeline för data med hjälp av Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) mer information.
* **Azure PowerShell** finns [skapa och övervaka Azure Data Factory med Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) stegvisa självstudier/för att skapa en datafabrik med hjälp av PowerShell. Se [Data Factory-Cmdlet-referens] [ adf-powershell-reference] innehållet i MSDN Library för en omfattande dokumentation av Data Factory-cmdlets.
* **.NET-klassbibliotek** programmässigt kan du skapa datafabriker med Data Factory .NET SDK. Se [skapa, övervaka och hantera datafabriker med .NET SDK](data-factory-create-data-factories-programmatically.md) för en genomgång av hur du skapar en datafabrik med .NET SDK. Se [Data Factory klassen biblioteksreferens] [ msdn-class-library-reference] för en omfattande Data Factory .NET SDK-dokumentationen.
* **REST API** du kan också använda REST-API som exponeras av Azure Data Factory-tjänsten för att skapa och distribuera datafabriker. Se [Data Factory REST API-referens] [ msdn-rest-api-reference] för en omfattande Data Factory REST API-dokumentationen.
* **Azure Resource Manager-mall** finns [Självstudier: skapa din första Azure data factory med Azure Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md) för information.

### <a name="can-i-rename-a-data-factory"></a>Kan jag byta namn på en datafabrik?
Nej. Precis som andra Azure-resurser kan inte ändra namnet på ett Azure data factory.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Kan jag flytta en datafabrik från en Azure-prenumeration till en annan?
Ja. Använd den **flytta** knappen på din data factory-bladet som visas i följande diagram:

![Flytta data factory](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Vad är beräknings-miljöer som stöds av Data Factory?
Följande tabell innehåller en lista över compute-miljöer som stöds av Data Factory och de aktiviteter som kan köras på dem.

| Compute-miljö | activities |
| --- | --- |
| [HDInsight-kluster på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) eller [egna HDInsight-kluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop-strömning](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Machine Learning-aktiviteter: batchkörning och resursuppdatering](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Lagrad procedur](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Hur jämfört med SQL Server Integration Services (SSIS) i Azure Data Factory? 
Finns det [jämfört med Azure Data Factory. SSIS](http://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) presentation från en av våra MVP (mest värdefulla personal): Reza Rad. Några av de senaste ändringarna i Data Factory kanske inte visas i presentationen. Vi kontinuerligt lägger till flera funktioner i Azure Data Factory. Vi kontinuerligt lägger till flera funktioner i Azure Data Factory. Vi kommer införliva de här uppdateringarna i jämförelse av data integration teknik från Microsoft stund senare i år.   

## <a name="activities---faq"></a>Aktiviteter - vanliga frågor och svar
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Vilka är de olika typerna av aktiviteter som du kan använda i en Data Factory-pipelinen?
* [Data Movement aktiviteter](data-factory-data-movement-activities.md) att flytta data.
* [Data Transformation aktiviteter](data-factory-data-transformation-activities.md) process/Omforma data.

### <a name="when-does-an-activity-run"></a>När körs en aktivitet
Den **tillgänglighet** Konfigurationsinställningen i utdatatabellen data anger när aktiviteten körs. Om indatauppsättningar anges aktiviteten kontrollerar om alla indata-beroenden är uppfyllda (det vill säga **klar** tillstånd) innan den börjar köras.

## <a name="copy-activity---faq"></a>Kopieringsaktiviteten – vanliga frågor och svar
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Är det bättre att ha en pipeline med flera aktiviteter eller en separat pipeline för varje aktivitet?
Pipelines som är avsedda för att paketera relaterade aktiviteter. Om de datamängder som ansluter dem inte används av andra aktiviteter utanför pipeline, kan du behålla aktiviteter i en pipeline. På så sätt kan du inte behöver kedjan pipeline aktiva perioder så att de överensstämmer med varandra. Dessutom bevaras bättre dataintegriteten i tabeller som är interna för pipeline vid uppdatering av pipelinen. Pipeline-uppdatering i stort sett stoppar alla aktiviteter inom pipeline, tas de bort och skapar dem igen. Från redigering perspektiv, kan det också vara lättare att se flödet av data i de relaterade aktiviteterna i en JSON-fil för pipeline.

### <a name="what-are-the-supported-data-stores"></a>Vad är datalager stöds?
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory har stöd för följande datalager. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datalager med * kan finnas lokalt eller på Azure IaaS, och kräver att du installerar [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal/Azure IaaS-dator.

### <a name="what-are-the-supported-file-formats"></a>Vilka är filformat som stöds?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Där utförs kopieringen?
Se [globalt tillgänglig dataflyttning](data-factory-data-movement-activities.md#global) information. När ett lokalt datalager ingår, utförs i korthet kopieringen av Data Management Gateway i din lokala miljö. Och när data flyttas mellan två molnet butiker kopieringen utförs i närmast sink-plats i samma geografiska region.

## <a name="hdinsight-activity---faq"></a>HDInsight-aktivitet – vanliga frågor och svar
### <a name="what-regions-are-supported-by-hdinsight"></a>Vilka regioner som stöds av HDInsight?
Finns under geografisk tillgänglighet i följande artikel: eller [HDInsight prisinformation][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Vilken region som används av ett HDInsight-kluster på begäran?
HDInsight-kluster på begäran skapas i samma region där lagringsenhet för att användas med klustret finns.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Så här associerar du ytterligare lagringskonton till ditt HDInsight-kluster?
Om du använder egna HDInsight-kluster (BYOC - sätta ditt eget kluster) finns i följande avsnitt:

* [Med hjälp av ett HDInsight-kluster med alternativa Lagringskonton och Metastores][hdinsight-alternate-storage]
* [Använda ytterligare Lagringskonton med HDInsight Hive][hdinsight-alternate-storage-2]

Om du använder ett på begäran-kluster som skapas av Data Factory-tjänsten kan du ange ytterligare lagringskonton för till HDInsight länkade tjänsten så att Data Factory-tjänsten kan registrera dem å dina vägnar. I JSON-definitionen för den länkade tjänsten på begäran använder **additionalLinkedServiceNames** att ange alternativa lagringskonton som visas i följande fragment JSON:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
I exemplet ovan representerar otherLinkedServiceName1 och otherLinkedServiceName2 länkade tjänster vars definitioner innehåller autentiseringsuppgifter som HDInsight-kluster behöver åtkomst till alternativa lagringskonton.

## <a name="slices---faq"></a>Segment - vanliga frågor och svar
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Varför är min inkommande segment inte i tillståndet Ready?
Vanliga fel inte inställningen **externa** egenskapen **SANT** på inkommande dataset när indata är externa för datafabriken (som inte tillverkas av datafabriken).

I följande exempel behöver du bara ange **externa** till true i **dataset1**.  

**DataFactory1** Pipeline 1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 Pipeline 2: dataset3 -> activity3 -> dataset4

Om du har en annan data factory med en rörledning som tar dataset4 (tillverkas av pipeline 2 i data factory 1), måste du markera dataset4 som en extern datauppsättning eftersom datamängden produceras av en annan datafabrik (DataFactory1, inte DataFactory2).  

**DataFactory2**    
Pipeline 1: dataset4 -> activity4 -> dataset5

Om egenskapen externa är korrekt inställt, kan du kontrollera om indata finns på platsen som angavs i definitionen för inkommande datamängden.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Hur du kör ett segment vid ett senare tillfälle än midnatt när sektorn tillverkas dagligen?
Använd den **offset** egenskapen för att ange den tid som du vill ska produceras sektorn. Se [Dataset tillgänglighet](data-factory-create-datasets.md#dataset-availability) finns mer information om den här egenskapen. Här är ett enkelt exempel:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Dagliga segment som börjar vid **06: 00** i stället för standard midnatt.     

### <a name="how-can-i-rerun-a-slice"></a>Hur kan jag köra ett segment?
Du kan köra en sektor i ett av följande sätt:

* Använda övervaka och hantera appen för att köra en aktivitet fönster eller segment. Se [kör den markerade aktiviteten windows](data-factory-monitor-manage-app.md#perform-batch-actions) anvisningar.   
* Klicka på **kör** i kommandofältet på den **DATASEKTORN** bladet för sektorn i Azure-portalen.
* Kör **Set AzureRmDataFactorySliceStatus** med Status är inställd på **väntar på** för sektorn.   

    ```PowerShell
    Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
Se [Set AzureRmDataFactorySliceStatus] [ set-azure-datafactory-slice-status] mer information om cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Hur lång tid tog den för att bearbeta en sektor?
Använd aktiviteten fönstret Explorer i övervaka och hantera App veta hur lång tid det tog för att bearbeta en datasektorn. Se [aktivitet fönstret Explorer](data-factory-monitor-manage-app.md#activity-window-explorer) mer information.

Du kan också göra följande i Azure-portalen:  

1. Klicka på **datauppsättningar** panelen på den **DATA FACTORY** bladet för din data factory.
2. Klicka på den specifika datamängden på den **datauppsättningar** bladet.
3. Markera det segment som du är intresserad från den **senaste segment** listan den **tabell** bladet.
4. Klicka på den aktivitet som körs från den **aktiviteten körs** listan den **DATASEKTORN** bladet.
5. Klicka på **egenskaper** panelen på den **kör AKTIVITETSINFORMATION** bladet.
6. Du bör se den **varaktighet** fält med ett värde. Det här värdet är den tid det tar att bearbeta sektorn.   

### <a name="how-to-stop-a-running-slice"></a>Hur du stoppar en körs sektor?
Om du behöver stoppa pipelinen körs kan du använda [pausa AzureRmDataFactoryPipeline](/powershell/module/azurerm.datafactories/suspend-azurermdatafactorypipeline) cmdlet. För närvarande slutar pausa pipeline inte sektorn körningar som pågår. När de pågående körningar har hämtas inga extra sektorn.

Om du vill stoppa alla körningar omedelbart, är det enda sättet att ta bort pipeline och skapa den igen. Om du väljer att ta bort pipeline, behöver du inte ta bort tabeller och länkade tjänster som används av pipeline.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/azurerm.datafactories
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/set-azurermdatafactoryslicestatus

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
