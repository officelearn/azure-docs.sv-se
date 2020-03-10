---
title: Data i Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig hur Azure Machine Learning interagerar med dina data och hur de används i dina Machine Learning-experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: 4149e90e07bbcd03a0df41060b42b8902b89e774
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396180"
---
# <a name="data-access-in-azure-machine-learning"></a>Data åtkomst i Azure Machine Learning

I den här artikeln får du lära dig mer om Azure Machine Learning lösningar för data hantering och integrering för dina Machine Learning-uppgifter. Den här artikeln förutsätter att du redan har skapat ett [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) och [Azure Storage-tjänsten](https://docs.microsoft.com/azure/storage/common/storage-introduction).

När du är redo att använda data i din lagring rekommenderar vi att du

1. Skapa ett Azure Machine Learning data lager.
2. Skapa en Azure Machine Learning data uppsättning från det data lagret. 
3. Använd den data uppsättningen i dator inlärnings experimentet genom att antingen 
    1. Montera den på ditt Experiments beräknings mål för modell träning

        **ELLER** 

    1. Använda det direkt i Azure Machine Learning lösningar som automatiserad dator inlärning (automatiserad ML) experiment körningar, maskin inlärnings pipeliner och [Azure Machine Learning designer](concept-designer.md).
4. Skapa data uppsättnings Övervakare för data uppsättningen för modell utdata för att identifiera data drift. 
5. Om data påträffas kan du uppdatera din indata-datauppsättning och träna modellen på lämpligt sätt.

Följande diagram innehåller en visuell demonstration av det rekommenderade arbets flödet för data åtkomst.

![Data-Concept-diagram](./media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>Åtkomst till data i lagring

För att få åtkomst till dina data i ditt lagrings konto erbjuder Azure Machine Learning data lager och data uppsättningar. Data lager besvarar frågan: Hur ansluter jag säkert till mina data i mina Azure Storage? Data lager ger ett lager av abstraktion över lagrings tjänsten. Detta hjälper till att skydda och förenkla åtkomst till din lagring, eftersom anslutnings informationen lagras i data lagret och inte visas i skript. 

Data uppsättningar besvarar frågan: Hur får jag vissa datafiler i mitt Data Center? Data uppsättningar pekar på den eller de filer i din underliggande lagring som du vill använda för ditt dator inlärnings experiment. Tillsammans erbjuder data lager och data uppsättningar ett säkert, skalbart och reproducerat data leverans arbets flöde för dina Machine Learning-uppgifter.

### <a name="datastores"></a>Data lager

Ett Azure Machine Learning data lager är en lagrings abstraktion över dina Azure Storage-tjänster. [Registrera och skapa ett data lager](how-to-access-data.md) för att enkelt ansluta till ditt Azure Storage-konto och få åtkomst till data i dina underliggande Azure Storage-tjänster.

Azure Storage-tjänster som stöds och som kan registreras som data lager:
+ Azure Blob-behållare
+ Azure-filresurs
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks-filsystem
+ Azure Database for MySQL

### <a name="datasets"></a>Datauppsättningar

[Skapa en Azure Machine Learning data uppsättning](how-to-create-register-datasets.md) för att interagera med data i dina data lager och paketera dina data i ett förbruknings Bart objekt för Machine Learning-uppgifter. Registrera data uppsättningen på din arbets yta för att dela och återanvända den över olika experiment utan komplexa data inmatningar.

Data uppsättningar kan skapas från lokala filer, offentliga URL: er, [öppna data uppsättningar i Azure](#open)eller vissa filer i dina data lager. Om du vill skapa en data uppsättning från en i minnet Pandas dataframe skriver du data till en lokal fil, t. ex. en CSV-fil, och skapar din data uppsättning från den filen. Data uppsättningar är inte kopior av dina data, men är referenser som pekar på data i lagrings tjänsten, så ingen extra lagrings kostnad uppkommer. 

Följande diagram visar att om du inte har en Azure Storage-tjänst kan du skapa en data uppsättning direkt från lokala filer, offentliga URL: er eller en Azure Open-datauppsättning. Detta ansluter din data uppsättning till det standard data lager som skapades automatiskt med experimentets [Azure Machine Learning-arbetsyta](concept-workspace.md).

![Data-Concept-diagram](./media/concept-data/dataset-workflow.svg)

Ytterligare data uppsättnings funktioner finns i följande dokumentation:

+ [Version och spårning](how-to-version-track-datasets.md) av data uppsättning härkomst.
+ [Övervaka din data uppsättning](how-to-monitor-datasets.md) för att hjälpa till med data avkänning.
+  Se följande för dokumentation om de två typerna av data uppsättningar:
    + [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representerar data i tabell format genom att parsa den angivna filen eller listan med filer. Med detta kan du materialisera data till en Pandas eller Spark-DataFrame för vidare manipulering och rengöring. En fullständig lista över filer som du kan skapa TabularDatasets från finns i [TabularDatasetFactory-klassen](https://aka.ms/tabulardataset-api-reference).

    + [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) refererar till en eller flera filer i dina data lager eller offentliga URL: er. Med den här metoden kan du hämta eller montera filer som du väljer till beräknings målet som ett FileDataset-objekt.

## <a name="work-with-your-data"></a>Arbeta med dina data

Med data uppsättningar kan du utföra ett antal Machine Learning-uppgifter genom sömlös integrering med Azure Machine Learning funktioner. 

+ [Träna maskin inlärnings modeller](how-to-train-with-datasets.md).
+ Använda data uppsättningar i 
     + [automatiserade ML-experiment](how-to-create-portal-experiments.md)
     + [designern](tutorial-designer-automobile-price-train-score.md#import-data) 
+ Få åtkomst till data uppsättningar för poängsättning med batch-härledning i [Machine Learning-pipeliner](how-to-create-your-first-pipeline.md).
+ Skapa ett [projekt med data etiketter](#label).
+ Konfigurera en data uppsättnings Övervakare för [data](#drift) avkänning.

<a name="open"></a>

## <a name="azure-open-datasets"></a>Azure Open Datasets

[Azure Open-datauppsättningar](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) är granskade offentliga data uppsättningar som du kan använda för att lägga till scenario-/regionsspecifika funktioner till maskin inlärnings lösningar för mer exakta modeller. Öppna data uppsättningar finns i molnet på Microsoft Azure och är integrerade i Azure Machine Learning. Du kan också komma åt data uppsättningarna via API: er och använda dem i andra produkter, till exempel Power BI och Azure Data Factory.

I Azure Open-datauppsättningarna ingår data från offentliga domäner för väder, räkning, helger, offentlig säkerhet och plats som hjälper dig att träna maskin inlärnings modeller och utöka förutsägelse lösningar. Du kan också dela dina offentliga data uppsättningar i Azure Open-datauppsättningar.

<a name="label"></a>

## <a name="data-labeling"></a>Data etiketter

Att märka stora mängder data har ofta varit ett problem i Machine Learning-projekt. De med en komponent för visuellt innehåll, till exempel bild klassificering eller objekt identifiering, kräver vanligt vis tusentals avbildningar och motsvarande etiketter.

Azure Machine Learning ger dig en central plats för att skapa, hantera och övervaka projekt med etiketter. Genom att namnge projekt kan du koordinera data, etiketter och grupp medlemmar, så att du effektivt kan hantera etikett uppgifter. Aktiviteter som stöds för närvarande är bild klassificering, antingen flera etiketter eller flera klasser och objekt identifiering med hjälp av gränser rutor.

+ Skapa ett [data etiketts projekt](how-to-create-labeling-projects.md)och mata ut en data uppsättning för användning i Machine Learning-experiment.

<a name="drift"></a>

## <a name="data-drift"></a>Data avvikelse

I samband med Machine Learning är data driften ändringen i modell indata som leder till modell prestanda försämring. Det är en av de främsta orsakerna till modell precisionen över tid, vilket innebär att övervakningen av data driften hjälper till att identifiera problem med modell prestanda.
Se artikeln [skapa en data uppsättnings övervakare](how-to-monitor-datasets.md) för att lära dig mer om hur du identifierar och varnar data vid nya data i en data uppsättning.

## <a name="next-steps"></a>Nästa steg 

+ Skapa en data uppsättning i Azure Machine Learning Studio eller med python SDK och [Använd de här stegen.](how-to-create-register-datasets.md)
+ Prova exempel på data uppsättnings utbildning med våra [exempel antecknings böcker](https://aka.ms/dataset-tutorial).
+ Data ingångs exempel finns i [självstudien om data](https://aka.ms/datadrift-notebook)drift.
