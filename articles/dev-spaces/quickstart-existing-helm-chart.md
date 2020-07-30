---
title: Utveckla ett program med ett befintligt Helm-diagram på Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: Den här snabb starten visar hur du använder Azure dev Spaces och kommando raden för att utveckla ett program med ett befintligt Helm-diagram på Azure Kubernetes-tjänsten
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
manager: gwallace
ms.custom: devx-track-javascript
ms.openlocfilehash: 7d1d782203401607d3909d79d1f0f3a499712c32
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414317"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Snabb start: utveckla ett program med ett befintligt Helm-diagram på Kubernetes – Azure dev Spaces
I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Kör ett program med ett befintligt Helm-diagram i AKS med hjälp av Azure dev Spaces på kommando raden.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free).
- [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes Service-kluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Kommandona nedan skapar en resurs grupp med namnet *MyResourceGroup* och ett AKS-kluster som kallas *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure dev Spaces i ditt AKS-kluster

Använd `use-dev-spaces` kommandot för att aktivera dev Spaces på ditt AKS-kluster och följ anvisningarna. Kommandot nedan aktiverar dev Spaces i *MyAKS* -klustret i gruppen *MyResourceGroup* och skapar ett dev-utrymme som heter *dev*.

> [!NOTE]
> `use-dev-spaces`Kommandot installerar även Azure dev Spaces CLI om det inte redan är installerat. Det går inte att installera Azure dev Spaces CLI i Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Hämta exempel program kod

I den här artikeln använder du [exempel programmet Azure dev Spaces](https://github.com/Azure/dev-spaces) för att demonstrera användningen av Azure dev Spaces.

Klona programmet från GitHub och navigera till katalogen *dev-Spaces/samples/python/kom-start/webfrontend* :

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Förbereda programmet

För att kunna köra ditt program på Azure dev Spaces behöver du ett Dockerfile-och Helm-diagram. För vissa språk, till exempel [Java][java-quickstart], [.net Core][netcore-quickstart]och [Node.js][nodejs-quickstart], kan Azure dev Spaces client-verktyget generera alla de till gångar du behöver. För många andra språk, till exempel Go, PHP och python, kan klient verktyget generera Helm-diagrammet så länge du kan ange en giltig Dockerfile. I det här fallet har exempel programmet ett befintligt Dockerfile-och Helm-diagram

Generera konfigurationen för att köra programmet med Azure dev Spaces med det befintliga Helm-diagrammet och Dockerfile med hjälp av `azds prep` kommandot:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

Du måste köra `prep` kommandot från katalogen *dev-Spaces/samples/python/kom-start/webfrontend* och ange platsen för Helm-diagrammet med hjälp av `--chart` .

> [!NOTE]
> Varningen kan visas: *Varning: Dockerfile kunde inte genereras på grund av ett språk som inte stöds.* När du kör `azds prep` . `azds prep`Kommandot försöker generera [ett Dockerfile-och Helm-diagram](how-dev-spaces-works-prep.md#prepare-your-code) för projektet, men kommer inte att skriva över några befintliga Dockerfiles-eller Helm-diagram.

## <a name="build-and-run-code-in-kubernetes"></a>Skapa och köra kod i Kubernetes

Skapa och kör koden i AKS med hjälp av `azds up` kommandot:

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

Du kan se den tjänst som körs genom att öppna den offentliga URL: en, som visas i utdata från `azds up` kommandot. I det här exemplet är den offentliga URL: en `http://dev.service.1234567890abcdef1234.eus.azds.io/` .

> [!NOTE]
> När du navigerar till tjänsten när du kör `azds up` , visas även spårningen av HTTP-begäranden i `azds up` kommandots utdata. De här spårningarna kan hjälpa dig att felsöka och felsöka tjänsten. Du kan inaktivera dessa spårningar med `--disable-http-traces` när du kör `azds up` .

Om du avbryter `azds up` kommandot med *CTRL + c*fortsätter tjänsten att köras i AKS och den offentliga URL: en är tillgänglig.

## <a name="update-code"></a>Uppdatera kod

Om du vill distribuera en uppdaterad version av tjänsten kan du uppdatera alla filer i projektet och köra kommandot igen `azds up` . Ett exempel:

1. Om körs `azds up` fortfarande trycker du på *CTRL + c*.
1. Uppdatera [rad 13 i `webfrontend.py` ](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13) till:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Spara ändringarna.
1. Kör `azds up` kommandot igen:

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Navigera till den tjänst som körs och observera dina ändringar.
1. Tryck på *CTRL + c* för att stoppa `azds up` kommandot.

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