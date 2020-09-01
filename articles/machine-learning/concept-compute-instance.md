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
ms.date: 07/27/2020
ms.openlocfilehash: 5c5326310887cd5756ae264a35aafe7e771a7863
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226802"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Vad är en Azure Machine Learning-beräkningsinstans?

En Azure Machine Learning beräknings instans är en hanterad molnbaserad arbets station för data forskare.

Med beräknings instanser är det enkelt att komma igång med Azure Machine Learning utveckling samt tillhandahålla funktioner för hantering och företags beredskap för IT-administratörer.  

Använd en beräknings instans som din fullständigt konfigurerade och hanterade utvecklings miljö i molnet för Machine Learning. De kan också användas som beräknings mål för utbildning och inferencing för utvecklings-och testnings ändamål.  

För modell träning för produktions klasser använder du ett [Azure Machine Learning beräknings kluster](how-to-create-attach-compute-sdk.md#amlcompute) med skalnings funktioner för flera noder. För modell distribution av produktions klass använder du [Azure Kubernetes service-kluster](how-to-deploy-azure-kubernetes-service.md).

## <a name="why-use-a-compute-instance"></a>Varför ska man använda en beräknings instans?

En beräknings instans är en fullständigt hanterad molnbaserad arbets station som är optimerad för din Machine Learning Development-miljö. Det ger följande fördelar:

|Viktiga fördelar|Beskrivning|
|----|----|
|Produktivitet|Du kan bygga och distribuera modeller med integrerade antecknings böcker och följande verktyg i Azure Machine Learning Studio:<br/>– Jupyter<br/>- JupyterLab<br/>-RStudio (för hands version)<br/>Compute-instansen är helt integrerad med Azure Machine Learning-arbetsyta och Studio. Du kan dela antecknings böcker och data med andra data forskare på arbets ytan. Du kan även konfigurera kontra fjärrutveckling av kod med [SSH](how-to-set-up-vs-code-remote.md) |
|Hanterad & säker|Minska din säkerhets storlek och Lägg till efterlevnad med företagets säkerhets krav. Beräknings instanser ger robusta hanterings principer och säkra nätverkskonfigurationer som:<br/><br/>– Automatisk etablering från Resource Manager-mallar eller Azure Machine Learning SDK<br/>- [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](/azure/role-based-access-control/overview)<br/>- [Stöd för virtuella nätverk](how-to-enable-virtual-network.md#compute-instance)<br/>– SSH-princip för att aktivera/inaktivera SSH-åtkomst<br/>TLS 1,2 aktiverat |
|Förkonfigurerat &nbsp; för &nbsp; ml|Spara tid på installations aktiviteter med förkonfigurerade och uppdaterade ML-paket, ramverk för djup inlärning, GPU-drivrutiner.|
|Helt anpassningsbar|Brett stöd för virtuella Azure-datorer, inklusive GPU: er och beständiga anpassningar på låg nivå, till exempel installation av paket och driv rutiner gör avancerade scenarier till en enkelt. |

## <a name="tools-and-environments"></a><a name="contents"></a>Verktyg och miljöer

> [!IMPORTANT]
> Verktyg som marker ATS (för hands version) nedan finns för närvarande i offentlig för hands version.
> För hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azure Machine Learning Compute-instansen kan du skapa, träna och distribuera modeller i en helt integrerad Notebook-miljö i din arbets yta.

De här verktygen och miljöerna är installerade på beräknings instansen: 

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
[Azure Machine Learning-SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>från PyPI|Innehåller de flesta av de azureml extra paketen.  Om du vill se hela listan [öppnar du ett terminalfönster på beräknings instansen](how-to-run-jupyter-notebooks.md#terminal) och kör <br/> `conda list -n azureml_py36 azureml*` |
|Andra PyPI-paket|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-paket|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Djup inlärnings paket|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-paket|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning python & R SDK-exempel||

Python-paketen installeras i **python 3,6-azureml-** miljön.  

### <a name="installing-packages"></a>Installera paket

Du kan installera paket direkt i Jupyter Notebook eller RStudio:

* RStudio Använd fliken **paket** längst ned till höger eller fliken **konsol** längst upp till vänster.  
* Python: Lägg till installations kod och kör i en Jupyter Notebook cell.

Eller så kan du komma åt ett terminalfönster på något av följande sätt:

* RStudio: Välj fliken **Terminal** längst upp till vänster.
* Jupyter Lab: Välj panelen **Terminal** under den **andra** rubriken på fliken Start.
* Jupyter: Välj **ny>Terminal** överst till höger på fliken filer.
* SSH till datorn.  Installera sedan python-paket i **Python 3,6-azureml-** miljön.  Installera R-paket i **R** -miljön.

## <a name="accessing-files"></a>Komma åt filer

Antecknings böcker och R-skript lagras på arbets ytans standard lagrings konto i Azure-filresursen.  Dessa filer finns under katalogen "användarfiler". Den här lagringen gör det enkelt att dela antecknings böcker mellan beräknings instanser. Lagrings kontot sparar också dina antecknings böcker på ett säkert sätt när du stoppar eller tar bort en beräknings instans.

Azure-filresursen på din arbets yta monteras som en enhet på beräknings instansen. Den här enheten är standard arbets katalogen för Jupyter, Jupyter Labs och RStudio. Det innebär att de antecknings böcker och andra filer som du skapar i Jupyter, JupyterLab eller RStudio automatiskt lagras på fil resursen och är tillgängliga för användning i andra beräknings instanser.

Filerna i fil resursen är tillgängliga från alla beräknings instanser i samma arbets yta. Eventuella ändringar av de här filerna på beräknings instansen kommer att bli tillförlitligt bestående tillbaka till fil resursen.

Du kan också klona de senaste Azure Machine Learning exemplen till din mapp under katalogen användarfiler i fil resursen för arbets ytan.

Att skriva små filer kan vara långsammare på nätverks enheter än att skriva till själva data bearbetnings instansen.  Om du skriver många små filer kan du försöka använda en katalog direkt på beräknings instansen, till exempel en `/tmp` katalog. Observera att de här filerna inte är tillgängliga från andra beräknings instanser. 

Du kan använda `/tmp` katalogen på beräknings instansen för dina temporära data.  Skriv dock inte stora filer av data på beräknings instansens OS-disk.  Använd [data lager](concept-azure-machine-learning-architecture.md#datasets-and-datastores) i stället. Om du har installerat JupyterLab git-tillägg kan det också leda till lägre prestanda för beräknings instanser.

## <a name="managing-a-compute-instance"></a>Hantera en beräknings instans

I arbets ytan i Azure Machine Learning Studio väljer du **Compute**och sedan **beräknings instans** överst.

![Hantera en beräknings instans](./media/concept-compute-instance/manage-compute-instance.png)

Du kan utföra följande åtgärder:

* [Skapa en beräknings instans](#create). 
* Uppdatera fliken beräknings instanser.
* Starta, stoppa och starta om en beräknings instans.  Du betalar för instansen när den körs. Stoppa beräknings instansen när du inte använder den för att minska kostnaderna. Att stoppa en beräknings instans frigör den. Starta den sedan igen när du behöver den. 
* Ta bort en beräknings instans.
* Filtrera listan över beräknings instanser till de som du skapade.  Detta är de beräknings instanser som du kan komma åt.

För varje beräknings instans i din arbets yta som du har åtkomst till kan du:

* Åtkomst Jupyter, JupyterLab, RStudio på beräknings instansen
* SSH till beräknings instans. SSH-åtkomst är inaktive rad som standard men kan aktive ras vid skapande av beräknings instanser. SSH-åtkomst sker via en funktion för offentlig/privat nyckel. På fliken får du information om SSH-anslutning, till exempel IP-adress, användar namn och port nummer.
* Hämta information om en angiven beräknings instans, till exempel IP-adress och region.

Med [RBAC](/azure/role-based-access-control/overview) kan du styra vilka användare i arbets ytan som kan skapa, ta bort, starta, stoppa och starta om en beräknings instans. Alla användare i arbets ytans deltagare och ägar roll kan skapa, ta bort, starta, stoppa och starta om beräknings instanser i arbets ytan. Det är dock bara skaparen av en angiven beräknings instans som tillåts komma åt Jupyter, JupyterLab och RStudio på den beräknings instansen. Instanserna av beräknings instansen har en beräknings instans som är dedikerad till dem, har rot åtkomst och kan terminalen i genom Jupyter/JupyterLab/RStudio. Compute-instansen kommer att ha en användar inloggning av skapare-användare och alla åtgärder använder den användarens identitet för RBAC och behörighet för experiment körningar. SSH-åtkomsten styrs via mekanismen för offentlig/privat nyckel.

De här åtgärderna kan styras av RBAC:
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/läsning*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/skrivning*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/ta bort*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/start/åtgärd*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/stoppa/åtgärd*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/omstart/åtgärd*

### <a name="create-a-compute-instance"></a><a name="create"></a>Skapa en beräknings instans

I arbets ytan i Azure Machine Learning Studio [skapar du en ny beräknings instans](how-to-create-attach-compute-studio.md#compute-instance) från antingen **Compute** -avsnittet eller i avsnittet **antecknings böcker** när du är redo att köra en av dina antecknings böcker. 

Du kan också skapa en instans
* Direkt från den [integrerade Notebook-upplevelsen](tutorial-1st-experiment-sdk-setup.md#azure)
* I Azure Portal
* Från Azure Resource Manager mall. En exempel-mall finns i [mallen skapa en Azure Machine Learning beräknings instans](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* Med Azure Machine Learning SDK
* Från [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

De dedikerade kärnorna per region per VM-tullkvot och den totala regionala kvoten som gäller för skapande av beräknings instanser, är enhetliga och delade med Azure Machine Learning inlärnings kluster kvot. Att stoppa beräknings instansen frigör inte kvoten för att se till att du kommer att kunna starta om beräknings instansen.

## <a name="compute-target"></a>Beräkningsmål

Du kan använda beräknings instanser som ett [utbildnings mål](concept-compute-target.md#train) som liknar Azure Machine Learning beräknings kluster. 

En beräknings instans:
* Har en jobbkö.
* Kör jobb på ett säkert sätt i en virtuell nätverks miljö, utan att företag behöver öppna SSH-porten. Jobbet körs i en behållare miljö och paketerar dina modell beroenden i en Docker-behållare.
* Kan köra flera små jobb parallellt (för hands version).  Två jobb per kärna kan köras parallellt medan resten av jobben placeras i kö.
* Stöd för distribuerade utbildnings jobb med en nod med flera noder

Du kan använda Compute instance som ett lokalt inferencing distributions mål för test-/fel söknings scenarier.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Vad hände med den virtuella Notebook-datorn?

Beräknings instanser ersätter den virtuella Notebook-datorn.  

Alla notebook-filer som lagras i fil resursen och data i arbets ytans data lager kommer att vara tillgängliga från en beräknings instans. Alla anpassade paket som tidigare har installerats på en VM-baserad virtuell dator måste dock installeras på nytt på beräknings instansen. Kvot begränsningar som gäller för att skapa beräknings kluster gäller även för skapande av beräknings instanser.

Det går inte att skapa nya virtuella dator datorer. Men du kan fortfarande komma åt och använda de virtuella datorerna som du har skapat, med fullständig funktionalitet. Du kan skapa beräknings instanser i samma arbets yta som de befintliga virtuella datorerna.


## <a name="next-steps"></a>Nästa steg

 * [Självstudie: träna din första ml-modell](tutorial-1st-experiment-sdk-train.md) visar hur du använder en beräknings instans med en integrerad antecknings bok.
