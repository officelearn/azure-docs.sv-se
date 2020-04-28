---
title: Utveckla ett program på Kubernetes
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: Den här snabb starten visar hur du använder Azure dev Spaces och kommando raden för att utveckla ett program i Azure Kubernetes-tjänsten
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
manager: gwallace
ms.openlocfilehash: 337c3cb139e1fe0c35344e49271503b98a59fa7b
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166010"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>Snabb start: utveckla ett program på Kubernetes – Azure dev Spaces
I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Utveckla och kör kod i behållare med hjälp av kommando raden.

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free).
- [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes service-kluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Kommandona nedan skapar en resurs grupp med namnet *MyResourceGroup* och ett AKS-kluster som kallas *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure dev Spaces i ditt AKS-kluster

Använd `use-dev-spaces` kommandot för att aktivera dev Spaces på ditt AKS-kluster och följ anvisningarna. Kommandot nedan aktiverar dev-utrymmen i *MyAKS* -klustret i gruppen *MyResourceGroup* och skapar ett *standard* dev-utrymme.

> [!NOTE]
> `use-dev-spaces` Kommandot installerar även Azure dev Spaces CLI om det inte redan är installerat. Det går inte att installera Azure dev Spaces CLI i Azure Cloud Shell.

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

## <a name="get-sample-application-code"></a>Hämta exempel program kod

I den här artikeln använder du [exempel programmet Azure dev Spaces](https://github.com/Azure/dev-spaces) för att demonstrera användningen av Azure dev Spaces.

Klona programmet från GitHub och navigera till katalogen *dev-Spaces/samples/NodeJS/komma igång/webfrontend* :

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Förbereda programmet

För att kunna köra ditt program på Azure dev Spaces behöver du ett Dockerfile-och Helm-diagram. För vissa språk, till exempel [Java][java-quickstart], [.net Core][netcore-quickstart]och [Node. js][nodejs-quickstart], kan Azure dev Spaces client-verktyget generera alla de till gångar du behöver. För många andra språk, till exempel Go, PHP och python, kan klient verktyget generera Helm-diagrammet så länge du kan ange en giltig Dockerfile.

Generera Docker-och Helm-diagrammets till gångar för att köra programmet i `azds prep` Kubernetes med hjälp av kommandot:

```cmd
azds prep --enable-ingress
```

Du måste köra `prep` kommandot från katalogen *dev-Spaces/samples/NodeJS/kom-start/webfrontend* för att kunna generera Docker-och Helm-diagrammets till gångar.

> [!TIP]
> `prep` Kommandot försöker generera [ett Dockerfile-och Helm-diagram](how-dev-spaces-works-prep.md#prepare-your-code) för projektet. Azure dev Spaces använder dessa filer för att skapa och köra din kod, men du kan ändra dessa filer om du vill ändra hur projektet skapas och körs.

## <a name="build-and-run-code-in-kubernetes"></a>Skapa och köra kod i Kubernetes

Skapa och kör koden i AKS med hjälp av `azds up` kommandot:

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
Service 'webfrontend' port 'http' is available at `http://webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Du kan se den tjänst som körs genom att öppna den offentliga URL: en, som visas i utdata `azds up` från kommandot. I det här exemplet är *`http://webfrontend.1234567890abcdef1234.eus.azds.io/`* den offentliga URL: en.

> [!NOTE]
> När du navigerar till tjänsten när du `azds up`kör, visas även spårningen av HTTP-begäranden i `azds up` kommandots utdata. De här spårningarna kan hjälpa dig att felsöka och felsöka tjänsten. Du kan inaktivera dessa spårningar `--disable-http-traces` med när `azds up`du kör.

Om du avbryter `azds up` kommandot med *CTRL + c*fortsätter tjänsten att köras i AKS och den offentliga URL: en är tillgänglig.

## <a name="update-code"></a>Uppdatera kod

Om du vill distribuera en uppdaterad version av tjänsten kan du uppdatera alla filer i projektet och köra `azds up` kommandot igen. Ett exempel:

1. Om `azds up` körs fortfarande trycker du på *CTRL + c*.
1. Uppdatera [rad 13 i `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) till:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Spara ändringarna.
1. Kör `azds up` kommandot igen:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Navigera till den tjänst som körs och observera dina ändringar.
1. Tryck på *CTRL + c* för att `azds up` stoppa kommandot.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbets utveckling genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Grupp utveckling i Azure dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service