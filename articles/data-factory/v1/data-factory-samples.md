---
title: Azure Data Factory - exempel
description: Innehåller information om exemplen som levereras med Azure Data Factory-tjänsten.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 03127dc777588f669ef07af52c8f73d986bfe0ea
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020820"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory - exempel
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [PowerShell-exempel i Data Factory](../samples-powershell.md) och [kodexempel i kodexempel för Azure-galleriet](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Exempel på GitHub
Den [GitHub Azure-DataFactory databasen](https://github.com/azure/azure-datafactory) innehåller flera exempel som hjälper dig att snabbt plugga med Azure Data Factory-tjänsten (eller) Ändra skripten och använda egna program. Mappen Samples\JSON innehåller JSON-kodfragment för vanliga scenarier.

| Exempel | Beskrivning |
|:--- |:--- |
| [Genomgång av ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |Det här exemplet innehåller en slutpunkt till slutpunkt-genomgång för att bearbeta loggfiler med hjälp av Azure Data Factory för att förvandla data från loggfiler i till insikter. <br/><br/>I den här genomgången Data Factory-pipeline samlar in exempelloggar, processer och berikar data från loggar med referensdata och omvandlar data för att utvärdera effekten av en marknadsföringskampanj som startades senast. |
| [JSON-exempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |Det här exemplet visar JSON-exempel för vanliga scenarier. |
| [Data om HTTP-Installationshämtaren exempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |Det här exemplet presenterar hämtning av data från en HTTP-slutpunkt i Azure Blob Storage med hjälp av anpassad .NET-aktivitet. |
| [Mellan AppDomain punkt Net aktivitet exemplet](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Det här exemplet kan du skapa en anpassad .NET-aktivitet som inte är begränsad till sammansättningen-versioner som används av ADF-startprogrammet (till exempel WindowsAzure.Storage verze 4.3.0, Newtonsoft.Json v6.0.x osv.). |
| [Köra R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |Det här exemplet innehåller den anpassade aktiviteten i Data Factory som kan användas för att anropa RScript.exe. Det här exemplet fungerar bara med din egen (inte) HDInsight-kluster på begäran som redan finns R har installerats på datorn. |
| [Anropa Spark-jobb på HDInsight Hadoop-kluster](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |Det här exemplet visar hur du använder MapReduce-aktivitet för att anropa en Spark-programmet. Spark-programmet kopierar bara data från Azure Blob-behållare till en annan. |
| [Twitter-analys med hjälp av Azure Machine Learning Batch bedömning aktivitet](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Detta exempel visar hur du använder AzureMLBatchScoringActivity för att anropa en Azure Machine Learning-modell som utför attitydanalys för twitter, bedömning förutsägelse osv. |
| [Twitter-analys med anpassad aktivitet](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Det här exemplet visar hur du använder en anpassad .NET-aktivitet för att anropa en Azure Machine Learning-modell som utför attitydanalys för twitter, bedömning förutsägelse osv. |
| [Parametriserade Pipelines för Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |Det innehåller en slutpunkt till slutpunkt C# kod för att distribuera N pipelines för bedömning och träna var och en med en annan region parameter där listan över regioner som kommer från en parameters.txt-fil som ingår i det här exemplet. |
| [Referens för datauppdatering för Azure Stream Analytics-jobb](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |Det här exemplet visar hur du använder Azure Data Factory och Azure Stream Analytics tillsammans att köra frågor med referensdata och installera uppdateringar för referensdata enligt ett schema. |
| [Hybrid-Pipeline med en lokal Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |Exemplet använder ett lokalt Hadoop-kluster som en beräkningsmål för att köra jobb i Data Factory, precis som du skulle lägga till andra beräkningsmål som ett HDInsight-baserat Hadoop-kluster i molnet. |
| [JSON-verktyget för konvertering](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |Det här verktyget kan du konvertera för från tidigare 2015-07-01-preview till senaste eller 2015-07-01-preview (standard). |
| [Indatafilen för U-SQL-exempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |Den här filen är en exempelfil som används av en U-SQL-aktivitet. |
| [Ta bort blob-fil](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity) | Det här exemplet visar en C# -fil som kan användas som en del av ADF anpassad .net-aktivitet för att ta bort filer från Azure Blob-plats-källan när filerna har kopierats.|

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
Du hittar följande Azure Resource Manager-mallar för Data Factory på GitHub.

| Mall | Beskrivning |
| --- | --- |
| [Kopiera från Azure Blob Storage till Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Distribuera den här mallen skapar en Azure-datafabrik med en pipeline som kopierar data från den angivna Azure blobblagringen till Azure SQL-databas |
| [Kopiera från Salesforce till Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Distribuera den här mallen skapar en Azure-datafabrik med en pipeline som kopierar data från det angivna Salesforce-kontot till Azure blob storage. |
| [Transformera data genom att köra Hive-skript på ett Azure HDInsight-kluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Distribuera den här mallen skapar en Azure-datafabrik med en pipeline som transformerar data genom att köra Hive-skriptet för exemplet på ett Azure HDInsight Hadoop-kluster. |

## <a name="samples-in-azure-portal"></a>Exempel på Azure-portalen
Du kan använda den **exempel pipelines** panelen på startsidan på din datafabrik för att distribuera exemplen och deras associerade entiteter (datauppsättningar och länkade tjänster) i till din datafabrik.

1. Skapa en data factory eller öppna en befintlig datafabrik. Se [kopiera data från Blob Storage till SQL Database med Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) steg att skapa en datafabrik.
2. I den **DATA FACTORY** bladet för data factory klickar du på den **exempel pipelines** panelen.

    ![Exemplet pipelines panel](./media/data-factory-samples/SamplePipelinesTile.png)
3. I den **exempel pipelines** bladet klickar du på den **exempel** som du vill distribuera.

    ![Exemplet pipelines bladet](./media/data-factory-samples/SampleTile.png)
4. Ange konfigurationsinställningar för exemplet. Till exempel ditt Azure storage-konto och kontonyckeln, Azure SQL-servernamnet, databas, användar-ID och lösenord, osv.

    ![Exempel-bladet](./media/data-factory-samples/SampleBlade.png)
5. När du är klar med att ange konfigurationsinställningarna, klickar du på **skapa** att skapa/distribuera exempelpipelines och länkade tjänster/tabeller som används av pipelines.
6. Du ser status för distributionen på panelen exempel du klickade på tidigare på den **exempel pipelines** bladet.

    ![Status för distribution](./media/data-factory-samples/DeploymentStatus.png)
7. När du ser den **distributionen lyckades** meddelande på panelen för det här exemplet Stäng den **exempel pipelines** bladet.  
8. På **DATA FACTORY** bladet som du ser att länkade tjänster, datauppsättningar och pipeliner läggs till din datafabrik.  

    ![Bladet Datafabrik](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Exemplen i Visual Studio
### <a name="prerequisites"></a>Förutsättningar
Du måste ha följande installerat på datorn:

* Visual Studio 2013 eller Visual Studio 2015
* Hämta Azure SDK för Visual Studio 2013 eller Visual Studio 2015. Gå till [Azures hämtningssida](https://azure.microsoft.com/downloads/) och klicka på **VS 2013** eller **VS 2015** i **.NET**-avsnittet.
* Hämta den senaste Azure Data Factory-plugin-programmet för Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Om du använder Visual Studio 2013 kan uppdatera du även plugin-programmet genom att göra följande: På menyn klickar du på **verktyg** -> **tillägg och uppdateringar** -> **Online** -> **Visual Studio-galleriet**  ->  **Microsoft Azure Data Factory-verktyg för Visual Studio** -> **uppdatering**.

### <a name="use-data-factory-templates"></a>Använd Data Factory-mallar
1. Klicka på **filen** på menyn, peka på **New**, och klicka på **projekt**.
2. Gör följande steg i dialogrutan **Nytt projekt**:

   1. Välj **DataFactory** under **mallar**.
   2. Välj **Data Factory mallar** i den högra rutan.
   3. Ange ett **namn** för projektet.
   4. Välj en **plats** för projektet.
   5. Klicka på **OK**.

      ![Dialogrutan Nytt projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. I den **Data Factory mallar** dialogrutan Välj exempelmallen från den **användningsfall mallar** , och klicka **nästa**. I följande steg beskriver hur du använder den **kunden profilering** mall. Stegen är liknande för andra exempel.

    ![Dialogrutan för data Factory-mallar](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. I den **Datafabrikskonfiguration** dialogrutan klickar du på **nästa** på den **Data Factory grunderna** sidan.
5. På den **konfigurera datafabrik** gör du följande steg:
   1. Välj **Skapa ny Datafabrik**. Du kan också välja **använda befintliga data factory**.
   2. Ange en **namn** för data factory.
   3. Välj den **Azure-prenumeration** som du vill att datafabriken ska skapas.
   4. Välj den **resursgrupp** för data factory.
   5. Välj den **västra USA**, **USA, östra**, eller **Nordeuropa** för den **region**.
   6. Klicka på **Nästa**.
6. I den **konfigurera datalager** anger du en befintlig **Azure SQL-databas** och **Azure storage-konto** (eller) skapa databaslagring/och klicka på Nästa.
7. I den **konfigurera beräkna** , Välj standardvärden och klicka på **nästa**.
8. I den **sammanfattning** , granska alla inställningar och klicka på **nästa**.
9. I den **Distributionsstatus** , vänta tills distributionen är klar och klicka på **Slutför**.
10. I Solution Explorer högerklickar du på projektet och klickar sedan på **Publicera**.
11. Om du ser dialogrutan **Logga in på ditt Microsoft-konto**, anger du dina autentiseringsuppgifter för det konto som har Azure-prenumerationen. Klicka sedan på **Logga in**.
12. Du bör se följande dialogruta:

    ![Dialogrutan Publicera](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. På sidan **Konfigurera datafabrik** går du igenom följande steg:

    1. Bekräfta att **använda befintliga data factory** alternativet.
    2. Välj den **datafabrik** när du tidigare väljer med hjälp av mallen.
    3. Klicka på **Nästa** för att växla till sidan **Publicera objekt**. (Tryck på **TAB** för att flytta ut från namnfältet om knappen **Nästa** är inaktiverad.)
14. På sidan **Publicera objekt** kontrollerar du att alla datafabriksentiteter har valts. Klicka på **Nästa** för att växla till sidan **Sammanfattning**.     
15. Granska sammanfattningen och klicka på **Nästa** för att starta distributionsprocessen och visa **Distributionsstatus**.
16. På sidan **Distributionsstatus** bör du se statusen för distributionen. Klicka på Slutför när distributionen är klar.

Se [skapa din första datafabrik (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) mer information om hur du använder Visual Studio för att utforma Data Factory-entiteter och publicera dem på Azure.          
