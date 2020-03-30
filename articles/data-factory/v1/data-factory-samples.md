---
title: Azure Data Factory - exempel
description: Innehåller information om exempel som levereras med Azure Data Factory-tjänsten.
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
ms.openlocfilehash: 63db1810cbdd2133bc0577530325351997b31f30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70139268"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory - exempel
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [PowerShell-exempel i Data Factory](../samples-powershell.md) och [kodexempel i galleriet Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Exempel på GitHub
[GitHub Azure-DataFactory-databasen](https://github.com/azure/azure-datafactory) innehåller flera exempel som hjälper dig att snabbt rampa upp med Azure Data Factory-tjänsten (eller) ändra skripten och använda den i ett eget program. Mappen Samples\JSON innehåller JSON-utdrag för vanliga scenarier.

| Exempel | Beskrivning |
|:--- |:--- |
| [ADF Genomgång](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Det här exemplet innehåller en heltäckande genomgång för bearbetning av loggfiler med Azure Data Factory för att omvandla data från loggfiler till insikter. <br/><br/>I den här genomgången samlar datafabrikspipelinen in exempelloggar, bearbetar och berikar data från loggar med referensdata och omvandlar data för att utvärdera effektiviteten i en marknadsföringskampanj som nyligen lanserades. |
| [JSON-prover](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Det här exemplet innehåller JSON-exempel för vanliga scenarier. |
| [Exempel på http-datahämtningsare](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |Det här exemplet visar hämtning av data från en HTTP-slutpunkt till Azure Blob Storage med anpassad .NET-aktivitet. |
| [Exempel på aktivitetsaktivitet för korsappdomänpunkt](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Med det här exemplet kan du skapa en anpassad .NET-aktivitet som inte är begränsad till sammansättningsversioner som används av ADF-startprogrammet (till exempel WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.). |
| [Kör R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Det här exemplet innehåller den anpassade aktivitet för Data Factory som kan användas för att anropa RScript.exe. Det här exemplet fungerar bara med ditt eget (inte on-demand) HDInsight-kluster som redan har R Installerat på det. |
| [Anropa Spark-jobb på HDInsight Hadoop-kluster](https://docs.microsoft.com/azure/data-factory/tutorial-transform-data-spark-portal) |Det här exemplet visar hur du använder MapReduce-aktivitet för att anropa ett Spark-program. Spark-programmet kopierar bara data från en Azure Blob-behållare till en annan. |
| [Twitter-analys med azure machine learning batch scoring aktivitet](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Det här exemplet visar hur du använder AzureMLBatchScoringActivity för att anropa en Azure Machine Learning-modell som utför sentimentanalys, bedömning av twitter, bedömning, förutsägelse osv. |
| [Twitter-analys med anpassad aktivitet](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Det här exemplet visar hur du använder en anpassad .NET-aktivitet för att anropa en Azure Machine Learning-modell som utför sentimentanalys, bedömning, förutsägelse osv. |
| [Parameteriserade pipelines för Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |Exemplet innehåller en C#-kod från slutna till slutna dagar för att distribuera N-pipelines för bedömning och omskolning var och en med en annan regionparameter där listan över regioner kommer från en parameters.txt-fil, som ingår i det här exemplet. |
| [Referensdatauppdatering för Azure Stream Analytics-jobb](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Det här exemplet visar hur du använder Azure Data Factory och Azure Stream Analytics tillsammans för att köra frågor med referensdata och konfigurera uppdateringen för referensdata enligt ett schema. |
| [Hybrid pipeline med lokala Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |Exemplet använder ett lokalt Hadoop-kluster som ett beräkningsmål för att köra jobb i Data Factory precis som du vill lägga till andra beräkningsmål som ett HDInsight-baserat Hadoop-kluster i molnet. |
| [JSON-konverteringsverktyg](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Med det här verktyget kan du konvertera JSONs från version före 2015-07-01-preview till senaste eller 2015-07-01-preview (standard). |
| [U-SQL-exempelindatafil](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Den här filen är en exempelfil som används av en U-SQL-aktivitet. |
| [Ta bort blob-fil](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | Det här exemplet visar en C#-fil som kan användas som en del av ADF-anpassad .net-aktivitet för att ta bort filer från källplatsen Azure Blob när filerna har kopierats.|

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
Du hittar följande Azure Resource Manager-mallar för Data Factory på GitHub.

| Mall | Beskrivning |
| --- | --- |
| [Kopiera från Azure Blob Storage till Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Distribuera den här mallen skapar en Azure-datafabrik med en pipeline som kopierar data från den angivna Azure blob lagring till Azure SQL-databasen |
| [Kopiera från Salesforce till Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Om du distribuerar den här mallen skapas en Azure-datafabrik med en pipeline som kopierar data från det angivna Salesforce-kontot till Azure-blob-lagringen. |
| [Omvandla data genom att köra Hive-skript i ett Azure HDInsight-kluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Om du distribuerar den här mallen skapas en Azure-datafabrik med en pipeline som omvandlar data genom att köra exempeldata från Hive-skriptet i ett Azure HDInsight Hadoop-kluster. |

## <a name="samples-in-azure-portal"></a>Exempel i Azure-portalen
Du kan använda panelen **Exempelpipelar** på startsidan för datafabriken för att distribuera exempelpipelor och tillhörande entiteter (datauppsättningar och länkade tjänster) till datafabriken.

1. Skapa en datafabrik eller öppna en befintlig datafabrik. Se [Kopiera data från Blob Storage till SQL Database med Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg för att skapa en datafabrik.
2. Klicka på panelen **Exempelpipellines** i **datafabriken** för datafabriken.

    ![Exempel på pipelines-panel](./media/data-factory-samples/SamplePipelinesTile.png)
3. Klicka på det **exempel** som du vill distribuera i bladet **Exempelpipelar.**

    ![Exempel på rörledningar blad](./media/data-factory-samples/SampleTile.png)
4. Ange konfigurationsinställningar för exemplet. Till exempel ditt Azure-lagringskontonamn och kontonyckel, Azure SQL-servernamn, databas, användar-ID och lösenord osv.

    ![Provblad](./media/data-factory-samples/SampleBlade.png)
5. När du är klar med att ange konfigurationsinställningarna klickar du på **Skapa** för att skapa/distribuera exempelpipelarna och länkade tjänster/tabeller som används av pipelines.
6. Du ser statusen för distributionen på exempelpanelen som du klickade på tidigare på **exempelpipelornas pipelines-blad.**

    ![Status för distribution](./media/data-factory-samples/DeploymentStatus.png)
7. När distributionen **har slutförts** på panelen för exemplet stänger du bladet **Exempelpipelar.**  
8. På **datafabriksbladet** ser du att länkade tjänster, datauppsättningar och pipelines läggs till i datafabriken.  

    ![Bladet Datafabrik](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Exempel i Visual Studio
### <a name="prerequisites"></a>Krav
Du måste ha följande installerat på datorn:

* Visual Studio 2013 eller Visual Studio 2015
* Hämta Azure SDK för Visual Studio 2013 eller Visual Studio 2015. Gå till [Azures hämtningssida](https://azure.microsoft.com/downloads/) och klicka på **VS 2013** eller **VS 2015** i **.NET**-avsnittet.
* Hämta det senaste Azure Data Factory-plugin-programmet för Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Om du använder Visual Studio 2013 kan du också uppdatera insticksprogrammet genom att göra följande steg: Klicka på -> **Verktygstillägg och Uppdateringar** -> **Online** -> **Visual Studio Gallery** -> **Microsoft Azure Data Factory Tools för Visual Studio** -> **Update**. **Tools**

### <a name="use-data-factory-templates"></a>Använda datafabriksmallar
1. Klicka på **Arkiv** på menyn, peka på **Nytt**och klicka på **Projekt**.
2. Gör följande steg i dialogrutan **Nytt projekt**:

   1. Välj **DataFactory** under **Mallar**.
   2. Välj **Data Factory-mallar** i den högra rutan.
   3. Ange ett **namn** för projektet.
   4. Välj en **plats** för projektet.
   5. Klicka på **OK**.

      ![Dialogrutan Nytt projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. I dialogrutan **Data fabriksmallar** markerar du exempelmallen i avsnittet **Använd-ärende-mallar** och klickar på **Nästa**. Följande steg går igenom med mallen **Kundprofilering.** Stegen är likartade för de andra exemplen.

    ![Dialogrutan Datafabriksmallar](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. Klicka på **Nästa** på sidan **Data Factory Basics** i dialogrutan Konfiguration av **datafabrik.**
5. Gör följande på sidan **Konfigurera datafabrik:**
   1. Välj **Skapa ny datafabrik**. Du kan också välja **Använd befintlig datafabrik**.
   2. Ange ett **namn** på datafabriken.
   3. Välj den **Azure-prenumeration** där du vill att datafabriken ska skapas.
   4. Välj **resursgruppen** för datafabriken.
   5. Välj **västra USA,** **östra USA**eller **norra Europa** för **regionen**.
   6. Klicka på **Nästa**.
6. På sidan **Konfigurera datalager** anger du en befintlig **Azure SQL-databas** och **Azure storage-konto** (eller) skapar databas/lagring och klickar på Nästa.
7. På sidan **Konfigurera beräkning** väljer du standardvärden och klickar på **Nästa**.
8. Granska alla inställningar på sidan **Sammanfattning** och klicka på **Nästa**.
9. Vänta tills distributionsstatusen är klar på sidan **Distributionsstatus** och klicka på **Slutför**.
10. I Solution Explorer högerklickar du på projektet och klickar sedan på **Publicera**.
11. Om du ser dialogrutan **Logga in på ditt Microsoft-konto**, anger du dina autentiseringsuppgifter för det konto som har Azure-prenumerationen. Klicka sedan på **Logga in**.
12. Du bör se följande dialogruta:

    ![Dialogrutan Publicera](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. På sidan **Konfigurera datafabrik** går du igenom följande steg:

    1. Bekräfta att **alternativet Använd befintlig datafabrik.**
    2. Välj den **datafabrik** du hade välja när du använde mallen.
    3. Klicka på **Nästa** för att växla till sidan **Publicera objekt**. (Tryck på **TAB** för att flytta ut från namnfältet om knappen **Nästa** är inaktiverad.)
14. På sidan **Publicera objekt** kontrollerar du att alla datafabriksentiteter har valts. Klicka på **Nästa** för att växla till sidan **Sammanfattning**.     
15. Granska sammanfattningen och klicka på **Nästa** för att starta distributionsprocessen och visa **Distributionsstatus**.
16. På sidan **Distributionsstatus** bör du se statusen för distributionen. Klicka på Slutför när distributionen är klar.

Mer information om hur du använder Visual Studio för att skapa datafabriker och publicera dem på Azure finns i [Skapa din första datafabrik (Visual Studio).](data-factory-build-your-first-pipeline-using-vs.md)          
