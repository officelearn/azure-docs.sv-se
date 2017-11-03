---
title: Installation av Azure Machine Learning modellen hantering och konfiguration | Microsoft Docs
description: "Det här dokumentet beskriver stegen och begrepp som är involverad i installera och konfigurera hantering av modellen i Azure Machine Learning."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ms.openlocfilehash: c89596a6d721c4cba899b8a6e2859ee36cba7b80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-setup"></a>Installation av hantering av modellen

## <a name="overview"></a>Översikt
Det här dokumentet hjälper dig att komma igång med att använda hantering av Azure ML-modell för att distribuera och hantera dina maskininlärning modeller som webbtjänster. 

Med hantering av Azure ML-modell kan du effektivt distribuera och hantera Machine Learning-modeller som har skapats med hjälp av ett antal ramverk, till exempel SparkML, Keras, TensorFlow, Microsoft kognitiva Toolkit eller Python. 

Du ska kunna ha din miljö för hantering av modellen har skapat och redo för att distribuera din maskininlärning modeller i slutet av det här dokumentet.

## <a name="what-you-need-to-get-started"></a>Vad du behöver att komma igång
Du bör ha ägare åtkomst till en Azure-prenumeration som du kan distribuera modeller till för att få ut mesta möjliga av den här guiden.
CLI finns förinstallerat på Azure Machine Learning arbetsstationen och på [Azure DSVMs](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>Med hjälp av CLI
Om du vill använda kommandoradsverktyget-gränssnitt (CLIs) från arbetsstationen **filen** -] **öppna CommandLine gränssnittet**. 

På en datavetenskap virtuell dator, Anslut och öppna Kommandotolken. Typen `az ml -h` att se alternativen. Mer information om kommandon som kan använda flaggan--hjälp.

Du skulle behöva installera CLIs på andra system.

### <a name="installing-or-updating-on-windows"></a>Installera (eller uppdatera) i Windows

Installera Python från https://www.python.org/. Se till att du har valt för att installera pip.

Öppna Kommandotolken med hjälp av kör som administratör och kör följande kommandon:

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>Om du har en tidigare version, avinstallerar du den först med hjälp av följande kommando:
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>Installera (eller uppdatera) på Linux
Kör följande kommando från kommandoraden och följ anvisningarna:

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
>Logga ut och logga in igen SSH-session för att ändringarna ska börja gälla.
>Du kan använda de tidigare kommandona för att uppdatera en tidigare version av CLIs på DSVM.
>

## <a name="deploying-your-model"></a>Distribuera din modell
Använd CLIs för att distribuera modeller som webbtjänster. Webbtjänsterna som kan distribueras lokalt eller till ett kluster.

Börja med en lokal distribution, verifiera att din modell koden fungerar och sedan distribuera till ett kluster för produktion för skalan.

Om du vill starta, måste du konfigurera din distributionsmiljö. Inställningen av miljön är en tid. När installationen är klar kan återanvända du miljön för efterföljande distributioner. Se följande avsnitt för mer information.

När installationen miljö:
- Du uppmanas att logga in på Azure. Logga in genom att använda en webbläsare att öppna sidan https://aka.ms/devicelogin och ange den angivna koden för att autentisera.
- Under autentiseringen efterfrågas ett konto för att autentisera med. Viktigt: Välj ett konto som har ett giltigt Azure-prenumeration och tillräcklig behörighet för att skapa resurser för kontot. - när du är klar logga in på din prenumerationsinformation visas och du tillfrågas om du vill fortsätta med den valda kontot.

### <a name="environment-setup"></a>Miljökonfiguration
Om du vill starta installationen måste du registrera providern miljö genom att ange följande kommando:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>Lokal distribution
Konfigurera en lokal miljö med följande kommando för att distribuera och testa webbtjänsten på den lokala datorn:

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Lokala web service-distributionen kräver din installera Docker på den lokala datorn. 
>

Kommandot lokal miljö installationen skapar följande resurser i din prenumeration:
- En resursgrupp (om det angetts)
- ett lagringskonto
- En Azure-behållaren registret (ACR)
- Programinsikter

När installationen är klar kan du ange miljö som ska användas med följande kommando:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Klusterdistribution
Använd Klusterdistribution för scenarier med hög skalning produktion. Det anger en ACS-kluster med Kubernetes som orchestrator. ACS-kluster kan skaländras ut för att hantera större dataflöde för din webbtjänstanrop.

Om du vill distribuera webbtjänsten till en produktionsmiljö måste du först ställa in miljön med hjälp av följande kommando:

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

Kommandot cluster miljö installationen skapar följande resurser i din prenumeration:
- En resursgrupp (om det angetts)
- ett lagringskonto
- En Azure-behållaren registret (ACR)
- En Kubernetes distribution i ett kluster med Azure Container Service (ACS)
- Programinsikter

Resursgrupp, storage-konto och ACR skapas snabbt. ACS-distribution kan ta upp till 20 minuter. 

När installationen är klar måste du ställa in miljön som ska användas för den här distributionen. Ange följande kommando:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> När miljön har skapats för efterföljande distributioner behöver du bara använda kommandot set återanvända den.
>

>[!NOTE] 
>Ange ett SSL-certifikat för att skapa en HTTPS-slutpunkt, när du skapar ett kluster med hjälp av--cert-name och--cert pem alternativen i az ml env installationen. Detta konfigurerar klustret att betjäna förfrågningar på https, kan skyddas med det angivna certifikatet. När installationen är klar, kan du skapa en CNAME DNS-post som pekar på klustrets FQDN.

### <a name="create-an-account"></a>Skapa ett konto
Det krävs ett konto för att distribuera modeller. Du måste göra detta en gång per konto, och du kan återanvända samma konto i flera distributioner.

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
Försök med något av många prover i galleriet.
