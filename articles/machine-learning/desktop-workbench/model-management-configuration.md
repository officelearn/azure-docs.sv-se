---
title: Azure Machine Learning Model Management installation och konfiguration | Microsoft Docs
description: Det här dokumentet beskriver de steg och koncept som ingår i att installera och konfigurera modellhantering i Azure Machine Learning.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ms.openlocfilehash: 883e3d2c5945a38c8fbca5c9f0f5e8a1e4093be1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649866"
---
# <a name="model-management-setup"></a>Installation av hantering av modellen

## <a name="overview"></a>Översikt
Det här dokumentet hjälper dig att komma igång med att använda Azure ML-modellhantering att distribuera och hantera dina machine learning-modeller som webbtjänster. 

Med Azure ML-modellhantering kan du effektivt distribuera och hantera Machine Learning-modeller som skapas med hjälp av ett antal ramverk, till exempel SparkML, Keras, TensorFlow, Microsoft Cognitive Toolkit eller Python. 

I slutet av det här dokumentet, bör du kunna ha din modellhanteringsmiljö förberetts och kan nu för att distribuera dina maskininlärningsmodeller.

## <a name="what-you-need-to-get-started"></a>Vad du behöver att komma igång
Du bör ha ägaråtkomst till en Azure-prenumeration som du kan distribuera dina modeller för att få ut det mesta av den här guiden.
CLI är redan installerat på Azure Machine Learning Workbench och på [Azure Dsvm](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Med hjälp av CLI
Om du vill använda kommandoradsverktyget-gränssnitt (CLI) från Workbench, klickar du på **filen** -] **öppna kommandoradsgränssnittet**. 

På en virtuell dator för datavetenskap, Anslut och öppna Kommandotolken. Typ `az ml -h` att visa alternativen. Mer information om kommandona som använder flaggan--help.

På andra system, skulle du behöva installera de CLI: erna.

### <a name="installing-or-updating-on-windows"></a>Installera (eller uppdatera) på Windows

Installera Python från https://www.python.org/. Se till att du har valt för att installera pip.

Öppna Kommandotolken med hjälp av kör som administratör och kör följande kommandon:

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>Om du har en tidigare version kan du avinstallera den först med hjälp av följande kommando:
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>Installera (eller uppdatera) på Linux
Kör följande kommando från kommandoraden och följa anvisningarna:

```bash
sudo -i
pip install azure-cli
pip install azure-cli-ml
```

När för installationsprogrammet för är klar kör du följande kommando:

```bash
sudo /opt/microsoft/azureml/initial_setup.sh
```

>[!NOTE]
>Logga ut och logga in på din SSH-session för att ändringarna ska börja gälla.
>Du kan använda de tidigare kommandona för att uppdatera en tidigare version av CLI: erna på DSVM.
>

## <a name="deploying-your-model"></a>Distribuera din modell
Använd de CLI: erna för att distribuera modeller som webbtjänster. Webbtjänster kan distribueras lokalt eller till ett kluster.

Börja med en lokal distribution, verifiera att din modell och kod fungerar, sedan distribuera till ett kluster för användning i produktion skala.

Om du vill börja, måste du konfigurera din distributionsmiljö. Miljö-installationen är en tid. När installationen är klar, kan du återanvända miljö för efterföljande distributioner. Se följande avsnitt innehåller mer information.

När du har slutfört miljö-installationen:
- Du uppmanas att logga in på Azure. För att logga in, använder du en webbläsare för att öppna sidan https://aka.ms/devicelogin och ange koden som tillhandahålls för att autentisera.
- Under autentiseringsprocessen uppmanas du för ett konto för att autentisera med. Viktigt: Välj ett konto som har en giltig Azure-prenumeration och tillräcklig behörighet för att skapa resurser i kontot. - när du är klar logga in din prenumerationsinformation visas och du får frågan om du vill fortsätta med den valda kontot.

### <a name="environment-setup"></a>Konfigurera miljön
Om du vill starta installationsprocessen, måste du registrera providern miljö genom att ange följande kommando:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>Lokal distribution
Konfigurera en lokal miljö med följande kommando för att distribuera och testa din webbtjänst på den lokala datorn:

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Distribution av lokala webbtjänster kräver ditt att installera Docker på den lokala datorn. 
>

Installationskommandot lokal miljö skapar följande resurser i din prenumeration:
- En resursgrupp (om det inte anges)
- Ett lagringskonto
- Ett Azure Container Registry (ACR)
- Application Insights

När installationen är klar ställer du in miljön som ska användas med följande kommando:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Distribution av kluster
Använd Klusterdistribution för produktionsscenarier med hög skalbarhet. Anger en ACS-kluster med Kubernetes som orchestrator. ACS-kluster kan skalas upp för att hantera större dataflöde för din webbtjänstanrop.

Om du vill distribuera webbtjänsten till en produktionsmiljö måste du först ställa in miljön med följande kommando:

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

Kommandot cluster miljö installationen skapar följande resurser i din prenumeration:
- En resursgrupp (om det inte anges)
- Ett lagringskonto
- Ett Azure Container Registry (ACR)
- En Kubernetes-distribution på ett Azure Container Service (ACS)-kluster
- Application Insights

Resursgruppen, lagringskontot och ACR skapas snabbt. ACS-distributionen kan ta upp till 20 minuter. 

När installationen är klar kan behöva du ställa in miljön som ska användas för den här distributionen. Ange följande kommando:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> När miljön har skapats för efterföljande distributioner behöver du bara använda kommandot set för att återanvända den.
>

>[!NOTE] 
>För att skapa en HTTPS-slutpunkt, anger du ett SSL-certifikat när du skapar ett kluster med hjälp av – cert-name och--cert pem alternativen i az ml env installationen. Detta ställer in klustret för att hantera begäranden på https som skyddas med det angivna certifikatet. När installationen är klar kan du skapa en CNAME DNS-post som pekar på klustrets FQDN.

### <a name="create-an-account"></a>Skapa ett konto
Det krävs ett konto för att distribuera modeller. Du behöver göra detta en gång per konto, och du kan återanvända samma konto i flera distributioner.

Om du vill skapa ett nytt konto, använder du följande kommando:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Om du vill använda ett befintligt konto, använder du följande kommando:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>Distribuera din modell
Du är nu redo att distribuera din sparade modell som en webbtjänst. 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

### <a name="next-steps"></a>Nästa steg
Prova en av många exempel i galleriet.
