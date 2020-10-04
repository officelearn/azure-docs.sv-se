---
title: Vad är en Azure Machine Learning-beräkningsinstans?
titleSuffix: Azure Machine Learning
description: Lär dig mer om Azure Machine Learning Compute-instansen, en fullständigt hanterad molnbaserad arbets Station.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/02/2020
ms.openlocfilehash: 68143d3ee5df6dca29c43cb090f5873c4b50060f
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704698"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Vad är en Azure Machine Learning-beräkningsinstans?

En Azure Machine Learning beräknings instans är en hanterad molnbaserad arbets station för data forskare.

Med beräknings instanser är det enkelt att komma igång med Azure Machine Learning utveckling samt tillhandahålla funktioner för hantering och företags beredskap för IT-administratörer.  

Använd en beräknings instans som din fullständigt konfigurerade och hanterade utvecklings miljö i molnet för Machine Learning. De kan också användas som beräknings mål för utbildning och inferencing för utvecklings-och testnings ändamål.  

För modell utbildning i produktions klass använder du ett [Azure Machine Learning beräknings kluster](how-to-create-attach-compute-cluster.md) med skalnings funktioner för flera noder. För modell distribution av produktions klass använder du [Azure Kubernetes service-kluster](how-to-deploy-azure-kubernetes-service.md).

## <a name="why-use-a-compute-instance"></a>Varför ska man använda en beräknings instans?

En beräknings instans är en fullständigt hanterad molnbaserad arbets station som är optimerad för din Machine Learning Development-miljö. Det ger följande fördelar:

