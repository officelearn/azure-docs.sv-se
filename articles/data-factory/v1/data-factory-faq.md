---
title: Azure Data Factory – Vanliga frågor och svar
description: Vanliga frågor och svar om Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 1b042c0279e458cf83bd5c81147f4b1f25d7687d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645148"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory – Vanliga frågor och svar
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [vanliga frågor - Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Allmänna frågor
### <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory?
Data Factory är en molnbaserad **dataintegrationstjänst som automatiserar förflyttning och omvandling av data**. Precis som en fabrik som driver utrustning för att ta råvaror och omvandla dem till färdiga varor, orkestrerar Data Factory befintliga tjänster som samlar in rådata och omvandlar den till färdig användningsinformation.

Med Data Factory kan du skapa datadrivna arbetsflöden för att flytta data mellan både lokala och molnbaserade datalager samt bearbeta/omvandla data med hjälp av beräkningstjänster som Azure HDInsight och Azure Data Lake Analytics. När du har skapat en pipeline som utför den åtgärd som du behöver kan du schemalägga den så att den körs med jämna mellanrum (varje timme, varje dag, varje vecka osv.).   

Mer information finns i [Översikt & nyckelbegrepp](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Var hittar jag prisinformation för Azure Data Factory?
Se [sidan Information om information om datafabrik för][adf-pricing-details] prisinformation för Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Hur kommer jag igång med Azure Data Factory?
* En översikt över Azure Data Factory finns i [Introduktion till Azure Data Factory](data-factory-introduction.md).
* En självstudiekurs om hur du **kopierar/flyttar data** med kopiera aktivitet finns i [Kopiera data från Azure Blob Storage till Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* För en handledning om hur du **omvandlar data** med HDInsight Hive-aktivitet. Se [Bearbeta data genom att köra Hive-skript i Hadoop-klustret](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Vad är datafabrikens regiontillgänglighet?
Data Factory finns i **USA Väst** och **Norra Europa.** De beräknings- och lagringstjänster som används av datafabriker kan finnas i andra regioner. Se [Regioner som stöds](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Vilka är gränserna för antalet datafabriker/rörledningar/aktiviteter/datauppsättningar?
Se **avsnittet Azure Data Factory Limits** i artikeln Azure Subscription and Service [Limits, Kvoter och Begränsningar.](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Vad är för redigerings-/utvecklarupplevelse med Azure Data Factory-tjänsten?
Du kan skapa/skapa datafabriker med något av följande verktyg/SDK:er:

* **Visuell studio** Du kan använda Visual Studio för att skapa en Azure-datafabrik. Mer information finns [i Skapa din första datapipeline med Visual Studio.](data-factory-build-your-first-pipeline-using-vs.md)
* **Azure PowerShell** Se [Skapa och övervaka Azure Data Factory med Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) för en självstudiekurs/genomgång för att skapa en datafabrik med PowerShell. Se [Data Factory Cmdlet Reference innehåll][adf-powershell-reference] på MSDN-bibliotek för en omfattande dokumentation av Data Factory cmdlets.
* **.NET-klassbibliotek** Du kan programmässigt skapa datafabriker med hjälp av Data Factory .NET SDK. Se [Skapa, övervaka och hantera datafabriker med .NET SDK](data-factory-create-data-factories-programmatically.md) för en genomgång av att skapa en datafabrik med .NET SDK. Se [Referens för datafabriksklassbibliotek][msdn-class-library-reference] för en omfattande dokumentation av Data Factory .NET SDK.
* **REST API** Du kan också använda REST API som exponeras av Azure Data Factory-tjänsten för att skapa och distribuera datafabriker. Se [Data Factory REST API Reference][msdn-rest-api-reference] för en omfattande dokumentation av Data Factory REST API.
* **Mall för Azure Resource Manager** Se [självstudiekurs: Skapa din första Azure-datafabrik med Azure Resource Manager-mallen](data-factory-build-your-first-pipeline-using-arm.md) för information.

### <a name="can-i-rename-a-data-factory"></a>Kan jag byta namn på en datafabrik?
Nej. Precis som andra Azure-resurser kan namnet på en Azure-datafabrik inte ändras.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Kan jag flytta en datafabrik från en Azure-prenumeration till en annan?
Ja. Använd knappen **Flytta** på datafabriksbladet enligt följande diagram:

![Flytta datafabrik](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Vilka beräkningsmiljöer stöds av Data Factory?
Följande tabell innehåller en lista över beräkningsmiljöer som stöds av Data Factory och de aktiviteter som kan köras på dem.

| Compute-miljö | activities |
| --- | --- |
| [HDInsight-kluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) på begäran eller [ditt eget HDInsight-kluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Machine Learning-aktiviteter: batchkörning och resursuppdatering](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), SQL [Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Lagrad procedur](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Hur jämför Azure Data Factory med SQL Server Integration Services (SSIS)? 
Se [Azure Data Factory vs. SSIS-presentationen](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) från en av våra MVP:er (mest värdefulla proffs): Reza Rad. Vissa av de senaste ändringarna i Data Factory kanske inte visas i bildspelet. Vi lägger kontinuerligt till fler funktioner i Azure Data Factory. Vi lägger kontinuerligt till fler funktioner i Azure Data Factory. Vi kommer att införliva dessa uppdateringar i jämförelsen av dataintegrationsteknik från Microsoft någon gång senare i år.   

## <a name="activities---faq"></a>Aktiviteter - Vanliga frågor och svar
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Vilka olika typer av aktiviteter kan du använda i en Data Factory-pipeline?
* [Dataflyttningsaktiviteter](data-factory-data-movement-activities.md) för att flytta data.
* [Data transformationsaktiviteter](data-factory-data-transformation-activities.md) för att bearbeta/omvandla data.

### <a name="when-does-an-activity-run"></a>När körs en aktivitet?
Konfigurationsinställningen **för tillgänglighet** i utdatatabellen avgör när aktiviteten körs. Om indatauppsättningar anges kontrollerar aktiviteten om alla indataberoenden är uppfyllda (det vill säga **läget Klar)** innan den börjar köras.

## <a name="copy-activity---faq"></a>Kopiera aktivitet - Vanliga frågor och svar
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Är det bättre att ha en pipeline med flera aktiviteter eller en separat pipeline för varje aktivitet?
Pipelines är tänkta att bunta relaterade aktiviteter. Om de datauppsättningar som ansluter dem inte förbrukas av någon annan aktivitet utanför pipelinen kan du behålla aktiviteterna i en pipeline. På så sätt behöver du inte kedja pipeline aktiva perioder så att de anpassar sig till varandra. Dessutom bevaras dataintegriteten i tabellerna internt i pipelinen bättre när pipelinen uppdateras. Pipeline-uppdatering stoppar i princip alla aktiviteter i pipelinen, tar bort dem och skapar dem igen. Från redigeringsperspektiv kan det också vara lättare att se dataflödet inom relaterade aktiviteter i en JSON-fil för pipelinen.

### <a name="what-are-the-supported-data-stores"></a>Vilka är de datalager som stöds?
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory har stöd för följande datalager. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datalager med * kan finnas lokalt eller på Azure IaaS, och kräver att du installerar [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal/Azure IaaS-dator.

### <a name="what-are-the-supported-file-formats"></a>Vilka filformat stöds?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Var utförs kopieringen?
Mer information finns i avsnittet Globalt [tillgänglig datarörelse.](data-factory-data-movement-activities.md#global) Kort sagt, när ett lokalt datalager är involverat utförs kopieringen av Data Management Gateway i din lokala miljö. Och när dataförflyttningen är mellan två molnlager utförs kopieringen i den region som ligger närmast sink-platsen i samma geografi.

## <a name="hdinsight-activity---faq"></a>HDInsight-aktivitet – Vanliga frågor och svar
### <a name="what-regions-are-supported-by-hdinsight"></a>Vilka regioner stöds av HDInsight?
Se avsnittet Geografisk tillgänglighet i följande artikel: eller [HDInsight-prisinformation][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Vilken region används av ett HDInsight-kluster på begäran?
HDInsight-klustret på begäran skapas i samma region där den lagring som du angav ska användas med klustret finns.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Hur associerar du ytterligare lagringskonton till ditt HDInsight-kluster?
Om du använder ditt eget HDInsight Cluster (BYOC - Bring Your Own Cluster) läser du följande avsnitt:

* [Använda ett HDInsight-kluster med alternativa lagringskonton och metabutiker][hdinsight-alternate-storage]
* [Använda ytterligare lagringskonton med HDInsight Hive][hdinsight-alternate-storage-2]

Om du använder ett kluster på begäran som skapas av datafabrikstjänsten anger du ytterligare lagringskonton för den HDInsight-länkade tjänsten så att datafabrikstjänsten kan registrera dem för din räkning. I JSON-definitionen för den länkade tjänsten på begäran använder du egenskapen **additionalLinkedServiceNames** för att ange alternativa lagringskonton enligt följande JSON-kodavsnitt:

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
I exemplet ovan representerar andraLinkedServiceName1 och otherLinkedServiceName2 länkade tjänster vars definitioner innehåller autentiseringsuppgifter som HDInsight-klustret behöver för att komma åt alternativa lagringskonton.

## <a name="slices---faq"></a>Segment - Vanliga frågor och svar
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Varför är mina indatasegment inte i redoläge?
Ett vanligt misstag är att inte ange **att extern** egenskap ska **vara true** på indatauppsättningen när indata är externa till datafabriken (produceras inte av datafabriken).

I följande exempel behöver du bara ange **externt** till true på **datauppsättning1**.  

**DataFactory1** Pipeline 1: dataset1 -> aktivitet1 -> dataset2 -> activity2 -> dataset3 Pipeline 2: dataset3-> aktivitet3 -> dataset4

Om du har en annan datafabrik med en pipeline som tar datauppsättning4 (producerad av pipeline 2 i datafabrik 1) markerar du datauppsättning4 som en extern datauppsättning eftersom datauppsättningen produceras av en annan datafabrik (DataFactory1, inte DataFactory2).  

**DataFactory2**    
Pipeline 1: datauppsättning4->aktivitet4->datauppsättning5

Om den externa egenskapen är korrekt inställd kontrollerar du om indata finns på den plats som anges i definitionen för indatadatauppsättning.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Hur man kör en skiva vid en annan tidpunkt än midnatt när segmentet produceras dagligen?
Använd **egenskapen offset** för att ange den tidpunkt då du vill att segmentet ska produceras. Mer information om den här egenskapen finns i avsnittet Tillgänglighet för [datauppsättning.](data-factory-create-datasets.md#dataset-availability) Här är ett snabbt exempel:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Dagliga segment börjar klockan **06.00** i stället för standard midnatt.     

### <a name="how-can-i-rerun-a-slice"></a>Hur kan jag köra en skiva igen?
Du kan köra ett segment på ett av följande sätt:

* Använd Övervaka och hantera app för att köra ett aktivitetsfönster eller segment igen. Instruktioner [finns i Kör om valda aktivitetsfönster.](data-factory-monitor-manage-app.md#perform-batch-actions)   
* Klicka på **Kör** i kommandofältet på **DATA SLICE-bladet** för segmentet i Azure-portalen.
* Kör **Set-AzDataFactorySliceStatus** cmdlet med Status inställd **på Väntar på** segmentet.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Mer information om cmdleten finns i [Set-AzDataFactorySliceStatus.][set-azure-datafactory-slice-status]

### <a name="how-long-did-it-take-to-process-a-slice"></a>Hur lång tid tog det att bearbeta en skiva?
Använd Aktivitetsfönsterutforskaren i Monitor & Hantera app för att veta hur lång tid det tog att bearbeta ett datasegment. Mer information finns i Utforskaren för [aktivitetsfönster.](data-factory-monitor-manage-app.md#activity-window-explorer)

Du kan också göra följande i Azure-portalen:  

1. Klicka på **Datasets** panel på **datafabriken** för din datafabrik.
2. Klicka på den specifika datauppsättningen på **datauppsättningsbladet.**
3. Välj det segment som du är intresserad av i listan **Senaste segment** på **TABLE-bladet.**
4. Klicka på aktiviteten som körs från listan **Aktivitetskörningar** på bladet **DATA SLICE.**
5. Klicka **Properties** på egenskapspanelen på bladet **AKTIVITETSKÖRNINGSINFORMATION.**
6. Du bör se fältet **VARAKTIGHET** med ett värde. Det här värdet är den tid det tar att bearbeta segmentet.   

### <a name="how-to-stop-a-running-slice"></a>Hur man stoppar en löpande skiva?
Om du behöver stoppa pipelinen från att köras kan du använda [Cmdlet Suspend-AzDataFactoryPipeline.](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) För närvarande stoppar avbryta pipeline inte segmentkörningar som pågår. När pågående körningar är klara plockas ingen extra skiva upp.

Om du verkligen vill stoppa alla körningar omedelbart, skulle det enda sättet vara att ta bort pipelinen och skapa den igen. Om du väljer att ta bort pipelinen behöver du INTE ta bort tabeller och länkade tjänster som används av pipelinen.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: https://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
