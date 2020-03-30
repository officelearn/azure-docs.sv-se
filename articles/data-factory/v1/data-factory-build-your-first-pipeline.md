---
title: 'Data Factory självstudiekurs: Första datapipeline '
description: Den här Azure Data Factory-självstudien visar hur du skapar och schemalägger en datafabrik som bearbetar data med Hive-skript i ett Hadoop-kluster.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 80644ed2d655544fa176a7be92aec3c01aa3bf14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966078"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Självstudiekurs: Skapa din första pipeline för att omvandla data med Hadoop-klustret
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [Powershell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Mall för Resurshanteraren](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [Quickstart: Create a data factory using Azure Data Factory](../quickstart-create-data-factory-dot-net.md) (Snabbstart: Skapa en datafabrik med Azure Data Factory) om du använder den aktuella versionen av Data Factory-tjänsten.

I den här självstudien skapar du din första Azure-datafabrik med en datapipeline. Pipelinen omvandlar indata genom att köra Hive-skript på ett Azure HDInsight -kluster (Hadoop) för att producera utdata.  

Den här artikeln innehåller översikt och förutsättningar för självstudien. När du har slutfört förutsättningarna kan du göra självstudien med något av följande verktyg/SDK:er: Visual Studio, PowerShell, Resource Manager-mallen, REST API. Välj ett av alternativen i listrutan i början (eller) länkar i slutet av den här artikeln för att göra självstudien med något av dessa alternativ.    

## <a name="tutorial-overview"></a>Självstudier – översikt
I den här självstudien får du göra följande:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera datapipellines som flyttar och omvandlar data.

    I den här självstudien skapar du en pipeline i datafabriken.
2. Skapa en **pipeline**. En pipeline kan ha en eller flera aktiviteter (Exempel: Kopiera aktivitet, HDInsight Hive-aktivitet). Det här exemplet använder HDInsight Hive-aktiviteten som kör ett Hive-skript i ett HDInsight Hadoop-kluster. Skriptet skapar först en tabell som refererar till råwebbloggdata som lagras i Azure blob storage och sedan partitionerar rådata per år och månad.

    I den här självstudien använder pipelinen Hive-aktiviteten för att omvandla data genom att köra en Hive-fråga i ett Azure HDInsight Hadoop-kluster.
3. Skapa **länkade tjänster**. Du skapar en länkad tjänst för att länka ett datalager eller en beräkningstjänst till datafabriken. Ett datalager som Azure Storage innehåller indata/utdata för aktiviteter i pipelinen. En beräkningstjänst som HDInsight Hadoop-kluster bearbetar/omvandlar data.

    I den här självstudien skapar du två länkade tjänster: **Azure Storage** och **Azure HDInsight**. Den Azure Storage-länkade tjänsten länkar ett Azure Storage-konto som innehåller indata-/utdata till datafabriken. Azure HDInsight-länkade tjänsten länkar ett Azure HDInsight-kluster som används för att omvandla data till datafabriken.
3. Skapa indata- och **utdatauppsättningar**. En indatauppsättning representerar indata för en aktivitet i pipeline och en utdatauppsättning representerar utdata för aktiviteten.

    I den här självstudien anger indata- och utdatauppsättningarna platser för indata och utdata i Azure Blob Storage. Den Azure Storage-länkade tjänsten anger vilket Azure Storage-konto som används. En indatauppsättning anger var indatafilerna finns och en utdatauppsättning anger var utdatafilerna placeras.


Se [Artikel Introduktion till Azure Data Factory](data-factory-introduction.md) för en detaljerad översikt över Azure Data Factory.

Här är **diagramvyn för** exempeldatafabriken som du skapar i den här självstudien. **MyFirstPipeline** har en aktivitet av typen Hive som använder **AzureBlobInput-datauppsättning** som indata och producerar **AzureBlobOutput-datauppsättning** som en utdata.

![Diagramvy i självstudiekursen Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


I den här **självstudien** innehåller mappen inputdata i azure blob-behållaren **som påbörjades** en fil med namnet input.log. Den här loggfilen har poster från tre månader: januari, februari och mars 2016. Här är exempelraderna för varje månad i indatafilen.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

När filen bearbetas av pipelinen med HDInsight Hive-aktivitet kör aktiviteten ett Hive-skript i HDInsight-klustret som partitionerar indata efter år och månad. Skriptet skapar tre utdatamappar som innehåller en fil med poster från varje månad.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Från exempelraderna ovan skrivs den första (med 2016-01-01) till filen 000000_0 i mappen month=1. På samma sätt skrivs den andra till filen i mappen month=2 och den tredje skrivs till filen i mappen month=3.  

## <a name="prerequisites"></a>Krav
Innan du börjar den här självstudien måste du ha följande förutsättningar:

1. **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Se gratis [testversion](https://azure.microsoft.com/pricing/free-trial/) artikeln om hur du kan få en gratis provperiod konto.
2. **Azure Storage** – Du använder ett Azure-lagringskonto för att lagra data i den här självstudien. Om du inte har ett Azure-lagringskonto läser du artikeln [Skapa ett lagringskonto.](../../storage/common/storage-account-create.md) När du har skapat lagringskontot noterar du **kontonamnet** och **åtkomstnyckeln**. Information om hur du hämtar åtkomstnycklarna för lagringskonto finns i [Hantera åtkomstnycklar för lagringskonto](../../storage/common/storage-account-keys-manage.md).
3. Ladda ner och granska Hive-frågefilen **(HQL)** som finns på: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Den här frågan omvandlar indata för att producera utdata.
4. Hämta och granska exempelinmatningsfilen **(input.log)** som finns på:[https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Skapa en blob-behållare med namnet **adfgetstarted** i din Azure Blob Storage.
6. Ladda upp **partitionweblogs.hql-filen** till **skriptmappen** i **behållaren adfgetstarted.** Använd verktyg som [Microsoft Azure Storage Explorer](https://storageexplorer.com/).
7. Ladda upp **input.log-filen** till mappen **inputdata** i **behållaren för adfgetstarted.**

När du har slutfört förutsättningarna väljer du något av följande verktyg/SDK:er för att göra självstudien:

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Powershell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Mall för Resurshanteraren](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio är ett GRAFISKT sätt att bygga dina datafabriker. Medan powershell-, Resource Manager-mall- och REST API-alternativen ger skript/programmering sätt att skapa dina datafabriker.

> [!NOTE]
> Datapipelinen i den här självstudien transformerar indata för att generera utdata. Den kopierar inte data från ett källdatalager till ett måldatalager. En självstudiekurs om hur du kopierar data med Azure Data Factory finns i [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Självstudie: Kopiera data från Blob Storage till SQL Database).
>
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory).
