---
title: Utveckla ett program på Kubernetes
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: Den här snabbstarten visar hur du använder Azure Dev Spaces och kommandoraden för att utveckla ett program på Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 8ee5cba06d9a526640d9057ee88a681d46392f4f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239697"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>Snabbstart: Utveckla ett program på Kubernetes - Azure Dev Spaces
I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Utveckla och kör kod i behållare med kommandoraden.

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

Använd `use-dev-spaces` kommandot för att aktivera dev spaces i AKS-klustret och följ anvisningarna. Kommandot nedan aktiverar Dev Spaces i *MyAKS-klustret* i gruppen MyResourceGroup och skapar ett *standardutrymme* för dev.The below command enables Dev Spaces on the MyAKS cluster in the *MyResourceGroup group* and creates a default dev space.

> [!NOTE]
> Kommandot `use-dev-spaces` kommer också att installera Azure Dev Spaces CLI om det inte redan är installerat. Du kan inte installera AZURE Dev Spaces CLI i Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Hämta exempelprogramkod

I den här artikeln använder du [exempelprogrammet Azure Dev Spaces](https://github.com/Azure/dev-spaces) för att demonstrera med Azure Dev Spaces.

Klona programmet från GitHub och navigera till katalogen *dev-spaces/samples/nodejs/getting-started/webfrontend:*

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Förbered programmet

För att kunna köra ditt program på Azure Dev Spaces behöver du ett Dockerfile- och Helm-diagram. För vissa språk, till exempel [Java,][java-quickstart] [.NET core][netcore-quickstart]och [Node.js,][nodejs-quickstart]kan Azure Dev Spaces-klientverktyget generera alla tillgångar du behöver. För många andra språk, till exempel Go, PHP och Python, kan klientverktyget generera Helm-diagrammet så länge du kan tillhandahålla en giltig Dockerfile.

Generera docker- och helm-diagramresurserna för att köra `azds prep` programmet i Kubernetes med kommandot:

```cmd
azds prep --enable-ingress
```

Du måste `prep` köra kommandot från katalogen *dev-spaces/samples/nodejs/getting-started/webfrontend* för att korrekt generera Docker- och Helm-diagramtillgångarna.

> [!TIP]
> Kommandot `prep` försöker generera [ett Dockerfile- och Helm-diagram](how-dev-spaces-works-prep.md#prepare-your-code) för projektet. Azure Dev Spaces använder dessa filer för att skapa och köra din kod, men du kan ändra dessa filer om du vill ändra hur projektet byggs och kördes.

## <a name="build-and-run-code-in-kubernetes"></a>Skapa och köra kod i Kubernetes

Skapa och kör koden i `azds up` AKS med kommandot:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Du kan se tjänsten som körs genom att öppna den offentliga `azds up` URL:en, som visas i utdata från kommandot. I det här exemplet *http://webfrontend.1234567890abcdef1234.eus.azds.io/* är den offentliga WEBBADRESSEN .

> [!NOTE]
> När du navigerar till `azds up`tjänsten när du kör visas http-begäran `azds up` spårningar också i utdata från kommandot. Dessa spår kan hjälpa dig att felsöka och felsöka din tjänst. Du kan inaktivera dessa `--disable-http-traces` spårningar när du kör `azds up`.

Om du `azds up` stoppar kommandot med *Ctrl+c*fortsätter tjänsten att köras i AKS och den offentliga url:en förblir tillgänglig.

## <a name="update-code"></a>Uppdatera kod

Om du vill distribuera en uppdaterad version av tjänsten kan du `azds up` uppdatera alla filer i projektet och köra kommandot igen. Ett exempel:

1. Om `azds up` det fortfarande körs trycker du på *Ctrl+c*.
1. Uppdatera [linje 13 `server.js` in](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) till:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Spara ändringarna.
1. Kör kommandot `azds up` igen:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
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