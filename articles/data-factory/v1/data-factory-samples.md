---
title: Azure Data Factory - exempel
description: "Innehåller information om prover som levereras med Azure Data Factory-tjänsten."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 4241aedebd388f24834e573fac7a8a61371e728a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory - exempel
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [PowerShell-exempel i Data Factory version 2](../samples-powershell.md) och [kodexempel i kodexempel för Azure-galleriet](https://azure.microsoft.com/en-us/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Exemplen på GitHub
Den [GitHub Azure-DataFactory databasen](https://github.com/azure/azure-datafactory) innehåller flera exempel som hjälper dig snabbt ramp in med Azure Data Factory-tjänsten (eller) Ändra skripten och använda den i egna program. Mappen Samples\JSON innehåller JSON kodavsnitt för vanliga scenarier.

| Exempel | Beskrivning |
|:--- |:--- |
| [ADF genomgång](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |Det här exemplet innehåller en genomgång för slutpunkt till slutpunkt för bearbetning av loggfiler med Azure Data Factory för att aktivera data från loggfiler i insikter. <br/><br/>I den här genomgången Data Factory-pipelinen samlar in exempel loggar, processer och förbättra data från loggar med referensdata och omvandlar data för att utvärdera effektiviteten hos en marknadsföringskampanj startades senast. |
| [JSON-exempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |Det här exemplet innehåller JSON-exempel för vanliga scenarier. |
| [HTTP-Installationshämtaren datasampel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |Det här exemplet showcases hämtning av data från en HTTP-slutpunkt till Azure Blob Storage med hjälp av anpassad .NET-aktivitet. |
| [Mellan AppDomain-punkt Net aktivitet exempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Det här exemplet kan du skapa en anpassad .NET-aktivitet som inte är begränsad till sammansättningen versioner som används av ADF programstart (till exempel WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x osv.). |
| [Kör R-skriptet](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |Det här exemplet innehåller den anpassade Data Factory-aktiviteten som kan användas för att anropa RScript.exe. Det här exemplet fungerar bara med din egen (inte på begäran) HDInsight-kluster som redan har R installerat på den. |
| [Anropa jobb Spark på HDInsight Hadoop-kluster](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |Det här exemplet visas hur du använder MapReduce activity för att anropa ett Spark-program. Spark kopieras bara data från Azure Blob-behållare till en annan. |
| [Twitter-analys med Azure Machine Learning bedömningen batchaktiviteten](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Det här exemplet visas hur du använder AzureMLBatchScoringActivity för att anropa en Azure Machine Learning-modell som utför twitter sentiment analys, bedömningen förutsägelse osv. |
| [Twitter-analys med anpassad aktivitet](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Det här exemplet visas hur du använder en anpassad aktivitet för .NET för att anropa en Azure Machine Learning-modell som utför twitter sentiment analys, bedömningen förutsägelse osv. |
| [Parametriserade Pipelines för Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |Exemplet innehåller en slutpunkt till slutpunkt C#-kod för att distribuera N pipelines för bedömningen och omtränings var och en med en annan region parameter var listan över regioner kommer från en parameters.txt-fil som ingår i det här exemplet. |
| [Referens för datauppdatering för Azure Stream Analytics-jobb](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |Det här exemplet visas hur du använder Azure Data Factory och Azure Stream Analytics att köra frågor med referensdata och konfigurera uppdatering för referensdata enligt ett schema. |
| [Hybrid Pipeline med lokala Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |Ett lokalt Hadoop-kluster används som mål för en beräkning för att köra jobb i Data Factory precis som du lägger till andra beräknings-mål som ett HDInsight-baserat Hadoop-kluster i molnet. |
| [JSON-konverteringsverktyg](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |Det här verktyget kan du konvertera JSONs från version innan 2015-07-01-preview till senaste eller 2015-07-01-preview (standard). |
| [Indatafilen för U-SQL-exempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |Den här filen är en exempelfil som används av en U-SQL-aktivitet. |
| [Ta bort blob-fil](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity) | Det här exemplet innehåller en C#-fil som kan användas som en del av ADF anpassad .net-aktivitet för att ta bort filer från källan Azure Blob-plats när filerna har kopierats.|

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
Du hittar följande Azure Resource Manager-mallar för Data Factory på GitHub.

| Mall | Beskrivning |
| --- | --- |
| [Kopiera från Azure Blobblagring till Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Distribuera den här mallen skapar ett Azure data factory med en pipeline som kopierar data från angivna Azure blob storage till Azure SQL-databas |
| [Kopiera från Salesforce till Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Distribuera den här mallen skapar ett Azure data factory med en pipeline som kopierar data från det angivna Salesforce-kontot till Azure blob storage. |
| [Transformera data genom att köra Hive-skript på Azure HDInsight-kluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Distribuera den här mallen skapar ett Azure data factory med en rörledning som omvandlar data genom att köra exemplet Hive-skript i ett Azure HDInsight Hadoop-kluster. |

## <a name="samples-in-azure-portal"></a>Exempel på Azure-portalen
Du kan använda den **exempel pipelines** panelen på startsidan på din data factory för att distribuera exempel pipelines och deras associerade enheterna (datauppsättningar och länkade tjänster) i till din data factory.

1. Skapa en datafabrik eller öppna en befintlig datafabrik. Se [kopiera data från Blob Storage till SQL-databas med hjälp av Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) steg för att skapa en datafabrik.
2. I den **DATAFABRIKEN** bladet för data factory klickar du på den **exempel pipelines** panelen.

    ![Exempel pipelines sida vid sida](./media/data-factory-samples/SamplePipelinesTile.png)
3. I den **exempel pipelines** bladet, klickar du på den **exempel** som du vill distribuera.

    ![Exempel pipelines bladet](./media/data-factory-samples/SampleTile.png)
4. Ange konfigurationsinställningar för. Till exempel ditt Azure storage-konto namn och kontonyckel, Azure SQL-servernamnet, databas, användar-ID och lösenord, osv.

    ![Exempel-bladet](./media/data-factory-samples/SampleBlade.png)
5. När du är klar med att ange inställningarna klickar du på **skapa** att skapa/distribuera exempel pipelines och länkade tjänster/tabeller som används av pipelines.
6. Du ser statusen för distributionen på panelen exempel du klickade på tidigare på den **exempel pipelines** bladet.

    ![Status för distribution](./media/data-factory-samples/DeploymentStatus.png)
7. När du ser den **distributionen lyckades** meddelande på panelen för det här exemplet Stäng den **exempel pipelines** bladet.  
8. På **DATA FACTORY** bladet som du ser att länkade tjänster, datauppsättningar och pipelines läggs till din data factory.  

    ![Bladet Datafabrik](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Exempel i Visual Studio
### <a name="prerequisites"></a>Krav
Du måste ha följande installerat på datorn:

* Visual Studio 2013 eller Visual Studio 2015
* Hämta Azure SDK för Visual Studio 2013 eller Visual Studio 2015. Gå till [Azures hämtningssida](https://azure.microsoft.com/downloads/) och klicka på **VS 2013** eller **VS 2015** i **.NET**-avsnittet.
* Hämta det senaste Azure Data Factory-plugin-programmet för Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Om du använder Visual Studio 2013 kan du även uppdatera plugin-programmet genom att göra följande: I menyn klickar du på **Verktyg** -> **Tillägg och uppdateringar** -> **Online** -> **Visual Studio-galleriet** -> **Microsoft Azure Data Factory-verktyg för Visual Studio** -> **Uppdatera**.

### <a name="use-data-factory-templates"></a>Använd Data Factory-mallar
1. Klicka på **filen** på menyn, peka på **ny**, och klicka på **projekt**.
2. I den **nytt projekt** dialogrutan gör du följande:

   1. Välj **DataFactory** under **mallar**.
   2. Välj **Data Factory mallar** i den högra rutan.
   3. Ange en **namn** för projektet.
   4. Välj en **plats** för projektet.
   5. Klicka på **OK**.

      ![Dialogrutan Nytt projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. I den **Data Factory mallar** dialogrutan Välj exempelmall från den **användningsfall mallar** avsnittet och klicka på **nästa**. I följande steg beskriver hur du använder den **kunden profilering** mall. Stegen är liknande för andra exemplen.

    ![Dialogrutan för data Factory-mallar](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. I den **Konfigurationshanteraren för Data Factory** dialogrutan klickar du på **nästa** på den **Data Factory grunderna** sidan.
5. På den **konfigurera datafabriken** gör du följande:
   1. Välj **skapa nya Data Factory**. Du kan också välja **använda befintlig datafabrik**.
   2. Ange en **namn** för data factory.
   3. Välj den **Azure-prenumeration** som du vill att datafabriken ska skapas.
   4. Välj den **resursgruppen** för data factory.
   5. Välj den **västra USA**, **östra USA**, eller **Nordeuropa** för den **region**.
   6. Klicka på **Nästa**.
6. I den **konfigurera datalager** anger du en befintlig **Azure SQL database** och **Azure storage-konto** (eller) skapa databaslagring/och klicka på Nästa.
7. I den **konfigurerar beräknings** , Välj standardvärden och klicka på **nästa**.
8. I den **sammanfattning** , granska alla inställningar och klicka på **nästa**.
9. I den **Distributionsstatus** , vänta tills distributionen är klar och klicka på **Slutför**.
10. I Solution Explorer högerklickar du på projektet och klickar sedan på **Publicera**.
11. Om du ser dialogrutan **Logga in på ditt Microsoft-konto**, anger du dina autentiseringsuppgifter för det konto som har Azure-prenumerationen. Klicka sedan på **Logga in**.
12. Du bör se följande dialogruta:

    ![Dialogrutan Publicera](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. På sidan **Konfigurera datafabrik** går du igenom följande steg:

    1. Bekräfta att **använda befintlig datafabrik** alternativet.
    2. Välj den **datafabriken** du hade väljer när du använder mallen.
    3. Klicka på **Nästa** för att växla till sidan **Publicera objekt**. (Tryck på **TAB** för att flytta ut från namnfältet om knappen **Nästa** är inaktiverad.)
14. På sidan **Publicera objekt** kontrollerar du att alla datafabriksentiteter har valts. Klicka på **Nästa** för att växla till sidan **Sammanfattning**.     
15. Granska sammanfattningen och klicka på **Nästa** för att starta distributionsprocessen och visa **Distributionsstatus**.
16. På sidan **Distributionsstatus** bör du se statusen för distributionen. Klicka på Slutför när distributionen är klar.

Se [skapa din första data factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) mer information om hur du använder Visual Studio för att utforma Data Factory entiteter och publicera dem till Azure.          
