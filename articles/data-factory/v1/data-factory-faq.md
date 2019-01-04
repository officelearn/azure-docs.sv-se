---
title: Azure Data Factory – vanliga frågor och svar
description: Vanliga frågor om Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 81c7c98f29c2e507e165a3943395e36a453cbf06
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024050"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory – vanliga frågor och svar
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [vanliga frågor och svar – Data Factory](../frequently-asked-questions.md).

## <a name="general-questions"></a>Allmänna frågor
### <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory?
Data Factory är en molnbaserad dataintegrering tjänst som **automatiserar flytt och omvandling av data**. Precis som en fabrik som använder maskiner till att omvandla råmaterial och omvandla dem till färdiga produkter, samordnar Data Factory befintliga tjänster som samlar in rådata och omvandla det till färdiga att använda information.

Data Factory kan du skapa datadrivna arbetsflöden som flyttar data mellan både lokala och molndatalager samt bearbeta/omvandla data med hjälp av Beräkningstjänster, till exempel Azure HDInsight och Azure Data Lake Analytics. När du har skapat en pipeline som utför den åtgärd som du behöver kan du schemalägga den så att den körs regelbundet (varje timme, varje dag, vecka osv.).   

Mer information finns i [översikt och nyckelkoncept](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Var hittar jag prisinformation för Azure Data Factory?
Se [Data Factory prisinformation om sidan] [ adf-pricing-details] för prisinformation för Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Hur kommer jag igång med Azure Data Factory?
* En översikt över Azure Data Factory finns i [introduktion till Azure Data Factory](data-factory-introduction.md).
* En självstudiekurs om hur du **kopiera/flytta data** med Kopieringsaktivitet finns i [kopiera data från Azure Blob Storage till Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* En självstudiekurs om hur du **Transformera data** med hjälp av HDInsight Hive-aktivitet. Se [bearbetar data genom att köra Hive-skript på Hadoop-kluster](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Vad är Data Factory regiontillgänglighet?
Data Factory finns i **västra USA** och **Nordeuropa**. Beräknings- och tjänsterna som används av datafabriker kan finnas i andra regioner. Se [regioner som stöds](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Vad är gränser för antal data fabriker/pipelines/aktiviteter/datauppsättningar?
Se **Azure Data Factory-gränser** delen av den [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../../azure-subscription-service-limits.md#data-factory-limits) artikeln.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Vad är redigering/utvecklarupplevelsen med Azure Data Factory-tjänsten?
Du kan redigera/skapa datafabriker med någon av följande verktyg/SDK: erna:

* **Azure-portalen** The Data Factory-blad i Azure-portalen tillhandahåller omfattande användargränssnitt att skapa datafabriker ad länkade tjänster. Den **Data Factory-redigeraren**, som ingår även i portalen, kan du enkelt skapa länkade tjänster, tabeller, datauppsättningar och pipeliner genom att ange JSON-definitioner för dessa artefakter. Se [skapa din första datapipeline med hjälp av Azure portal](data-factory-build-your-first-pipeline-using-editor.md) ett exempel på hur du använder portalen/Redigeraren för att skapa och distribuera en data factory.
* **Visual Studio** du kan använda Visual Studio för att skapa en Azure-datafabrik. Se [skapa din första datapipeline med hjälp av Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) mer information.
* **Azure PowerShell** Se [skapa och övervaka Azure-Datafabrik med hjälp av Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) en självstudie/genomgång för att skapa en datafabrik med hjälp av PowerShell. Se [Cmdlet-referens för Data Factory] [ adf-powershell-reference] innehållet i MSDN Library för en omfattande dokumentation om Data Factory-cmdletar.
* **.NET-klassbiblioteket** du kan skapa datafabriker programmässigt med hjälp av Data Factory .NET SDK. Se [skapa, övervaka och hantera datafabriker med hjälp av .NET SDK](data-factory-create-data-factories-programmatically.md) en genomgång för att skapa en datafabrik med hjälp av .NET SDK. Se [biblioteksreferens för Data Factory klass] [ msdn-class-library-reference] för en omfattande dokumentation för Data Factory .NET SDK.
* **REST API** du kan också använda REST-API som visas av Azure Data Factory-tjänsten för att skapa och distribuera datafabriker. Se [Data Factory REST API-referens] [ msdn-rest-api-reference] för en omfattande dokumentation för Data Factory REST API.
* **Azure Resource Manager-mall** Se [självstudien: Skapa din första Azure-datafabrik med hjälp av Azure Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md) fo information.

### <a name="can-i-rename-a-data-factory"></a>Kan jag byta namn på en data factory?
Nej. Namnet på en Azure-datafabrik som andra Azure-resurser kan inte ändras.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Kan jag flytta en data factory från en Azure-prenumeration till en annan?
Ja. Använd den **flytta** knapp på din data factory-blad som du ser i följande diagram:

![Flytta data factory](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Vilka är de beräkningsmiljöer som stöds av Data Factory?
Följande tabell innehåller en lista över de beräkningsmiljöer som stöds av Data Factory och de aktiviteter som kan köras på dem.

| Compute-miljö | activities |
| --- | --- |
| [HDInsight-kluster på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) eller [ett eget HDInsight-kluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Machine Learning-aktiviteter: Batchkörning och resursuppdatering](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [SQLServer](data-factory-compute-linked-services.md#sql-server-linked-service) |[Lagrad procedur](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Hur jämfört med SQL Server Integration Services (SSIS) i Azure Data Factory? 
Se den [jämfört med Azure Data Factory. SSIS](http://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) presentation från en av våra MVP (Most värdefull Professionals): Reza Rad. Vissa av de senaste ändringarna i Data Factory kan inte visas i presentationen. Vi lägger kontinuerligt till fler funktioner till Azure Data Factory. Vi lägger kontinuerligt till fler funktioner till Azure Data Factory. Vi kommer införliva de här uppdateringarna i jämförelse av data Integreringstekniker från Microsoft en stund senare i år.   

## <a name="activities---faq"></a>Aktiviteter – vanliga frågor och svar
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Vilka är de olika typerna av aktiviteter som du kan använda i Data Factory-pipeline?
* [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) att flytta data.
* [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) att bearbeta/Transformera data.

### <a name="when-does-an-activity-run"></a>När körs en aktivitet?
Den **tillgänglighet** konfigurationsinställning i utdatatabellen avgör när aktiviteten körs. Om indatauppsättningar anges, aktiviteten kontrollerar om alla indata-beroenden uppfylls (det vill säga **redo** tillstånd) innan den börjar köras.

## <a name="copy-activity---faq"></a>Kopiera aktivitet – vanliga frågor och svar
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Är det bättre att ha en pipeline med flera aktiviteter eller en separat pipeline för varje aktivitet?
Pipelines som är avsedda för att paketera relaterade aktiviteter. Om de datauppsättningar som kopplar ihop dem inte förbrukas av andra aktiviteter utanför pipelinen, behåller du aktiviteter i en pipeline. På så sätt kan du inte behöver kedja pipeline aktiva perioder så att de överensstämmer med varandra. Dessutom bevaras bättre dataintegriteten i tabellerna som är interna för pipelinen vid uppdatering av pipelinen. Pipeline-uppdatering i stort sett stoppar alla aktiviteter i pipelinen, tar bort dem och skapar dem igen. Från redigerar perspektiv, kan det också vara lättare att se flödet av data i de relaterade aktiviteterna i en JSON-fil för pipelinen.

### <a name="what-are-the-supported-data-stores"></a>Vilka är datalager som stöds?
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory har stöd för följande datalager. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datalager med * kan finnas lokalt eller på Azure IaaS, och kräver att du installerar [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal/Azure IaaS-dator.

### <a name="what-are-the-supported-file-formats"></a>Vilka är filformat som stöds?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Där utförs kopieringen?
Se [globalt tillgänglig dataförflyttning](data-factory-data-movement-activities.md#global) information. När ett lokalt datalager ingår, utförs i korthet kopieringen av Data Management Gateway i din lokala miljö. Och när dataförflyttning mellan två molnbaserade datalager kopieringen utförs i regionen som är närmast mottagaren plats i samma geografiska område.

## <a name="hdinsight-activity---faq"></a>HDInsight-aktivitet – vanliga frågor och svar
### <a name="what-regions-are-supported-by-hdinsight"></a>Vilka regioner stöds av HDInsight?
Se avsnittet geografisk tillgänglighet i följande artikel: eller [HDInsight – prisinformation][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Vilken region som ska användas av ett HDInsight-kluster på begäran?
HDInsight-kluster på begäran skapas i samma region där den lagring som du angav för att användas med klustret finns.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Så här associerar du ytterligare lagringskonton till HDInsight-kluster?
Om du använder ett eget HDInsight-kluster (ta med din egen kluster - BYOC), finns i följande avsnitt:

* [Använda ett HDInsight-kluster med alternativa Lagringskonton och Metastores][hdinsight-alternate-storage]
* [Använda ytterligare Lagringskonton med Hive i HDInsight][hdinsight-alternate-storage-2]

Om du använder ett kluster på begäran som skapas av Data Factory-tjänsten kan du ange ytterligare lagringskonton för HDInsight-länkad tjänst så att Data Factory-tjänsten kan registrera dem för din räkning. I JSON-definitionen för den länkade tjänsten på begäran använder **, additionallinkedservicenames,** egenskapen ange alternativa lagringskonton som visas i följande JSON-kodfragment:

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
I exemplet ovan motsvarar otherLinkedServiceName1 och otherLinkedServiceName2 du länkade tjänster vars definitioner innehåller autentiseringsuppgifter som HDInsight-klustret behöver åtkomst till alternativa lagringskonton.

## <a name="slices---faq"></a>Sektorer – vanliga frågor och svar
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Varför är min indatasektorerna inte i färdigt tillstånd?
Ett vanligt fel inte ange **externa** egenskap **SANT** för indatauppsättningen när indata är extern till data factory (inte tillverkas av data factory).

I följande exempel behöver du bara ange **externa** TRUE på **dataset1**.  

**DataFactory1** Pipeline-1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 Pipeline 2: dataset3 -> activity3 dataset4 ->

Om du har en annan data factory med en pipeline som tar dataset4 (produceras av pipelinen 2 i data factory 1) kan du markera dataset4 som en extern datauppsättning eftersom datauppsättningen produceras av en annan data factory (DataFactory1, inte DataFactory2).  

**DataFactory2**    
Pipeline 1: dataset4 -> activity4 dataset5 ->

Om den externa egenskapen har konfigurerats korrekt, kontrollerar du om det finns indata på den plats som anges i datauppsättningen för indata.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Hur du kör en sektor vid ett senare tillfälle än midnatt när sektorn produceras varje dag?
Använd den **offset** egenskap anger du den tid som du vill att sektorn som produceras. Se [tillgänglighet för datauppsättningar](data-factory-create-datasets.md#dataset-availability) finns mer information om den här egenskapen. Här är ett enkelt exempel:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Dagliga sektorer som börjar vid **06: 00** i stället för standard-midnatt.     

### <a name="how-can-i-rerun-a-slice"></a>Hur kan jag köra en sektor?
Du kan köra en sektor i ett av följande sätt:

* Använda appen övervaka och hantera för att köra ett aktivitetsfönster eller segment. Se [kör den markerade aktivitetsfönster](data-factory-monitor-manage-app.md#perform-batch-actions) anvisningar.   
* Klicka på **kör** i kommandofältet på den **DATASEKTOR** bladet för sektorn i Azure-portalen.
* Kör **Set-AzureRmDataFactorySliceStatus** cmdlet: en med statusen inställd **väntar på** för sektorn.   

    ```PowerShell
    Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
Se [Set-AzureRmDataFactorySliceStatus] [ set-azure-datafactory-slice-status] mer information om cmdlet: en.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Hur lång tid tog den för att bearbeta en sektor?
Använd aktiviteten fönstret Explorer i övervaknings- och Hanteringsappen veta hur lång tid det tog för att bearbeta en datasektor. Se [aktivitet fönstret Explorer](data-factory-monitor-manage-app.md#activity-window-explorer) mer information.

Du kan också göra följande i Azure portal:  

1. Klicka på **datauppsättningar** panel på den **DATA FACTORY** bladet för din datafabrik.
2. Klicka på den specifika datauppsättningen på den **datauppsättningar** bladet.
3. Välj den sektor som du är intresserad från den **nyliga sektorer** lista på den **tabell** bladet.
4. Klicka på den aktivitet som körs från den **Aktivitetskörningar** i listan den **DATASEKTOR** bladet.
5. Klicka på **egenskaper** panel på den **AKTIVITETSKÖRNINGSINFORMATION** bladet.
6. Du bör se den **varaktighet** med ett värde. Det här värdet är den tid det tar att bearbeta sektorn.   

### <a name="how-to-stop-a-running-slice"></a>Hur du stoppar ett pågående segment?
Om du vill stoppa pipelinen körs kan du använda [Suspend-AzureRmDataFactoryPipeline](/powershell/module/azurerm.datafactories/suspend-azurermdatafactorypipeline) cmdlet. För närvarande pausar pipelinen inte att stoppa den sektor körningar som pågår. När den pågående körningar är klar, hämtas inga extra sektorn.

Om du vill stoppa alla körningar direkt, är det enda sättet att ta bort pipelinen och skapa det igen. Om du väljer att ta bort pipelinen, behöver du inte ta bort tabeller och länkade tjänster som används av pipelinen.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/azurerm.datafactories/
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/azurerm.datafactories/set-azurermdatafactoryslicestatus

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
