---
title: Installera Azure dev Spaces på AKS och klient sidans verktyg
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Lär dig hur du installerar Azure dev Spaces i ett AKS-kluster och installerar verktyg på klient sidan.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
ms.openlocfilehash: c62fe38a12b5ec279bc51fe8bc0d340e2f439200
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280057"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>Installera Azure dev Spaces på AKS och klient sidans verktyg

Den här artikeln visar flera olika sätt att installera Azure dev Spaces på ett AKS-kluster samt installera verktyg på klient sidan.

## <a name="install-azure-dev-spaces-using-the-cli"></a>Installera Azure dev Spaces med CLI

Innan du kan installera dev-utrymmen med hjälp av CLI behöver du:
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][az-portal-create-account].
* [Azure CLI installerat][install-cli].
* [Ett AKS-kluster][create-aks-cli] i en [region som stöds][supported-regions].

Använd kommandot `use-dev-spaces` för att aktivera dev Spaces i ditt AKS-kluster och följa anvisningarna.

```cmd
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Kommandot ovan aktiverar dev Spaces i *myAKSCluster* -klustret i gruppen *myResourceGroup* och skapar ett *standard* dev-utrymme.

```cmd
$ az aks use-dev-spaces -g myResourceGroup -n myAKSCluster

'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

Kommandot `use-dev-spaces` installerar även Azure dev Spaces CLI.

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>Installera Azure dev Spaces med hjälp av Azure Portal

Innan du kan installera dev-utrymmen med Azure Portal behöver du:
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][az-portal-create-account].
* [Ett AKS-kluster][create-aks-portal] i en [region som stöds][supported-regions].

Så här installerar du Azure dev Spaces med hjälp av Azure Portal:
1. Logga in på [Azure-portalen][az-portal].
1. Navigera till ditt AKS-kluster.
1. Klicka på *dev Spaces*.
1. Ändra *Aktivera dev Spaces* till *Ja* och klicka på *Spara*.

![Aktivera dev Spaces i Azure Portal](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Om du installerar Azure dev Spaces med hjälp av Azure Portal installeras **inte** verktyg på klient sidan för Azure dev Spaces.

## <a name="install-the-client-side-tooling"></a>Installera klient sidans verktyg

Du kan använda Azure dev Spaces client-verktyg för att interagera med dev-utrymmen i ett AKS-kluster från den lokala datorn. Det finns flera sätt att installera klient sidans verktyg på:

* I [Visual Studio Code][vscode]installerar du [tillägget Azure dev Spaces][vscode-extension].
* Installera arbets belastningen Azure Development i [Visual Studio 2019][visual-studio].
* I Visual Studio 2017 installerar du arbets belastningen webb utveckling och [Visual Studio Tools för Kubernetes][visual-studio-k8s-tools].
* Ladda ned och installera [Windows][cli-win], [Mac][cli-mac]eller [Linux][cli-linux] cli.

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbets utveckling genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Grupp utveckling i Azure dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
