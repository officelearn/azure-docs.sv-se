---
title: Distribuera ett program med en Dockerfile på Kubernetes med Azure Dev blanksteg
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Distribuera en mikrotjänst med en Dockerfile på AKS med Azure Dev blanksteg
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, tjänsten nät, tjänsten nät routning, kubectl, k8s
manager: gwallace
ms.openlocfilehash: a272047955564ba745d4cf521e560af9d24cfb9e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706842"
---
# <a name="quickstart-develop-an-application-with-a-dockerfile-on-kubernetes-using-azure-dev-spaces"></a>Snabbstart: Utveckla ett program med en Dockerfile på Kubernetes med Azure Dev blanksteg
I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Utveckla och köra kod i behållare med hjälp av kommandoraden.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har en Azure-prenumeration, kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
- [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes Service-kluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Den kommandona nedan skapar du en resursgrupp med namnet *MyResourceGroup* och ett AKS-kluster som heter *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure Dev blanksteg på AKS-kluster

Använd den `use-dev-spaces` kommando för att aktivera Dev blanksteg på AKS-klustret och följa anvisningarna. I nedanstående kommando aktiverar Dev blanksteg på den *MyAKS* -kluster i den *MyResourceGroup* gruppen och skapar en *standard* dev utrymme.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Exempelkod för program

I den här artikeln använder du den [Azure Dev blanksteg exempelprogrammet](https://github.com/Azure/dev-spaces) att visa hur man använder Azure Dev blanksteg. Exempelprogrammet är skriven i Go, men du kan använda nästan alla språk som du anger en giltig Dockerfile och Använd ditt program i behållare som du kan köra den på AKS.

Klona programmet från GitHub och navigera till den *dev-blanksteg/samples/golang/komma-igång/webfrontend* directory:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/golang/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Förbereda programmet

För att köra ditt program på Azure Dev blanksteg, behöver du ett Dockerfile och Helm-diagram. För vissa språk, till exempel [Java][java-quickstart], [.NET core][netcore-quickstart], och [Node.js][nodejs-quickstart], Azure Dev blanksteg klienten verktyg kan skapa de resurser du behöver. För många andra språk, till exempel Go, PHP och Python, kan klienten verktyg generera Helm-diagrammet så länge som du kan ange en giltig Dockerfile.

Exempelprogrammet innehåller en giltig Dockerfile. Om du vill generera tillgångar för Helm-diagrammet för att köra programmet i Kubernetes, använda den `azds prep` kommando:

```cmd
azds prep --public
```

Du måste köra den `prep` från den *dev-blanksteg/samples/golang/komma-igång/webfrontend* directory att korrekt generera Helm-diagram-tillgångar.

# <a name="build-and-run-code-in-kubernetes"></a>Skapa och köra kod i Kubernetes

Skapa och köra din kod i AKS med hjälp av den `azds up` kommando:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...3s
Installing Helm chart...2s
Waiting for container image build...39s
Building container image...
Step 1/7 : FROM golang:1.10
Step 2/7 : EXPOSE 80
Step 3/7 : WORKDIR /go/src
Step 4/7 : COPY src .
Step 5/7 : WORKDIR /go/src/app
Step 6/7 : RUN go install app
Step 7/7 : ENTRYPOINT /go/bin/app
Built container image in 41s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://default.webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:50199
...
```

Du kan se att tjänsten körs genom att öppna en offentlig URL och navigera till den `/api` sökväg. En offentlig URL visas i utdata från den `azds up` kommando. I det här exemplet är en offentlig URL `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/` och du navigera till `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/api`.

Om du stoppar den `azds up` kommando med hjälp av *Ctrl + c*tjänsten fortsätter att köras i AKS och offentligt URL-Adressen förblir tillgängliga.

## <a name="update-code"></a>Uppdatera kod

För att distribuera en uppdaterad version av din tjänst måste du uppdatera alla filer i projektet och kör den `azds up` kommando. Exempel:

1. Om `azds up` är fortfarande körs, tryck på *Ctrl + c*.
1. Uppdatera [rad 29 i `src/app/main.go` ](https://github.com/Azure/dev-spaces/blob/master/samples/golang/getting-started/webfrontend/src/app/main.go#L29) till:
    
    ```golang
        fmt.Fprintf(w, "Hello from webfrontend in Azure")
    ```

1. Spara ändringarna.
1. Kör den `azds up` kommando:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Navigera till tjänsten som körs och se dina ändringar.
1. Tryck på *Ctrl + c* att stoppa den `azds up` kommando.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure Dev blanksteg hjälper dig att utveckla mer komplexa program över flera behållare och du kan förenkla samarbetsfunktioner utveckling genom att arbeta med olika versioner eller grenar av din kod i olika blanksteg.

> [!div class="nextstepaction"]
> [Utveckling i grupp i Azure Dev blanksteg][team-quickstart]


[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: about.md#supported-regions-and-configurations