---
title: Utveckla ett program med ett befintligt Helm-diagram på Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: Den här snabbstarten visar hur du använder Azure Dev Spaces och kommandoraden för att utveckla ett program med ett befintligt Helm-diagram på Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s
manager: gwallace
ms.openlocfilehash: e767b1ade2a80882ee33ff1fdd718c691dcefcf3
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82033566"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Snabbstart: Utveckla ett program med ett befintligt Helm-diagram på Kubernetes - Azure Dev Spaces
I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Kör ett program med ett befintligt Helm-diagram i AKS med Azure Dev Spaces på kommandoraden.

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
- [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes-tjänstkluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Kommandona nedan skapar en resursgrupp som heter *MyResourceGroup* och ett AKS-kluster som heter *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure Dev Spaces i AKS-klustret

Använd `use-dev-spaces` kommandot för att aktivera dev spaces i AKS-klustret och följ anvisningarna. Kommandot nedan aktiverar Dev Spaces i *MyAKS-klustret* i gruppen *MyResourceGroup* och skapar ett dev-utrymme som kallas *dev*.

> [!NOTE]
> Kommandot `use-dev-spaces` kommer också att installera Azure Dev Spaces CLI om det inte redan är installerat. Du kan inte installera AZURE Dev Spaces CLI i Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Hämta exempelprogramkod

I den här artikeln använder du [exempelprogrammet Azure Dev Spaces](https://github.com/Azure/dev-spaces) för att demonstrera med Azure Dev Spaces.

Klona programmet från GitHub och navigera till katalogen *dev-spaces/samples/python/getting-started/webfrontend:*

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Förbered programmet

För att kunna köra ditt program på Azure Dev Spaces behöver du ett Dockerfile- och Helm-diagram. För vissa språk, till exempel [Java,][java-quickstart] [.NET core][netcore-quickstart]och [Node.js,][nodejs-quickstart]kan Azure Dev Spaces-klientverktyget generera alla tillgångar du behöver. För många andra språk, till exempel Go, PHP och Python, kan klientverktyget generera Helm-diagrammet så länge du kan tillhandahålla en giltig Dockerfile. I det här fallet har exempelprogrammet ett befintligt Dockerfile- och Helm-diagram

Generera konfigurationen för att köra programmet med Azure Dev Spaces med `azds prep` det befintliga Helm-diagrammet och Dockerfile med kommandot:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

Du måste `prep` köra kommandot från katalogen *dev-spaces/samples/python/getting-started/webfrontend* och ange `--chart`platsen för Helm-diagrammet med .

> [!NOTE]
> Varning: *Varning: Dockerfile kunde inte genereras på grund av språk som inte stöds.* när `azds prep`du kör . Kommandot `azds prep` försöker generera [ett Dockerfile- och Helm-diagram](how-dev-spaces-works-prep.md#prepare-your-code) för projektet, men skriver inte över några befintliga Dockerfiles- eller Helm-diagram.

## <a name="build-and-run-code-in-kubernetes"></a>Skapa och köra kod i Kubernetes

Skapa och kör koden i `azds up` AKS med kommandot:

```cmd
$ azds up
Using dev space 'dev' with target 'MyAKS'
Synchronizing files...14s
Installing Helm chart...2s
Waiting for container image build...3s
Building container image...
Step 1/7 : FROM python:3
Step 2/7 : WORKDIR /python/webfrontend
Step 3/7 : RUN pip install flask
Step 4/7 : COPY webfrontend.py webfrontend.py
Step 5/7 : COPY public/ public/
Step 6/7 : EXPOSE 80
Step 7/7 : CMD ["python", "./webfrontend.py"]
Built container image in 45s
Waiting for container...25s
Service 'azds-543eae-dev-webfrontend' port 'http' is available at http://dev.service.1234567890abcdef1234.eus.azds.io/
Service 'azds-543eae-dev-webfrontend' port 80 (http) is available via port forwarding at http://localhost:52382
Press Ctrl+C to detach
...
```

Du kan se tjänsten som körs genom att öppna den offentliga `azds up` URL:en, som visas i utdata från kommandot. I det här exemplet *http://dev.service.1234567890abcdef1234.eus.azds.io/* är den offentliga WEBBADRESSEN .

> [!NOTE]
> När du navigerar till `azds up`tjänsten när du kör visas http-begäran `azds up` spårningar också i utdata från kommandot. Dessa spår kan hjälpa dig att felsöka och felsöka din tjänst. Du kan inaktivera dessa `--disable-http-traces` spårningar när du kör `azds up`.

Om du `azds up` stoppar kommandot med *Ctrl+c*fortsätter tjänsten att köras i AKS och den offentliga url:en förblir tillgänglig.

## <a name="update-code"></a>Uppdatera kod

Om du vill distribuera en uppdaterad version av tjänsten kan du `azds up` uppdatera alla filer i projektet och köra kommandot igen. Ett exempel:

1. Om `azds up` det fortfarande körs trycker du på *Ctrl+c*.
1. Uppdatera [linje 13 `webfrontend.py` in](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13) till:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Spara ändringarna.
1. Kör kommandot `azds up` igen:

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Navigera till din körtjänst och observera dina ändringar.
1. Tryck på *Ctrl+c* för att stoppa `azds up` kommandot.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure Dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbete genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Teamutveckling i Azure Dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service