---
title: Vad är en Azure Machine Learning beräknings instans?
titleSuffix: Azure Machine Learning
description: Lär dig mer om Azure Machine Learning Compute-instansen, en fullständigt hanterad molnbaserad arbets Station.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355029"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Vad är en Azure Machine Learning beräknings instans?

En Azure Machine Learning beräknings instans (för hands version) är en fullständigt hanterad molnbaserad arbets station för data forskare. 

Med beräknings instanser är det enkelt att komma igång med Azure Machine Learning utveckling samt tillhandahålla funktioner för hantering och företags beredskap för IT-administratörer.  

Använd en beräknings instans som din fullständigt konfigurerade och hanterade utvecklings miljö i molnet.

Beräknings instanser används vanligt vis som utvecklings miljöer.  De kan också användas som beräknings mål för utbildning och inferencing för utveckling och testning.  För stora aktiviteter är ett [Azure Machine Learning beräknings kluster](how-to-set-up-training-targets.md#amlcompute) med skalnings funktioner för flera noder ett bättre beräknings måls val.


## <a name="why-use-a-compute-instance"></a>Varför ska man använda en beräknings instans?

En beräknings instans är en fullständigt hanterad molnbaserad arbets station som är optimerad för din Machine Learning Development-miljö. Det ger följande fördelar:

|Viktiga fördelar||
|----|----|
|Produktivitet|Data forskare kan bygga och distribuera modeller med integrerade antecknings böcker och följande verktyg i webbläsaren:<br/>– Jupyter<br/>- JupyterLab<br/>– RStudio|
|Hanterad & säker|Minska din säkerhets storlek och Lägg till efterlevnad med företagets säkerhets krav. Beräknings instanser ger robusta hanterings principer och säkra nätverkskonfigurationer som:<br/><br/>– Automatisk etablering från Resource Manager-mallar eller Azure Machine Learning SDK<br/>- [rollbaserad åtkomst kontroll (RBAC)](/azure/role-based-access-control/overview)<br/>stöd för - [virtuellt nätverk](how-to-enable-virtual-network.md#compute-instance)<br/>– SSH-princip för att aktivera/inaktivera SSH-åtkomst|
|Förkonfigurerad&nbsp;eller&nbsp;ML|Spara tid på installations aktiviteter med förkonfigurerade och uppdaterade ML-paket, ramverk för djup inlärning, GPU-drivrutiner.|
|Helt anpassningsbar|Brett stöd för virtuella Azure-datorer, inklusive GPU: er och beständiga anpassningar på låg nivå, till exempel installation av paket och driv rutiner gör avancerade scenarier till en enkelt. |

## <a name="contents"></a>Verktyg och miljöer

Med Azure Machine Learning Compute-instansen kan du skapa, träna och distribuera modeller i en helt integrerad Notebook-miljö i din arbets yta.


De här verktygen och miljöerna är installerade på beräknings instansen: 

|Allmänna verktyg & miljöer|Detaljer|
|----|:----:|
|Drivrutiner|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI-bibliotek||
|Azure CLI ||
|Azure Machine Learning exempel ||
|Azure Machine Learning EDAT-motor ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|**R** -verktyg & miljöer|Detaljer|
|----|:----:|
|RStudio server med öppen källkod||
|R-kernel||
|Azure Machine Learning SDK för R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK-exempel|

|**Python** -verktyg & miljöer|Detaljer|
|----|----|
|Anaconda python||
|Jupyter och tillägg||
|Jupyterlab och tillägg||
|Visual Studio Code ||
[Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>från PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Andra PyPI-paket|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-paket|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Djup inlärnings paket|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-paket|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning python & R SDK-exempel||

Python-paketen installeras i **python 3,6-azureml-** miljön.  

Beräknings instanser används vanligt vis som utvecklings miljöer.  De kan också användas som beräknings mål för utbildning och inferencing för utveckling och testning.  För stora aktiviteter är ett [Azure Machine Learning beräknings kluster](how-to-set-up-training-targets.md#amlcompute) med skalnings funktioner för flera noder ett bättre beräknings måls val.

### <a name="installing-packages"></a>Installera paket

Du kan installera paket direkt i en Jupyter Notebook eller RStudio:

* RStudio Använd fliken **paket** längst ned till höger eller fliken **konsol** längst upp till vänster.  
* Python: Lägg till installations kod och kör i en Jupyter Notebook-cell.

Eller så kan du komma åt ett terminalfönster på något av följande sätt:

* RStudio: Välj fliken **Terminal** längst upp till vänster.
* Jupyter Lab: Välj panelen **Terminal** under den **andra** rubriken på fliken Start.
* Jupyter: Välj **ny > Terminal** överst till höger på fliken filer.
* SSH till datorn.  Installera sedan python-paket i **Python 3,6-azureml-** miljön.  Installera R-paket i **R** -miljön.

## <a name="accessing-files"></a>Komma åt filer

Antecknings böcker och R-skript lagras på arbets ytans standard lagrings konto i Azure-filresursen.  Dessa filer finns under katalogen "användarfiler". Den här lagringen gör det enkelt att dela antecknings böcker mellan beräknings instanser. Lagrings kontot sparar också dina antecknings böcker på ett säkert sätt när du stoppar eller tar bort en beräknings instans.

Azure-filresursen på din arbets yta monteras som en enhet på beräknings instansen. Den här enheten är standard arbets katalogen för Jupyter, Jupyter Labs och RStudio.

Filerna i fil resursen är tillgängliga från alla beräknings instanser i samma arbets yta. Eventuella ändringar av de här filerna på beräknings instansen kommer att bli tillförlitligt bestående tillbaka till fil resursen.

Du kan också klona de senaste Azure Machine Learning exemplen till din mapp under katalogen användarfiler i fil resursen för arbets ytan.

Att skriva små filer kan vara långsammare på nätverks enheter än att skriva till den virtuella datorn.  Om du skriver många små filer kan du försöka använda en katalog direkt på beräknings instansen, till exempel en `/tmp` katalog. Observera att de här filerna inte är tillgängliga från andra beräknings instanser i arbets ytan.

## <a name="managing-a-compute-instance"></a>Hantera en beräknings instans

I arbets ytan i Azure Machine Learning Studio väljer du **Compute**och sedan **beräknings instans** överst.

![Hantera en beräknings instans](./media/concept-compute-instance/manage-compute-instance.png)

Du kan utföra följande åtgärder:

* Skapa en beräknings instans. Ange namn, typ av virtuell Azure-dator inklusive GPU: er (antecknings typ för virtuell dator kan inte ändras efter att du har skapat), aktivera/inaktivera SSH-åtkomst och konfigurera inställningar för virtuellt nätverk. Du kan också skapa en instans direkt från integrerade antecknings böcker, Azure Portal, Resource Manager-mall eller Azure Machine Learning SDK. De dedikerade kärnorna per regions kvot som används för att skapa beräknings instanser är enhetliga och delas med Azure Machine Learning beräknings kluster kvot.
* Uppdatera fliken beräknings instanser
* Starta, stoppa och starta om en beräknings instans
* Ta bort en beräknings instans

För varje beräknings instans i din arbets yta kan du:

* Åtkomst Jupyter, JupyterLab, RStudio på beräknings instansen
* SSH till beräknings instans. SSH-åtkomst är inaktive rad som standard men kan aktive ras vid skapande av beräknings instanser. SSH-åtkomst sker via en funktion för offentlig/privat nyckel. På fliken får du information om SSH-anslutning, till exempel IP-adress, användar namn och port nummer.
* Hämta information om en angiven beräknings instans, till exempel IP-adress och region.

Med [RBAC](/azure/role-based-access-control/overview) kan du styra vilka användare i arbets ytan som kan skapa, ta bort, starta, stoppa och starta om en beräknings instans. Alla användare i arbets ytans deltagare och ägar roll kan skapa, ta bort, starta, stoppa och starta om beräknings instanser i arbets ytan. Det är dock bara skaparen av en angiven beräknings instans som tillåts komma åt Jupyter, JupyterLab och RStudio på den beräknings instansen. Instanserna av beräknings instansen har en beräknings instans som är dedikerad till dem, har rot åtkomst och kan terminalen i Jupyter. Compute-instansen kommer att ha en användar inloggning av skapare-användare och alla åtgärder använder den användarens identitet för RBAC och behörighet för experiment körningar. SSH-åtkomsten styrs via mekanismen för offentlig/privat nyckel.

Du kan också skapa en instans
* Direkt från den integrerade Notebook-upplevelsen
* I Azure Portal
* Från Azure Resource Manager mall
* Med Azure Machine Learning SDK

De dedikerade kärnorna per region kvot, som gäller för skapande av beräknings instanser, är enhetliga och delade med Azure Machine Learning utbildnings kluster kvot. 

## <a name="compute-target"></a>Compute Target

Du kan använda beräknings instanser som ett [utbildnings mål](concept-compute-target.md#train) som liknar Azure Machine Learning Compute Training-kluster. Etablera en virtuell dator med flera GPU: n för att köra distribuerade utbildnings jobb med TensorFlow/PyTorch-uppskattningar. Du kan också skapa en körnings konfiguration och använda den för att köra experimentet på beräknings instansen. Du kan använda Compute instance som ett lokalt inferencing distributions mål för testning/fel söknings scenarier.

## <a name="notebookvm"></a>Vad hände med den virtuella Notebook-datorn?

Beräknings instanser ersätter den virtuella Notebook-datorn.  

Alla notebook-filer som lagras i fil resursen och data i arbets ytans data lager kommer att vara tillgängliga från en beräknings instans. Alla anpassade paket som tidigare har installerats på en VM-baserad virtuell dator måste dock installeras på nytt på beräknings instansen. Kvot begränsningar som gäller för att skapa beräknings kluster gäller även för skapande av beräknings instanser. 

Det går inte att skapa nya virtuella dator datorer. Men du kan fortfarande komma åt och använda de virtuella datorerna som du har skapat, med fullständig funktionalitet. Du kan skapa beräknings instanser i samma arbets yta som de befintliga virtuella datorerna. 


## <a name="next-steps"></a>Nästa steg

 * [Självstudie: träna din första ml-modell](tutorial-1st-experiment-sdk-train.md) visar hur du använder en beräknings instans med en integrerad antecknings bok.
