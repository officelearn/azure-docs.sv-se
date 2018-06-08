---
title: Installation av Azure Machine Learning modellen hantering och konfiguration | Microsoft Docs
description: Det här dokumentet beskriver stegen och begrepp som är involverad i installera och konfigurera hantering av modellen i Azure Machine Learning.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 12/6/2017
ms.openlocfilehash: bf96a6986abe858074decaaecfe38b3807822aab
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830249"
---
# <a name="model-management-setup"></a>Installation av hantering av modellen

Det här dokumentet hjälper dig att komma igång med att använda hantering av Azure ML-modell för att distribuera och hantera dina maskininlärning modeller som webbtjänster. 

Med hantering av Azure ML-modell kan du effektivt distribuera och hantera Machine Learning-modeller som har skapats med hjälp av ett antal ramverk, till exempel SparkML, Keras, TensorFlow, Microsoft kognitiva Toolkit eller Python. 

Du ska kunna ha din miljö för hantering av modellen har skapat och redo för att distribuera din maskininlärning modeller i slutet av det här dokumentet.

## <a name="what-you-need-to-get-started"></a>Vad du behöver att komma igång
Du bör ha deltagarbehörighet åtkomst till en Azure-prenumeration eller resursgrupp som du kan distribuera modeller till för att få mest av den här guiden.
CLI finns förinstallerat på Azure Machine Learning arbetsstationen och på [Azure DSVMs](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Med hjälp av CLI
Om du vill använda kommandoradsverktyget-gränssnitt (CLIs) från arbetsstationen **filen** -> **öppnar du kommandotolken**. 

På en datavetenskap virtuell dator, Anslut och öppna Kommandotolken. Typen `az ml -h` att se alternativen. Mer information om kommandon som kan använda flaggan--hjälp.

Du skulle behöva installera CLIs på andra system.

### <a name="installing-or-updating-on-windows"></a>Installera (eller uppdatera) i Windows

Installera Python från https://www.python.org/. Se till att du har valt för att installera pip.

Öppna Kommandotolken med hjälp av kör som administratör och kör följande kommandon:

```cmd
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="installing-or-updating-on-linux"></a>Installera (eller uppdatera) på Linux
Kör följande kommando från kommandoraden och följ anvisningarna:

```bash
sudo -i
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="configuring-docker-on-linux"></a>Konfigurera Docker på Linux
För att kunna konfigurera Docker på Linux för rot-användare, följer du anvisningarna här: [efter installationen steg för Linux](https://docs.docker.com/engine/installation/linux/linux-postinstall/)

>[!NOTE]
> Du kan köra skriptet nedan för att konfigurera Docker korrekt på en Linux DSVM. **Kom ihåg att logga ut och logga in igen när skriptet har körts.**
>```
>sudo /opt/microsoft/azureml/initial_setup.sh
>```

## <a name="deploying-your-model"></a>Distribuera din modell
Använd CLI för att distribuera modeller som webbtjänster. Webbtjänsterna som kan distribueras lokalt eller till ett kluster.

Börja med en lokal distribution, verifiera att din modell koden fungerar och sedan distribuera till ett kluster för produktion för skalan.

Om du vill starta, måste du konfigurera din distributionsmiljö. Inställningen av miljön är en tid. När installationen är klar kan återanvända du miljön för efterföljande distributioner. Se följande avsnitt för mer information.

När installationen miljö:
- Du uppmanas att logga in på Azure. Logga in genom att använda en webbläsare och gå till sidan https://aka.ms/devicelogin och ange den angivna koden för att autentisera.
- Under autentiseringen efterfrågas ett konto för att autentisera med. Viktigt: Välj ett konto som har ett giltigt Azure-prenumeration och tillräcklig behörighet för att skapa resurser i kontot. När du är klar logga in på din prenumerationsinformation visas och tillfrågas du om du vill fortsätta med det valda kontot.

### <a name="environment-setup"></a>Miljökonfiguration
Om du vill starta installationen måste du registrera några miljö providrar genom att ange följande kommandon:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
```
#### <a name="local-deployment"></a>Lokal distribution
Konfigurera en lokal miljö med följande kommando för att distribuera och testa webbtjänsten på den lokala datorn. Resursgruppens namn är valfritt.

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Lokala web service-distributionen måste du installera Docker på den lokala datorn. 
>

Kommandot lokal miljö installationen skapar följande resurser i din prenumeration:
- En resursgrupp (om inte, eller det angivna namnet inte finns)
- ett lagringskonto
- En Azure-behållaren registret (ACR)
- Ett Application insights-konto

När installationen är klar kan du ange miljö som ska användas med följande kommando:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Klusterdistribution
Använd Klusterdistribution för scenarier med hög skalning produktion. Det anger en ACS-kluster med Kubernetes som orchestrator. ACS-kluster kan skaländras ut för att hantera större dataflöde för din webbtjänstanrop.

Om du vill distribuera webbtjänsten till en produktionsmiljö måste du först ställa in miljön med hjälp av följande kommando:

```azurecli
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. eastus2] [-g [resource group]]
```

Kommandot cluster miljö installationen skapar följande resurser i din prenumeration:
- En resursgrupp (om inte, eller det angivna namnet inte finns)
- ett lagringskonto
- En Azure-behållaren registret (ACR)
- En Kubernetes distribution i ett kluster med Azure Container Service (ACS)
- Ett Application insights-konto

>[!IMPORTANT]
> För att kunna skapa en klustermiljö, behöver du ha deltagare åtkomst på Azure-prenumeration eller resursgruppen.

Resursgrupp, storage-konto och ACR skapas snabbt. ACS-distribution kan ta upp till 20 minuter. 

Om du vill kontrollera status för en pågående klusteretablering, använder du följande kommando:

```azurecli
az ml env show -n [environment name] -g [resource group]
```

När installationen är klar måste du ställa in miljön som ska användas för den här distributionen. Ange följande kommando:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> När miljön har skapats för efterföljande distributioner behöver du bara använda kommandot set återanvända den.
>

### <a name="create-a-model-management-account"></a>Skapa ett konto för hantering av modellen
Det krävs ett konto för hantering av modellen för att distribuera modeller. Du måste göra detta en gång per prenumeration, och du kan återanvända samma konto i flera distributioner.

Om du vill skapa ett nytt konto, använder du följande kommando:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Om du vill använda ett befintligt konto, använder du följande kommando:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

På grund av den här processen miljön är klar och modellen hanteringskontot har skapats för att tillhandahålla funktioner som behövs för att hantera och distribuera Machine Learning-modeller (se [Azure Machine Learning modellen Management](model-management-overview.md) för en Översikt över).

## <a name="next-steps"></a>Nästa steg

* För instruktioner om hur du distribuerar webbtjänster ska köras på en lokal dator eller ett kluster fortsätter på [distribuerar en Maskininlärningsmodell som en webbtjänst](model-management-service-deploy.md).
* Försök med något av de många beräkningarna i galleriet.
