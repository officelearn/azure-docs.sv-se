---
title: Data i Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig hur Azure Machine Learning på ett säkert sätt ansluter till dina data och använder dessa data för maskininlärningsuppgifter.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/20/2020
ms.openlocfilehash: 982c9c9eadec4403c8116430e1e25092de99f1d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128494"
---
# <a name="data-access-in-azure-machine-learning"></a>Dataåtkomst i Azure Machine Learning

Azure Machine Learning gör det enkelt att ansluta till dina data i molnet.  Det ger ett abstraktionslager över den underliggande lagringstjänsten, så att du kan komma åt och arbeta säkert med dina data utan att behöva skriva kod som är specifik för din lagringstyp. Azure Machine Learning innehåller även följande datafunktioner:

*    Versionshantering och spårning av data härstamning
*    Märkning av data 
*    Övervakning av dataavvikelser
*    Interoperabilitet med pandor och Spark DataFrames

## <a name="data-workflow"></a>Arbetsflöde för data

När du är redo att använda data i din molnbaserade lagringslösning rekommenderar vi följande arbetsflöde för dataleverans. Det här arbetsflödet förutsätter att du har ett [Azure-lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) och data i en molnbaserad lagringstjänst i Azure. 

1. Skapa ett [Azure Machine Learning-datalager](#datastores) för att lagra anslutningsinformation till din Azure-lagring.

2. Från det datalagret skapar du en [Azure Machine Learning-datauppsättning](#datasets) för att peka på en eller flera specifika filer i det underliggande lagringsutrymmet. 

3. Om du vill använda den datauppsättningen i maskininlärningsexperimentet kan du antingen
    1. Montera den på experimentets beräkningsmål för modellutbildning.

        **Eller** 

    1. Använd den direkt i Azure Machine Learning-lösningar som automatiserade datorinlärningsexperimentkörningar (automated ML), pipelines för maskininlärning eller [Azure Machine Learning-designern](concept-designer.md).

4. Skapa [datauppsättningsövervakare](#data-drift) för modellutdatauppsättningen för att identifiera för datadrift. 

5. Om datadrift upptäcks uppdaterar du indatauppsättningen och tränar om modellen därefter.

Följande diagram ger en visuell demonstration av det rekommenderade arbetsflödet.

![Diagram över datakoncept](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Datalager

Azure Machine Learning-datalager behåller anslutningsinformationen till din Azure-lagring på ett säkert sätt, så att du inte behöver koda den i skripten. [Registrera och skapa ett datalager](how-to-access-data.md) för att enkelt ansluta till ditt lagringskonto och komma åt data i din underliggande Azure-lagringstjänst. 

Molnbaserade lagringstjänster som stöds i Azure och som kan registreras som datalager:

+ Azure Blob-behållare
+ Azure-filresurs
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks-filsystem
+ Azure Database for MySQL

## <a name="datasets"></a>Datauppsättningar

Azure Machine Learning-datauppsättningar är referenser som pekar på data i din lagringstjänst. De är inte kopior av dina data, så ingen extra lagringskostnad uppstår. Om du vill interagera med dina data i lagring [skapar du en datauppsättning](how-to-create-register-datasets.md) för att paketera dina data till ett förbrukningsobjekt för maskininlärningsuppgifter. Registrera datauppsättningen på arbetsytan för att dela och återanvända den i olika experiment utan datainmatningskomplexiteter.

Datauppsättningar kan skapas från lokala filer, offentliga url:ar, [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)eller Azure-lagringstjänster via datalager. Om du vill skapa en datauppsättning från en dataram för i minnet pandas skriver du data till en lokal fil, till exempel en parkett, och skapar datauppsättningen från filen.  

Vi stöder 2 typer av datauppsättningar: 
+ En [tabelldatauppsättning](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representerar data i tabellformat genom att tolka den angivna filen eller listan med filer. Du kan ladda en Tabelldatauppsättning i en Pandas eller Spark DataFrame för ytterligare manipulering och rengöring. En fullständig lista över dataformat som du kan skapa Tabelldatauppsättningar från finns i [klassen TabellformDatasetFactory](https://aka.ms/tabulardataset-api-reference).

+ En [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) refererar till en eller flera filer i dina datalager eller offentliga webbadresser. Du kan [hämta eller montera filer](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) som refereras av FileDatasets till ditt beräkningsmål.

Ytterligare datauppsättningar finns i följande dokumentation:

+ [Version och spåra](how-to-version-track-datasets.md) datauppsättningslinje.
+ [Övervaka din datauppsättning](how-to-monitor-datasets.md) för att hjälpa till med identifiering av datadrift.    

## <a name="work-with-your-data"></a>Arbeta med dina data

Med datauppsättningar kan du utföra ett antal maskininlärningsuppgifter genom sömlös integrering med Azure Machine Learning-funktioner. 

+ Skapa ett [datamärkningsprojekt](#label).
+ Träna maskininlärningsmodeller:
     + [automatiserade ML-experiment](how-to-use-automated-ml-for-ml-models.md)
     + [formgivaren](tutorial-designer-automobile-price-train-score.md#import-data)
     + [Bärbara datorer](how-to-train-with-datasets.md)
     + [Azure Machine Learning-pipelines](how-to-create-your-first-pipeline.md)
+ Få tillgång till datauppsättningar för bedömning med [batchinferens](how-to-use-parallel-run-step.md) i [machine learning-pipelines](how-to-create-your-first-pipeline.md).
+ Konfigurera en datauppsättningsövervakare för [identifiering av datadrift.](#drift)

<a name="label"></a>

## <a name="data-labeling"></a>Märkning av data

Märkning av stora mängder data har ofta varit en huvudvärk i maskininlärningsprojekt. De med en datorseende komponent, såsom bildklassificering eller objektidentifiering, kräver i allmänhet tusentals bilder och motsvarande etiketter.

Azure Machine Learning ger dig en central plats för att skapa, hantera och övervaka märkningsprojekt. Märkprojekt hjälper till att samordna data, etiketter och gruppmedlemmar, så att du kan hantera märkningsuppgifterna mer effektivt. Uppgifter som stöds är bildklassificering, antingen multietikett eller flerklass, och objektidentifiering med hjälp av avgränsade rutor.

Skapa ett [datamärkningsprojekt](how-to-create-labeling-projects.md)och mata ut en datauppsättning för användning i maskininlärningsexperiment.

<a name="drift"></a>

## <a name="data-drift"></a>Data drift

I samband med maskininlärning är datadrift förändringen i modellindata som leder till försämrad modellprestanda. Det är en av de främsta anledningarna till att modellens noggrannhet försämras med tiden, vilket innebär att övervakning av datadrift hjälper till att identifiera prestandaproblem för modeller.

Se artikeln [Skapa en datauppsättningsövervakare](how-to-monitor-datasets.md) om du vill veta mer om hur du identifierar och varnar för data för att driva på nya data i en datauppsättning.

## <a name="next-steps"></a>Nästa steg 

+ Skapa en datauppsättning i Azure Machine Learning studio eller med Python SDK [med hjälp av dessa steg.](how-to-create-register-datasets.md)
+ Prova exempel på datauppsättningsträning med våra [exempel anteckningsböcker.](https://aka.ms/dataset-tutorial)
+ Exempel på datadrift finns i den här [självstudiekursen för datadrift](https://aka.ms/datadrift-notebook).