|Viktiga fördelar|Description|
|----|----|
|Produktivitet|Du kan bygga och distribuera modeller med integrerade antecknings böcker och följande verktyg i Azure Machine Learning Studio:<br/>– Jupyter<br/>- JupyterLab<br/>-RStudio (för hands version)<br/>Compute-instansen är helt integrerad med Azure Machine Learning-arbetsyta och Studio. Du kan dela antecknings böcker och data med andra data forskare på arbets ytan. Du kan också ställa in VS Code-fjärrutveckling med [SSH](how-to-set-up-vs-code-remote.md) |
|Hanterad & säker|Minska din säkerhets storlek och Lägg till efterlevnad med företagets säkerhets krav. Beräknings instanser ger robusta hanterings principer och säkra nätverkskonfigurationer som:<br/><br/>– Autoetablering från Resource Manager-mallar eller Azure Machine Learning SDK<br/>- [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](/azure/role-based-access-control/overview)<br/>- [Stöd för virtuella nätverk](how-to-enable-virtual-network.md#compute-instance)<br/>– SSH-princip för att aktivera/inaktivera SSH-åtkomst<br/>TLS 1,2 aktiverat |
|Förkonfigurerat &nbsp; för &nbsp; ml|Spara tid på installations aktiviteter med förkonfigurerade och uppdaterade ML-paket, ramverk för djup inlärning, GPU-drivrutiner.|
|Helt anpassningsbar|Brett stöd för virtuella Azure-datorer, inklusive GPU: er och beständiga anpassningar på låg nivå, till exempel installation av paket och driv rutiner gör avancerade scenarier till en enkelt. |

Du kan själv [skapa en beräknings instans](how-to-create-manage-compute-instance.md?tabs=python#create) eller en administratör för att [skapa en beräknings instans](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview).

## <a name="tools-and-environments"></a><a name="contents"></a>Verktyg och miljöer

> [!IMPORTANT]
> Objekt som marker ATS (för hands version) i den här artikeln finns för närvarande i offentlig för hands version.
> För hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azure Machine Learning Compute-instansen kan du skapa, träna och distribuera modeller i en helt integrerad Notebook-miljö i din arbets yta.

Du kan [installera paket](how-to-create-manage-compute-instance.md#install-packages) och [lägga till kernels](how-to-create-manage-compute-instance.md#add-new-kernels) i din beräknings instans.  

De här verktygen och miljöerna är redan installerade på beräknings instansen: 

|Allmänna verktyg & miljöer|Information|
|----|:----:|
|Drivrutiner|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI-bibliotek||
|Azure CLI ||
|Azure Machine Learning exempel ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|**R** -verktyg & miljöer|Information|
|----|:----:|
|RStudio-server med öppen källkod (för hands version)||
|R-kernel||
|Azure Machine Learning SDK för R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK-exempel|

|**Python** -verktyg & miljöer|Information|
|----|----|
|Anaconda Python||
|Jupyter och tillägg||
|Jupyterlab och tillägg||
[Azure Machine Learning-SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)</br>från PyPI|Innehåller de flesta av de azureml extra paketen.  Om du vill se hela listan [öppnar du ett terminalfönster på beräknings instansen](how-to-run-jupyter-notebooks.md#terminal) och kör <br/> `conda list -n azureml_py36 azureml*` |
|Andra PyPI-paket|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-paket|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Djup inlärnings paket|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-paket|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning python & R SDK-exempel||

Python-paketen installeras i **python 3,6-azureml-** miljön.  

## <a name="accessing-files"></a>Komma åt filer

Antecknings böcker och R-skript lagras på arbets ytans standard lagrings konto i Azure-filresursen.  Dessa filer finns under katalogen "användarfiler". Den här lagringen gör det enkelt att dela antecknings böcker mellan beräknings instanser. Lagrings kontot sparar också dina antecknings böcker på ett säkert sätt när du stoppar eller tar bort en beräknings instans.

Azure-filresursen på din arbets yta monteras som en enhet på beräknings instansen. Den här enheten är standard arbets katalogen för Jupyter, Jupyter Labs och RStudio. Det innebär att de antecknings böcker och andra filer som du skapar i Jupyter, JupyterLab eller RStudio automatiskt lagras på fil resursen och är tillgängliga för användning i andra beräknings instanser.

Filerna i fil resursen är tillgängliga från alla beräknings instanser i samma arbets yta. Eventuella ändringar av de här filerna på beräknings instansen kommer att bli tillförlitligt bestående tillbaka till fil resursen.

Du kan också klona de senaste Azure Machine Learning exemplen till din mapp under katalogen användarfiler i fil resursen för arbets ytan.

Att skriva små filer kan vara långsammare på nätverks enheter än att skriva till själva data bearbetnings instansen.  Om du skriver många små filer kan du försöka använda en katalog direkt på beräknings instansen, till exempel en `/tmp` katalog. Observera att de här filerna inte är tillgängliga från andra beräknings instanser. 

Du kan använda `/tmp` katalogen på beräknings instansen för dina temporära data.  Skriv dock inte stora filer av data på beräknings instansens OS-disk.  Använd [data lager](concept-azure-machine-learning-architecture.md#datasets-and-datastores) i stället. Om du har installerat JupyterLab git-tillägg kan det också leda till långsam prestanda för beräknings instanser.

## <a name="compute-target"></a>Beräkningsmål

Du kan använda beräknings instanser som ett [utbildnings mål](concept-compute-target.md#train) som liknar Azure Machine Learning Compute Training-kluster. 

En beräknings instans:
* Har en jobbkö.
* Kör jobb på ett säkert sätt i en virtuell nätverks miljö, utan att företag behöver öppna SSH-porten. Jobbet körs i en behållare miljö och paketerar dina modell beroenden i en Docker-behållare.
* Kan köra flera små jobb parallellt (för hands version).  Två jobb per kärna kan köras parallellt medan resten av jobben placeras i kö.
* Stöd för distribuerade utbildnings jobb med en nod med flera noder

Du kan använda Compute instance som ett lokalt inferencing distributions mål för test-/fel söknings scenarier.


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Vad hände med den virtuella Notebook-datorn?

Beräknings instanser ersätter den virtuella Notebook-datorn.  

Alla notebook-filer som lagras i fil resursen och data i arbets ytans data lager kommer att vara tillgängliga från en beräknings instans. Alla anpassade paket som tidigare har installerats på en virtuell dator måste dock installeras om på beräknings instansen. Kvot begränsningar som gäller för att skapa beräknings kluster gäller även för skapande av beräknings instanser.

Det går inte att skapa nya virtuella dator datorer. Men du kan fortfarande komma åt och använda de virtuella datorerna som du har skapat, med fullständig funktionalitet. Du kan skapa beräknings instanser i samma arbets yta som de befintliga virtuella datorerna.


## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera en beräknings instans](how-to-create-manage-compute-instance.md)
* [Självstudie: träna din första ml-modell](tutorial-1st-experiment-sdk-train.md) visar hur du använder en beräknings instans med en integrerad antecknings bok.
