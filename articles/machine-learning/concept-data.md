---
title: Skydda data åtkomst i molnet
titleSuffix: Azure Machine Learning
description: Lär dig hur du ansluter säkert till dina data på ett säkert sätt från Azure Machine Learning och hur du använder data uppsättningar och data lager för ML-aktiviteter. Data lager kan lagra data från en Azure-Blob, Azure Data Lake gen 1 & 2, SQL DB, Databricks,...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/24/2020
ms.custom: devx-track-python
ms.openlocfilehash: dadd3a8316efc5bf090a84a738c8f6da223d4572
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651802"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Skydda data åtkomst i Azure Machine Learning

Azure Machine Learning gör det enkelt att ansluta till dina data i molnet.  Det ger ett abstraktions lager över den underliggande lagrings tjänsten, så att du kan komma åt och arbeta med dina data på ett säkert sätt utan att behöva skriva kod som är unik för din lagrings typ. Azure Machine Learning tillhandahåller även följande data funktioner:

*    Versions hantering och spårning av data härkomst
*    Data etiketter 
*    Övervakning av dataavvikelser
*    Samverkan med Pandas och Spark DataFrames

## <a name="data-workflow"></a>Data arbets flöde

När du är redo att använda data i din molnbaserade lagrings lösning rekommenderar vi följande data leverans arbets flöde. Det här arbets flödet förutsätter att du har ett [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) och data i en molnbaserad lagrings tjänst i Azure. 

1. Skapa ett [Azure Machine Learning data lager](#datastores) för att lagra anslutnings information i Azure Storage.

2. Från det data lagret skapar du en [Azure Machine Learning data uppsättning](#datasets) som pekar på en eller flera filer i den underliggande lagringen. 

3. Om du vill använda den data uppsättningen i dator inlärnings experimentet kan du antingen
    1. Montera den på ditt Experiments beräknings mål för modell träning.

        **ELLER** 

    1. Använd den direkt i Azure Machine Learning lösningar, till exempel automatisk maskin inlärning (automatiserad ML) experiment körningar, maskin inlärnings pipeliner eller [Azure Machine Learning designer](concept-designer.md).

4. Skapa data [uppsättnings övervakare](#data-drift) för data uppsättningen för modell utdata för att identifiera data drift. 

5. Om data påträffas kan du uppdatera din indata-datauppsättning och träna modellen på lämpligt sätt.

Följande diagram innehåller en visuell demonstration av detta rekommenderade arbets flöde.

![Data-Concept-diagram](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Datalager

Azure Machine Learning data lager sparar säkert anslutnings informationen i Azure Storage, så du behöver inte koda den i dina skript. [Registrera och skapa ett data lager](how-to-access-data.md) för att enkelt ansluta till ditt lagrings konto och få åtkomst till data i din underliggande Azure Storage-tjänst. 

Molnbaserade lagrings tjänster som stöds i Azure och som kan registreras som data lager:

+ Azure Blob-behållare
+ Azure-filresurs
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks-filsystem
+ Azure Database for MySQL

## <a name="datasets"></a>Datauppsättningar

Azure Machine Learning data uppsättningar är referenser som pekar på data i lagrings tjänsten. De är inte kopior av dina data, så ingen extra lagrings kostnad uppstår och integriteten hos dina ursprungliga data källor är inte utsatt för risk.

 Om du vill interagera med dina data i lagring [skapar du en data uppsättning](how-to-create-register-datasets.md) för att paketera dina data i ett förbruknings Bart objekt för Machine Learning-uppgifter. Registrera data uppsättningen på din arbets yta för att dela och återanvända den över olika experiment utan komplexa data inmatningar.

Data uppsättningar kan skapas från lokala filer, offentliga URL: er, [öppna data uppsättningar i Azure](https://azure.microsoft.com/services/open-datasets/)eller Azure Storage-tjänster via data lager. Om du vill skapa en data uppsättning från en i minnet Pandas dataframe skriver du data till en lokal fil, t. ex. en Parquet och skapar din data uppsättning från den filen.  

Vi stöder 2 typer av data uppsättningar: 

+ En [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) refererar till en eller flera filer i dina data lager eller offentliga URL: er. Om dina data redan har rensats och är redo att användas i övnings experiment, kan du [Hämta eller montera filer](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) som FileDatasets till ditt beräknings mål.

+ En [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representerar data i tabell format genom att parsa den angivna filen eller listan med filer. Du kan läsa in en TabularDataset i en Pandas eller Spark-DataFrame för ytterligare manipulering och rengöring. En fullständig lista över data format som du kan skapa TabularDatasets från finns i [TabularDatasetFactory-klassen](https://aka.ms/tabulardataset-api-reference).

Ytterligare data uppsättnings funktioner finns i följande dokumentation:

+ [Version och spårning](how-to-version-track-datasets.md) av data uppsättning härkomst.
+ [Övervaka din data uppsättning](how-to-monitor-datasets.md) för att hjälpa till med data avkänning.    

## <a name="work-with-your-data"></a>Arbeta med dina data

Med data uppsättningar kan du utföra ett antal Machine Learning-uppgifter genom sömlös integrering med Azure Machine Learning funktioner. 

+ Skapa ett [projekt med data etiketter](#label).
+ Träna Machine Learning-modeller:
     + [automatiserade ML-experiment](how-to-use-automated-ml-for-ml-models.md)
     + [designern](tutorial-designer-automobile-price-train-score.md#import-data)
     + [antecknings böcker](how-to-train-with-datasets.md)
     + [Azure Machine Learning pipelines](how-to-create-your-first-pipeline.md)
+ Få åtkomst till data uppsättningar för poängsättning med [batch-härledning](how-to-use-parallel-run-step.md) i [Machine Learning-pipeliner](how-to-create-your-first-pipeline.md).
+ Konfigurera en data uppsättnings Övervakare för [data](#drift) avkänning.

<a name="label"></a>

## <a name="data-labeling"></a>Data etiketter

Att märka stora mängder data har ofta varit ett problem i Machine Learning-projekt. De med en komponent för visuellt innehåll, till exempel bild klassificering eller objekt identifiering, kräver vanligt vis tusentals avbildningar och motsvarande etiketter.

Azure Machine Learning ger dig en central plats för att skapa, hantera och övervaka projekt med etiketter. Genom att namnge projekt kan du koordinera data, etiketter och grupp medlemmar, så att du effektivt kan hantera etikett uppgifter. Aktiviteter som stöds för närvarande är bild klassificering, antingen flera etiketter eller flera klasser och objekt identifiering med hjälp av gränser rutor.

Skapa ett [data etiketts projekt](how-to-create-labeling-projects.md)och mata ut en data uppsättning för användning i Machine Learning-experiment.

<a name="drift"></a>

## <a name="data-drift"></a>Data avvikelse

I samband med Machine Learning är data driften ändringen i modell indata som leder till modell prestanda försämring. Det är en av de främsta orsakerna till modell precisionen över tid, vilket innebär att övervakningen av data driften hjälper till att identifiera problem med modell prestanda.

Se artikeln [skapa en data uppsättnings övervakare](how-to-monitor-datasets.md) för att lära dig mer om hur du identifierar och varnar data vid nya data i en data uppsättning.

## <a name="next-steps"></a>Nästa steg 

+ Skapa en data uppsättning i Azure Machine Learning Studio eller med python SDK [med hjälp av dessa steg.](how-to-create-register-datasets.md)
+ Prova exempel på data uppsättnings utbildning med våra [exempel antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
