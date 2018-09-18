---
title: 'Data Factory-självstudie: första datapipeline | Microsoft Docs'
description: Den här Azure Data Factory-självstudien visar hur du skapar och schemalägga en datafabrik som bearbetar data via Hive-skript på ett Hadoop-kluster.
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
ms.openlocfilehash: 63ae8699af5213634eeac7dfc5045a3fc888b6c0
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734260"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Självstudie: Skapa din första pipeline för att transformera data med Hadoop-kluster
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [Quickstart: Create a data factory using Azure Data Factory](../quickstart-create-data-factory-dot-net.md) (Snabbstart: Skapa en datafabrik med Azure Data Factory) om du använder den aktuella versionen av Data Factory-tjänsten.

I den här självstudien skapar du din första Azure-datafabrik med en datapipeline. Pipelinen transformerar indata genom att köra Hive-skript på ett Azure HDInsight (Hadoop)-kluster för att producera utdata.  

Den här artikeln innehåller översikt och förutsättningar för självstudien. När du har slutfört förutsättningarna kan du göra självstudien med något av följande verktyg/SDK: erna: Azure-portalen, Visual Studio, PowerShell, REST-API på Resource Manager-mall. Välj något av alternativen i listrutan i början (eller) länkarna i slutet av den här artikeln att göra självstudien med något av följande alternativ.    

## <a name="tutorial-overview"></a>Självstudier – översikt
I den här självstudien får du göra följande:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera datapipelines som flyttar och transformerar data. 

    I de här självstudierna skapar du en pipeline i datafabriken. 
2. Skapa en **pipeline**. En pipeline kan innehålla en eller flera aktiviteter (exempel: Kopieringsaktivitet, HDInsight Hive-aktivitet). Det här exemplet används en HDInsight Hive-aktivitet som kör ett Hive-skript på ett HDInsight Hadoop-kluster. Skriptet skapar först en tabell som refererar till den råa web loggdata som lagras i Azure blob storage och sedan partitionerar rådata per år och månad.

    I den här självstudien använder pipelinen Hive-aktivitet du transformerar data genom att köra en Hive-fråga på ett Azure HDInsight Hadoop-kluster. 
3. Skapa **länkade tjänster**. Du skapar en länkad tjänst länkar ett datalager eller en tjänst för beräkning till datafabriken. Ett datalager som Azure Storage innehåller indata och utdata för aktiviteter i pipelinen. En databearbetningstjänst som HDInsight Hadoop-kluster processer/omvandlingar data.

    I den här självstudien skapar du två länkade tjänster: **Azure Storage** och **Azure HDInsight**. Azure Storage länkade tjänsten länkar ett Azure Storage-konto som innehåller indata/utdata till data factory. Azure HDInsight den länkade tjänsten länkar ett Azure HDInsight-kluster som används för att omvandla data till data factory. 
3. Skapa indata och utdata **datauppsättningar**. En indatauppsättning representerar indata för en aktivitet i pipeline och en utdatauppsättning representerar utdata för aktiviteten.

    I den här självstudien anger uppsättningar för indata och utdata platserna för inkommande och utgående data i Azure Blob Storage. Den länkade Azure Storage-tjänsten anger vad Azure Storage-konto är används. En indatauppsättning anger indatafilerna finns och en datauppsättning för utdata anger var utdatafilerna finns där. 


Se [introduktion till Azure Data Factory](data-factory-introduction.md) artikeln en detaljerad översikt över Azure Data Factory.
  
Här är den **diagramvy** på exemplet datafabriken du skapar i den här självstudien. **MyFirstPipeline** har en aktivitet av typen Hive som förbrukar **AzureBlobInput** datauppsättningen som indata och ger **AzureBlobOutput** datauppsättningen som utdata. 

![Diagramvy i Data Factory-självstudien](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


I de här självstudierna **inputdata** mappen för den **adfgetstarted** Azure blob-behållare som innehåller en fil som heter input.log. Den här loggfilen har poster från tre månader: januari, februari och mars 2016. Här följer exempel rader för varje månad i indatafilen. 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

När filen bearbetas av pipelinen med HDInsight Hive-aktivitet, kör aktiviteten en Hive-skript i HDInsight-klustret att partitioner indata per år och månad. Skriptet skapar tre utdatamappar som innehåller en fil med poster från varje månad.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Från rad exemplet som visas ovan, förstnämnda (med 2016-01-01) skrivs till filen 000000_0 i månaden = 1 mapp. På samma sätt kan den andra som skrivs till filen i månaden = 2 mappen och tredje en skrivs till filen i månaden = 3 mapp.  

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här självstudien måste du ha följande krav:

1. **Azure-prenumeration** -om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Se den [kostnadsfri utvärderingsversion av](https://azure.microsoft.com/pricing/free-trial/) artikel om hur du kan få ett kostnadsfritt konto.
2. **Azure Storage** – du använder ett Azure storage-konto för att lagra data i den här självstudien. Om du inte har ett Azure storage-konto kan du läsa den [skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md) artikeln. När du har skapat lagringskontot, Skriv ned den **kontonamn** och **åtkomstnyckel**. Se [visa, kopiera och återskapa åtkomstnycklar](../../storage/common/storage-account-manage.md#access-keys).
3. Ladda ned och granska filen Hive-fråga (**HQL**) finns på: [ https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql ](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Den här frågan omvandlar indata till utdata. 
4. Ladda ned och granska indata-exempelfilen (**input.log**) finns på: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Skapa en blobbehållare med namnet **adfgetstarted** i Azure Blob Storage. 
6. Ladda upp **partitionweblogs.hql** filen till den **skriptet** mapp i den **adfgetstarted** behållare. Använd verktyg som [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/). 
7. Ladda upp **input.log** filen till den **inputdata** mapp i den **adfgetstarted** behållare. 

När du har slutfört förutsättningarna kan du välja något av följande verktyg/SDK: erna att göra självstudien: 

- [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Azure-portalen och Visual Studio tillhandahåller GUI sätt att skapa din datafabriker. Å andra sidan alternativ för REST API, PowerShell och Resource Manager-mall innehåller skript/programmering sätt att skapa din datafabriker.

> [!NOTE]
> Datapipelinen i den här självstudien transformerar indata för att generera utdata. Den kopierar inte data från ett källdatalager till ett måldatalager. En självstudiekurs om hur du kopierar data med Azure Data Factory finns i [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Självstudie: Kopiera data från Blob Storage till SQL Database).
> 
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory). 





  
