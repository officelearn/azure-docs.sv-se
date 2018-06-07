---
title: 'Data Factory-Självstudier: första data pipeline | Microsoft Docs'
description: Här Azure Data Factory-självstudierna visar hur du skapar och schemalägga en datafabrik som bearbetar data på ett Hadoop-kluster med Hive-skript.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0c115cce7659fe1f10e8d3d9c988748151916ce0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620749"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Självstudier: Skapa din första pipeline för att omvandla data med Hadoop-kluster
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Läs [Quickstart: Create a data factory using Azure Data Factory version 2](../quickstart-create-data-factory-dot-net.md) (Snabbstart: Skapa en datafabrik med Azure Data Factory version 2) om du använder version 2 av Data Factory-tjänsten, som finns tillgänglig som förhandsversion.

I den här självstudiekursen skapar du din första Azure data factory med en rörledning för data. Pipelinen omvandlar inkommande data genom att köra Hive-skript i ett kluster för Azure HDInsight (Hadoop) gav inga utdata.  

Den här artikeln innehåller översikt och förutsättningar för självstudiekursen. När du har slutfört kraven kan du göra kursen med någon av följande verktyg/SDK: Azure-portalen, Visual Studio, PowerShell, Resource Manager-mall, REST-API. Välj något av alternativen i listrutan i början (eller) länkar i slutet av den här artikeln att göra kursen med någon av dessa alternativ.    

## <a name="tutorial-overview"></a>Självstudier – översikt
I den här självstudien får du göra följande:

1. Skapa en **datafabriken**. En datafabrik kan innehålla en eller flera pipelines för data som flyttas och transformera data. 

    I den här självstudiekursen skapar du en pipeline i datafabriken. 
2. Skapa en **pipeline**. En pipeline kan ha en eller flera aktiviteter (exempel: Kopieringsaktiviteten, HDInsight Hive aktivitet). Det här exemplet använder HDInsight Hive-aktivitet som kör en Hive-skript på ett HDInsight Hadoop-kluster. Skriptet skapar en tabell som refererar till rå web loggdata som lagras i Azure blob storage först och sedan partitionerar rådata per år och månad.

    I den här kursen använder pipeline Hive-aktiviteten för att omvandla data genom att köra en Hive-fråga på ett Azure HDInsight Hadoop-kluster. 
3. Skapa **länkade tjänster**. Du skapar en länkad tjänst för att länka ett datalager eller en tjänst för beräkning till datafabriken. Ett datalager, till exempel Azure Storage innehåller in-/ utdata-data för aktiviteter i pipelinen. En tjänst för beräkning, till exempel HDInsight Hadoop-kluster processer/transformeringar data.

    I den här självstudiekursen skapar du två länkade tjänster: **Azure Storage** och **Azure HDInsight**. Azure Storage länkade tjänsten länkar ett Azure Storage-konto som innehåller in-/ utdata-data till datafabriken. Azure HDInsight länkade tjänsten länkar ett Azure HDInsight-kluster som används för att omvandla data till datafabriken. 
3. Skapa ingående och utgående **datauppsättningar**. En indatauppsättning representerar indata för en aktivitet i pipeline och en utdatauppsättning representerar utdata för aktiviteten.

    I den här självstudiekursen ange inkommande och utgående datauppsättningar placeringen av inkommande och utgående data i Azure Blob Storage. Länkad Azure Storage-tjänst anger Azure Storage-konto är används. En inkommande datauppsättning anger inkommande filer finns och en datamängd för utdata anger var utdatafilerna finns där. 


Se [introduktion till Azure Data Factory](data-factory-introduction.md) artikel en detaljerad översikt över Azure Data Factory.
  
Här är den **diagramvy** i exemplet data factory som du skapar i den här kursen. **MyFirstPipeline** har en aktivitet av typen Hive som förbrukar **AzureBlobInput** dataset som indata och ger **AzureBlobOutput** dataset som utdata. 

![Diagramvy i självstudiekursen Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


I den här självstudiekursen **inputdata** mappen för den **adfgetstarted** Azure blob-behållare innehåller en fil med namnet input.log. Loggfilen har poster från tre månader: januari, februari och mars 2016. Här följer exempel rader för varje månad i indatafilen. 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

När filen bearbetas av pipeline med HDInsight Hive aktivitet körs aktiviteten en Hive-skript på HDInsight-klustret att partitioner indata per år och månad. Skriptet skapar tre utdata mapparna som innehåller en fil med poster från varje månad.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Från raderna exemplet ovan, det första (med 2016-01-01) skrivs till filen 000000_0 i månaden = 1 mapp. På liknande sätt kan det andra skrivs till filen i månaden = 2 mappen och tredje en skrivs till filen i månaden = 3 mapp.  

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här självstudien måste du ha följande krav:

1. **Azure-prenumeration** -om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Finns det [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/) artikel om hur du kan skaffa ett kostnadsfritt utvärderingskonto.
2. **Azure Storage** – du använder ett Azure storage-konto för att lagra data i den här kursen. Om du inte har ett Azure storage-konto finns i [skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account) artikel. När du har skapat lagringskontot, notera den **kontonamn** och **åtkomstnyckeln**. Se [visa, kopiera och generera lagring åtkomstnycklar](../../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys).
3. Hämta och granska filen Hive-fråga (**HQL**) finns på: [ https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql ](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Den här frågan transformerar indata för att gav inga utdata. 
4. Hämta och granska inkommande exempelfilen (**input.log**) finns på: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Skapa en blobbbehållare med namnet **adfgetstarted** i Azure Blob Storage. 
6. Överför **partitionweblogs.hql** filen till den **skriptet** mapp i den **adfgetstarted** behållare. Använd verktyg som [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/). 
7. Överför **input.log** filen till den **inputdata** mapp i den **adfgetstarted** behållare. 

Välj något av följande verktyg/SDK behöver kursen när du har slutfört de nödvändiga förutsättningarna: 

- [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Azure-portalen och Visual Studio tillhandahåller GUI sätt för att skapa din datafabriker. Medan PowerShell, Resource Manager-mall och REST-API ger scripting/programmering sätt för att skapa din datafabriker.

> [!NOTE]
> Datapipelinen i den här självstudien transformerar indata för att generera utdata. Den kopierar inte data från ett källdatalager till ett måldatalager. En självstudiekurs om hur du kopierar data med Azure Data Factory finns i [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Självstudie: Kopiera data från Blob Storage till SQL Database).
> 
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory). 





  
