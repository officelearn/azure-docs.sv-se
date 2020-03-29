---
title: Aktivera Azure Dev Spaces på AKS & installera verktygen på klientsidan
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Lär dig hur du aktiverar Azure Dev Spaces i ett AKS-kluster och installerar verktygen på klientsidan.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898954"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Aktivera Azure Dev Spaces i ett AKS-kluster och installera verktygen på klientsidan

Den här artikeln visar flera sätt att aktivera Azure Dev Spaces i ett AKS-kluster samt installera verktyg på klientsidan.

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>Aktivera eller ta bort Azure Dev Spaces med CLI

Innan du kan aktivera Dev Spaces med CLI behöver du:
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][az-portal-create-account].
* [Azure CLI har installerat][install-cli].
* [Ett AKS-kluster][create-aks-cli] i en [region som stöds][supported-regions].

Använd `use-dev-spaces` kommandot för att aktivera dev spaces i AKS-klustret och följ anvisningarna.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Kommandot Ovan aktiverar Dev Spaces i myAKSCluster-klustret i *myResourceGroup-gruppen* och skapar ett *standardutrymme* för dev.The above command enables Dev Spaces on the *myAKSCluster* cluster in the myResourceGroup group and creates a default dev space.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

Kommandot `use-dev-spaces` installerar också Azure Dev Spaces CLI.

Om du vill ta bort Azure Dev `azds remove` Spaces från AKS-klustret använder du kommandot. Ett exempel:

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Kommandot Ovan tar bort Azure Dev Spaces från *MyAKS-klustret* i *MyResourceGroup*. Alla namnområden som du har skapat med Azure Dev Spaces finns kvar tillsammans med sina arbetsbelastningar, men nya arbetsbelastningar i dessa namnområden kommer inte att instrumenteras med Azure Dev Spaces. Om du startar om befintliga poddar som är instrumenterade med Azure Dev Spaces kan du dessutom se fel. Dessa poddar måste distribueras om utan Azure Dev Spaces-verktyg. Om du vill ta bort Azure Dev Spaces helt från klustret tar du bort alla poddar i alla namnområden där Azure Dev Spaces har aktiverats.

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>Aktivera eller ta bort Azure Dev Spaces med Azure-portalen

Innan du kan aktivera Dev Spaces med Azure-portalen behöver du:
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][az-portal-create-account].
* [Ett AKS-kluster][create-aks-portal] i en [region som stöds][supported-regions].

Så här aktiverar du Azure Dev Spaces med Azure-portalen:
1. Logga in på [Azure-portalen][az-portal].
1. Navigera till AKS-klustret.
1. Välj menyalternativet *Dev Spaces.*
1. Ändra *Aktivera dev spaces* till *Ja* och klicka på *Spara*.

![Aktivera dev spaces i Azure-portalen](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Om du aktiverar Azure Dev Spaces med Azure-portalen installeras **inga** klientverktyg för Azure Dev Spaces.

Om du vill ta bort Azure Dev Spaces från AKS-klustret ändrar du *Aktivera utvecklingsutrymmen* till *Nej* och klickar på *Spara*. Alla namnområden som du har skapat med Azure Dev Spaces finns kvar tillsammans med sina arbetsbelastningar, men nya arbetsbelastningar i dessa namnområden kommer inte att instrumenteras med Azure Dev Spaces. Om du startar om befintliga poddar som är instrumenterade med Azure Dev Spaces kan du dessutom se fel. Dessa poddar måste distribueras om utan Azure Dev Spaces-verktyg. Om du vill ta bort Azure Dev Spaces helt från klustret tar du bort alla poddar i alla namnområden där Azure Dev Spaces har aktiverats.

## <a name="install-the-client-side-tools"></a>Installera verktygen på klientsidan

Du kan använda Azure Dev Spaces-klientverktygen för att interagera med utvecklingsutrymmen på ett AKS-kluster från den lokala datorn. Det finns flera sätt att installera verktygen på klientsidan:

* Installera [azure dev spaces-tillägget][vscode-extension]i [Visual Studio-kod][vscode].
* Installera Azure Development-arbetsbelastningen i [Visual Studio 2019.][visual-studio]
* Installera arbetsbelastningen för webbutveckling och Visual [Studio Tools för Kubernetes][visual-studio-k8s-tools]i Visual Studio 2017.
* Ladda ned och installera [Windows,][cli-win] [Mac][cli-mac]eller [Linux][cli-linux] CLI.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure Dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbete genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Teamutveckling i Azure Dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
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
