---
title: Migrera till Azure Machine Learning-tjänsten
description: Lär dig mer om att uppgradera eller migrera till den sent versionen av Azure Machine Learning-tjänsten från en tidigare version.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: haining
author: haining
ms.date: 09/24/2018
ms.openlocfilehash: e2b3545c020f41f25f19843eab158cfb1b419164
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253456"
---
# <a name="migrate-to-the-latest-version-of-azure-machine-learning-service"></a>Migrera till den senaste versionen av Azure Machine Learning-tjänsten 

**Om du har installerat Workbench (förhandsgranskning) och/eller har experimentering och modellhantering Förhandsgranska konton, använda den här artikeln för att migrera till den senaste versionen.**  Om du inte har förhandsversion Workbench installerad, eller en experimentering och/eller modellhanteringskonto, behöver du inte migrera något.

## <a name="what-can-i-migrate"></a>Vad kan jag migrera?
De flesta artefakter som skapats i den första förhandsversionen av Azure Machine Learning-tjänsten lagras i din egen lokala eller molnlagring. Dessa artefakter försvinner inte. Om du vill migrera, registrera artefakterna igen med den uppdaterade Azure Machine Learning-tjänsten. 

Följande tabell och artikeln förklarar vad du kan göra med dina befintliga tillgångar och resurser före eller efter att flytta till den senaste versionen av Azure Machine Learning-tjänsten. Du kan också fortsätta att använda den tidigare versionen och dina tillgångar under en viss tid ([finns i övergången supporttider](overview-what-happened-to-workbench.md#timeline)).

|Tillgång eller resurs från den gamla versionen|Kan jag migrera?|Åtgärder|
|-----------------|:-------------:|-------------|
|Machine learning-modeller (som lokala filer)|Ja|Ingen. Fungerar som tidigare.|
|Modeller beroenden & scheman (som lokala filer)|Ja|Ingen. Fungerar som tidigare.|
|Projekt|Ja|[Koppla den lokala mappen till en ny arbetsyta](#projects).|
|Körningshistorik|Nej|[Nedladdningsbara](#history) ett tag.|
|Förberedelseverktyg datafiler|Nej|[Förbered eventuella storlek datauppsättning](#dataprep) för modellering med den nya Azure Machine Learning Data Prep-SDK eller använder Azure Databricks.|
|Beräkningsmål|Nej|Registrera dem i ny arbetsyta.|
|Registrerade modeller|Nej|Omregistrera modellen under en ny arbetsyta.|
|Registrerade manifest|Nej|Ingen. Det finns inte längre manifest som ett koncept i den nya arbetsytan.|
|Registrerade avbildningar|Nej|Skapa Docker-avbildning för distribution under en ny arbetsyta.|
|Distribuerade webbtjänster|Nej|Ingen. De kommer att fungera som – är <br/>eller [distribuera dem igen med senaste versionen](#services).|
|Experimentering och <br/>Konton för modellhantering|Nej|[Skapa en arbetsyta](#resources) i stället.|
|Machine learning-KLI & SDK|Nej|Använd den nya [CLI](reference-azure-machine-learning-cli.md) och [SDK](https://aka.ms/aml-sdk) för nytt arbete.|


Läs mer om [vad som ändrats i den här versionen](overview-what-happened-to-workbench.md)?

>[!Warning]
>Den här artikeln är inte för Azure Machine Learning Studio-användare. Det är för Azure Machine Learning-tjänstkunder som har installerat Workbench (förhandsgranskning) och/eller har experimentering och konton för modellhantering förhandsversion.

<a name="resources"></a>

## <a name="azure-resources"></a>Azure-resurser

Resurser, till exempel din experimenteringskonton och konton för modellhantering maskininlärning compute-miljöer inte kan migreras över till den senaste versionen av Azure Machine Learning-tjänsten. Se den [tidslinje](overview-what-happened-to-workbench.md#timeline) på hur länge dina tillgångar fortsätter att fungera.

Kom igång med den senaste versionen genom att skapa en arbetsyta med Azure Machine Learning-tjänsten i den [Azure-portalen](quickstart-get-started.md). Portalens instrumentpanel för arbetsytor stöds endast i webbläsarna Edge, Chrome och Firefox.

Den här nya arbetsytan är den huvudtjänst resursen och gör att du kan använda alla de senaste funktionerna i Azure Machine Learning-tjänsten. Mer information om detta [arbetsyta och arkitektur](concept-azure-machine-learning-architecture.md).

<a name="projects"></a>

## <a name="projects"></a>Projekt

Projekt är nu kataloger på den lokala datorn i den senaste versionen i stället för att dina projekt i en arbetsyta i molnet. Visas ett diagram över den [senaste arkitekturen](concept-azure-machine-learning-architecture.md). 

Om du vill använda den lokala katalogen som innehåller filer och skript, ange katalogens namn i den ['experiment.submit'](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python-kommandot eller med kommandot az ml projekt bifoga CLI.

Exempel:
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

<a name="services"></a>

## <a name="deployed-web-services"></a>Distribuerade webbtjänster

Distribuera om dina modeller med nya SDK eller CLI att nya av distributionsmålen för att migrera webbtjänster. Det finns ingen anledning att ändra din ursprungliga bedömningsfilen, modellfiler filen beroenden, miljöfil och schemafiler. 

I den senaste versionen distribueras modeller som webbtjänster kan [Azure Container Instances](how-to-deploy-to-aci.md) (ACI) eller [Azure Kubernetes Service](how-to-deploy-to-aks.md) kluster (AKS). 

Mer information finns i följande artiklar:
+ [Distribuera till ACI](how-to-deploy-to-aci.md)
+ [Distribuera till AKS](how-to-deploy-to-aks.md)
+ [Självstudie: Distribuera modeller med Azure Machine Learning-tjänsten](tutorial-deploy-models-with-aml.md)

När [stöd för det tidigare CLI upphört att gälla](overview-what-happened-to-workbench.md#timeline), du kan inte hantera webbtjänster som du ursprungligen har distribuerat med ditt modellhanteringskonto. Dessa webbtjänster kommer dock fortsätta att fungera för så länge det finns fortfarande stöd för Azure Container Service (ACS).

<a name="history"></a>

## <a name="run-history-records"></a>Kör historikposter

Medan du inte kan fortsätta att lägga till i din befintliga körhistorik under den gamla arbetsytan, kan du exportera händelser som du har med hjälp av föregående CLI. När [stöd för det tidigare CLI upphört att gälla](overview-what-happened-to-workbench.md#timeline), du kan inte exportera dessa körhistorik längre.

Starta utbildning dina modeller och spåra körhistorik som använder den nya CLI och SDK. Du kan lära dig hur med den [självstudie: skapa modeller med Azure Machine Learning-tjänsten](tutorial-train-models-with-aml.md).

Så här exporterar körningshistoriken med tidigare CLI:

```azurecli
#list all runs
az ml history list

#get details about a particular run
az ml history info

# download all artifacts of a run
az ml history download
```

<a name="dataprep"></a>

## <a name="data-preparation-files"></a>Förberedelse av datafiler
Filer för förberedelse av data är inte portabla utan Workbench. Men du kan fortfarande förbereda datauppsättning valfri storlek för modellering med nya Azure Machine Learning Data Prep SDK eller använder Azure Databricks för stora datauppsättningar.  [Lär dig hur du hämtar data prep SDK](how-to-data-prep.md). 

## <a name="next-steps"></a>Nästa steg

En Snabbstart som visar hur du skapar en arbetsyta, skapa ett projekt, köra ett skript och utforska körningshistoriken för skriptet med den senaste versionen av Azure Machine Learning-tjänsten, försök [Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md).

En mer ingående beskrivning av det här arbetsflödet, följa kompletta självstudien som innehåller detaljerade anvisningar för utbildning och distribuera modeller med Azure Machine Learning-tjänsten. 

> [!div class="nextstepaction"]
> [Självstudie: Skapa och distribuera modeller](tutorial-train-models-with-aml.md)
