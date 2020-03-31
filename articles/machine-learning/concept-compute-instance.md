---
title: Vad är en Azure Machine Learning-beräkningsinstans?
titleSuffix: Azure Machine Learning
description: Lär dig mer om Azure Machine Learning-beräkningsinstansen, en fullständigt hanterad molnbaserad arbetsstation.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283932"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Vad är en Azure Machine Learning-beräkningsinstans?

En Azure Machine Learning-beräkningsinstans (förhandsversion) är en fullständigt hanterad molnbaserad arbetsstation för dataforskare. 

Beräkningsinstanser gör det enkelt att komma igång med Azure Machine Learning-utveckling samt tillhandahålla funktioner för hantering och företagsberedskap för IT-administratörer.  

Använd en beräkningsinstans som din fullständigt konfigurerade och hanterade utvecklingsmiljö i molnet.

Beräkningsinstanser används vanligtvis som utvecklingsmiljöer.  De kan också användas som ett beräkningsmål för utbildning och inferencing för utveckling och testning.  För stora uppgifter är ett [Azure Machine Learning-beräkningskluster](how-to-set-up-training-targets.md#amlcompute) med flernodskalningsfunktioner ett bättre val av beräkningsmål.


## <a name="why-use-a-compute-instance"></a>Varför använda en beräkningsinstans?

En beräkningsinstans är en fullständigt hanterad molnbaserad arbetsstation som är optimerad för din maskininlärningsutvecklingsmiljö. Det ger följande fördelar:

|Viktiga fördelar||
|----|----|
|Produktivitet|Dataforskare kan skapa och distribuera modeller med hjälp av integrerade bärbara datorer och följande verktyg i sin webbläsare:<br/>- Jupyter.<br/>- JupyterLab.<br/>- RStudio|
|Hanterad & säker|Minska ditt säkerhetsavtryck och lägg till efterlevnad av företagets säkerhetskrav. Beräkningsinstanser ger robusta hanteringsprinciper och säkra nätverkskonfigurationer, till exempel:<br/><br/>- Automatisk etablering från Resource Manager-mallar eller Azure Machine Learning SDK<br/>- [Rollbaserad åtkomstkontroll (RBAC)](/azure/role-based-access-control/overview)<br/>- [Stöd för virtuella nätverk](how-to-enable-virtual-network.md#compute-instance)<br/>- SSH-princip för att aktivera/inaktivera SSH-åtkomst|
|Förkonfigurerad&nbsp;&nbsp;eller ML|Spara tid på installationsuppgifter med förkonfigurerade och uppdaterade ML-paket, ramverk för djupinlärning, GPU-drivrutiner.|
|Helt anpassningsbar|Brett stöd för Azure VM-typer, inklusive GPU:er och beständig anpassning på låg nivå, till exempel installera paket och drivrutiner, gör avancerade scenarier enkelt. |

## <a name="tools-and-environments"></a><a name="contents"></a>Verktyg och miljöer

Azure Machine Learning-beräkningsinstansen gör att du kan skapa, träna och distribuera modeller i en helt integrerad anteckningsboksupplevelse på arbetsytan.


Dessa verktyg och miljöer installeras på beräkningsinstansen: 

|Allmänna verktyg & miljöer|Information|
|----|:----:|
|Drivrutiner|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI-bibliotek||
|Azure CLI ||
|Exempel på Azure Machine Learning ||
|EDAT-motor för Azure Machine Learning ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**R-verktyg** & miljöer|Information|
|----|:----:|
|RStudio Server Open Source Edition||
|R-kärna||
|Azure Machine Learning SDK för R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK-exempel|

|**PYTHON-verktyg** & miljöer|Information|
|----|----|
|Anaconda Python||
|Jupyter och förlängningar||
|Jupyterlab och förlängningar||
|Visual Studio-koden ||
[Azure Machine Learning-SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>från PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Andra PyPI-paket|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-paket|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Paket med djupinlärning|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-paket|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python & R SDK-exempel||

Python-paket är alla installerade i **Python 3.6 - AzureML-miljön.**  

Beräkningsinstanser används vanligtvis som utvecklingsmiljöer.  De kan också användas som ett beräkningsmål för utbildning och inferencing för utveckling och testning.  För stora uppgifter är ett [Azure Machine Learning-beräkningskluster](how-to-set-up-training-targets.md#amlcompute) med flernodskalningsfunktioner ett bättre val av beräkningsmål.

### <a name="installing-packages"></a>Installera paket

Du kan installera paket direkt i en Jupyter-anteckningsbok eller Rstudio:

* RStudio Använd fliken **Paket** längst ned till höger eller fliken **Konsol** längst upp till vänster.  
* Python: Lägg till installationskod och kör i en Jupyter-anteckningsbokscell.

Eller så kan du komma åt ett terminalfönster på något av följande sätt:

* RStudio: Välj fliken **Terminal** längst upp till vänster.
* Jupyter Lab: Välj **terminalpanelen** under den **andra** rubriken på fliken Startprogrammet.
* Jupyter: Välj **Ny>Terminal** längst upp till höger på fliken Filer.
* SSH till maskinen.  Installera sedan Python-paket i **Python 3.6 - AzureML-miljön.**  Installera R-paket i **R-miljön.**

## <a name="accessing-files"></a>Komma åt filer

Anteckningsböcker och R-skript lagras i standardlagringskontot för din arbetsyta i Azure-filresursen.  Dessa filer finns under katalogen "Användarfiler". Den här lagringen gör det enkelt att dela anteckningsböcker mellan beräkningsinstanser. Lagringskontot bevarar även dina anteckningsböcker på ett säkert sätt när du stoppar eller tar bort en beräkningsinstans.

Azure-filresurskontot för din arbetsyta är monterat som en enhet på beräkningsinstansen. Den här enheten är standardarbetskatalogen för Jupyter, Jupyter Labs och RStudio.

Filerna i filresursen är tillgängliga från alla beräkningsinstanser på samma arbetsyta. Alla ändringar av dessa filer i beräkningsinstansen sparas på ett tillförlitligt sätt tillbaka till filresursen.

Du kan också klona de senaste Azure Machine Learning-exemplen till mappen under användarfilkatalogen i arbetsytefilresursen.

Skriva små filer kan vara långsammare på nätverksenheter än att skriva till den virtuella datorn själv.  Om du skriver många små filer kan du prova att använda `/tmp` en katalog direkt på beräkningsinstansen, till exempel en katalog. Observera att dessa filer inte kommer att vara tillgängliga från andra beräkningsinstanser på arbetsytan.

## <a name="managing-a-compute-instance"></a>Hantera en beräkningsinstans

På arbetsytan i Azure Machine Learning studio väljer du **Beräkna**och väljer sedan **Beräkningsinstans** högst upp.

![Hantera en beräkningsinstans](./media/concept-compute-instance/manage-compute-instance.png)

Du kan utföra följande åtgärder:

* Skapa en beräkningsinstans. Ange namnet, Azure VM-typ inklusive GPU:er (observera att VM-typ inte kan ändras när du har skapat), aktivera/inaktivera SSH-åtkomst och konfigurera virtuella nätverksinställningar som tillval. Du kan också skapa en instans direkt från integrerade anteckningsböcker, Azure-portal, Resource Manager-mall eller Azure Machine Learning SDK. De dedikerade kärnorna per regionkvot som gäller för att skapa beräkningsinstanser är enhetlig och delas med Azure Machine Learning-beräkningsklusterkvoten.
* Uppdatera fliken beräkningsinstanser
* Starta, stoppa och starta om en beräkningsinstans
* Ta bort en beräkningsinstans

För varje beräkningsinstans på arbetsytan kan du:

* Tillgång Jupyter, JupyterLab, RStudio på beräkningsinstansen
* SSH till beräkningsinstans. SSH-åtkomst är inaktiverad som standard men kan aktiveras vid skapande av beräkningsinstans. SSH-åtkomst sker via offentlig/privat nyckelmekanism. Fliken ger dig information om SSH-anslutning som IP-adress, användarnamn och portnummer.
* Få information om en specifik beräkningsinstans som IP-adress och region.

[Med RBAC](/azure/role-based-access-control/overview) kan du styra vilka användare på arbetsytan som kan skapa, ta bort, starta, stoppa, starta om en beräkningsinstans. Alla användare i arbetsytans deltagare och ägarroll kan skapa, ta bort, starta, stoppa och starta om beräkningsinstanser över arbetsytan. Endast skaparen av en specifik beräkningsinstans får dock komma åt Jupyter, JupyterLab och RStudio på den beräkningsinstansen. Skaparen av beräkningsinstansen har beräkningsinstansen dedikerad till dem, har root-åtkomst och kan terminala in genom Jupyter. Beräkningsinstansen har enanvändarinloggning av skaparanvändare och alla åtgärder kommer att använda användarens identitet för RBAC och attribution av experimentkörningar. SSH-åtkomst kontrolleras genom offentlig/privat nyckelmekanism.

Du kan också skapa en instans
* Direkt från den integrerade bärbara datorupplevelsen
* I Azure-portalen
* Från Azure Resource Manager-mall
* Med Azure Machine Learning SDK

De dedikerade kärnorna per regionkvot, som gäller för att skapa beräkningsinstanser, är enhetlig och delas med Azure Machine Learning-utbildningsklusterkvoten. 

## <a name="compute-target"></a>Beräkningsmål

Beräkningsinstanser kan användas som ett [utbildningsberäkningsmål](concept-compute-target.md#train) som liknar Azure Machine Learning-beräkningsutbildningskluster. Etablera en virtuell dator med flera GPU för att köra distribuerade utbildningsjobb med TensorFlow/PyTorch-skattningsanordningar. Du kan också skapa en körningskonfiguration och använda den för att köra experimentet på beräkningsinstansen. Du kan använda beräkningsinstans som ett lokalt distributionsmål för testning/felsökning.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Vad har hänt med den virtuella datorn för bärbara datorer?

Beräkningsinstanser ersätter den virtuella datorn för bärbara datorer.  

Alla anteckningsfiler som lagras i arbetsytans filresurs och data i datalager för arbetsytan kan nås från en beräkningsinstans. Alla anpassade paket som tidigare installerats på en virtuell dator för bärbara datorer måste dock installeras om på beräkningsinstansen. Kvotbegränsningar som gäller för skapande av beräkningskluster gäller även för att skapa beräkningsinstans. 

Det går inte att skapa nya virtuella datorer för bärbara datorer. Du kan dock fortfarande komma åt och använda virtuella datorer för bärbara datorer som du har skapat, med full funktionalitet. Beräkningsinstanser kan skapas på samma arbetsyta som de befintliga virtuella datorerna för bärbara datorer. 


## <a name="next-steps"></a>Nästa steg

 * [Självstudiekurs: Träna din första ML-modell](tutorial-1st-experiment-sdk-train.md) visar hur du använder en beräkningsinstans med en integrerad anteckningsbok.
