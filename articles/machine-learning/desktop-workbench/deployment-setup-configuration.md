---
title: Azure Machine Learning Model Management installation och konfiguration | Microsoft Docs
description: Det här dokumentet beskriver de steg och koncept som ingår i att installera och konfigurera modellhantering i Azure Machine Learning.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 12/06/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 2d912f559af98045aaef8fe6fcaa1d83ab5f96bd
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269370"
---
# <a name="model-management-setup"></a>Installation av hantering av modellen

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


Det här dokumentet hjälper dig att komma igång med att använda Azure ML-modellhantering att distribuera och hantera dina machine learning-modeller som webbtjänster. 

Med Azure ML-modellhantering kan du effektivt distribuera och hantera Machine Learning-modeller som skapas med hjälp av ett antal ramverk, till exempel SparkML, Keras, TensorFlow, Microsoft Cognitive Toolkit eller Python. 

I slutet av det här dokumentet, bör du kunna ha din modellhanteringsmiljö förberetts och kan nu för att distribuera dina maskininlärningsmodeller.

## <a name="what-you-need-to-get-started"></a>Vad du behöver att komma igång
Du bör ha deltagarbehörighet åtkomst till en Azure-prenumeration eller resursgrupp som du kan distribuera dina modeller för att få ut mest av den här guiden.
CLI är redan installerat på Azure Machine Learning Workbench och på [Azure Dsvm](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Med hjälp av CLI
Om du vill använda kommandoradsverktyget-gränssnitt (CLI) från Workbench, klickar du på **filen** -> **öppna Kommandotolken**. 

På en virtuell dator för datavetenskap, Anslut och öppna Kommandotolken. Typ `az ml -h` att visa alternativen. Mer information om kommandona som använder flaggan--help.

På andra system, skulle du behöva installera de CLI: erna.

>[!NOTE]
> Jupyter notebook på en Linux DSVM, kan du komma åt Azure CLI och Azure ML CLI med kommandoformatet nedan.  **Detta är specifikt för en Jupyter-anteckningsbok på en Linux DSVM**.  De här kommandona åt den aktuella Python-kerneln i anteckningsboken (t.ex. conda `py35` miljö)
>```
>import sys
>! {sys.executable} -m azure.cli login
>! {sys.executable} -m azure.cli ml -h
>```

### <a name="installing-or-updating-on-windows"></a>Installera (eller uppdatera) på Windows

Installera Python från https://www.python.org/. Se till att du har valt för att installera pip.

Öppna Kommandotolken med hjälp av kör som administratör och kör följande kommandon:

```cmd
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="installing-or-updating-on-linux"></a>Installera (eller uppdatera) på Linux
Kör följande kommando från kommandoraden och följa anvisningarna:

```bash
sudo -i
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="configuring-docker-on-linux"></a>Konfigurera Docker på Linux
Om du vill konfigurera Docker på Linux för icke-begränsande användare, följer du instruktionerna här: [Anvisningarna efter installation för Linux](https://docs.docker.com/engine/installation/linux/linux-postinstall/)

>[!NOTE]
> Du kan köra skriptet nedan för att konfigurera Docker på rätt sätt på en Linux DSVM. **Kom ihåg att logga ut och logga in igen när skriptet har körts.**
>```
>sudo /opt/microsoft/azureml/initial_setup.sh
>```

## <a name="deploying-your-model"></a>Distribuera din modell
Använd CLI för att distribuera modeller som webbtjänster. Webbtjänster kan distribueras lokalt eller till ett kluster.

Börja med en lokal distribution, verifiera att din modell och kod fungerar, sedan distribuera till ett kluster för användning i produktion skala.

Om du vill börja, måste du konfigurera din distributionsmiljö. Miljö-installationen är en tid. När installationen är klar, kan du återanvända miljö för efterföljande distributioner. Se följande avsnitt innehåller mer information.

När du har slutfört miljö-installationen:
- Du uppmanas att logga in på Azure. För att logga in, använder du en webbläsare för att öppna sidan https://aka.ms/devicelogin och ange koden som tillhandahålls för att autentisera.
- Under autentiseringsprocessen uppmanas du för ett konto för att autentisera med. Viktigt: Välj ett konto som har en giltig Azure-prenumeration och tillräcklig behörighet för att skapa resurser i kontot. När du är klar logga in din prenumerationsinformation visas och du blir tillfrågad om du vill fortsätta med det valda kontot.

### <a name="environment-setup"></a>Konfigurera miljön
Om du vill starta installationsprocessen, måste du registrera några miljö providrar genom att ange följande kommandon:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
```
#### <a name="local-deployment"></a>Lokal distribution
Konfigurera en lokal miljö med följande kommando för att distribuera och testa din webbtjänst på den lokala datorn. Resursgruppens namn är valfritt.

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Distribution av lokala webbtjänster måste du installera Docker på den lokala datorn. 
>

Installationskommandot lokal miljö skapar följande resurser i din prenumeration:
- En resursgrupp (om det inte finns eller om det angivna namnet inte finns)
- Ett lagringskonto
- Ett Azure Container Registry (ACR)
- En Application insights-konto

När installationen är klar ställer du in miljön som ska användas med följande kommando:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Distribution av kluster
Använd Klusterdistribution för produktionsscenarier med hög skalbarhet. Anger en ACS-kluster med Kubernetes som orchestrator. ACS-kluster kan skalas upp för att hantera större dataflöde för din webbtjänstanrop.

Om du vill distribuera webbtjänsten till en produktionsmiljö måste du först ställa in miljön med följande kommando:

```azurecli
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. eastus2] [-g [resource group]]
```

Kommandot cluster miljö installationen skapar följande resurser i din prenumeration:
- En resursgrupp (om det inte finns eller om det angivna namnet inte finns)
- Ett lagringskonto
- Ett Azure Container Registry (ACR)
- En Kubernetes-distribution på ett Azure Container Service (ACS)-kluster
- En Application insights-konto

>[!IMPORTANT]
> För att skapa en klustermiljö, behöver du vara har deltagarbehörighet på Azure-prenumerationen eller resursgruppen.

Resursgruppen, lagringskontot och ACR skapas snabbt. ACS-distributionen kan ta upp till 20 minuter. 

Om du vill kontrollera status för en pågående klusteretablering, använder du följande kommando:

```azurecli
az ml env show -n [environment name] -g [resource group]
```

När installationen är klar kan behöva du ställa in miljön som ska användas för den här distributionen. Ange följande kommando:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> När miljön har skapats för efterföljande distributioner behöver du bara använda kommandot set för att återanvända den.
>

### <a name="create-a-model-management-account"></a>Skapa ett Modellhanteringskonto
Ett modellhanteringskonto krävs för att distribuera modeller. Du behöver göra detta en gång per prenumeration, och du kan återanvända samma konto i flera distributioner.

Om du vill skapa ett nytt konto, använder du följande kommando:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Om du vill använda ett befintligt konto, använder du följande kommando:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

Till följd av den här processen miljön är klar och modellhanteringskontot har skapats för att tillhandahålla de funktioner som behövs för att hantera och distribuera Machine Learning-modeller (se [Azure Machine Learning-modellhantering](model-management-overview.md) för en Översikt över).

## <a name="next-steps"></a>Nästa steg

* För anvisningar om hur du distribuerar webbtjänster för körning på en lokal dator eller ett kluster fortsätta till [distribuerar en Maskininlärningsmodell som en webbtjänst](model-management-service-deploy.md).
* Prova en av många exempel i galleriet.
