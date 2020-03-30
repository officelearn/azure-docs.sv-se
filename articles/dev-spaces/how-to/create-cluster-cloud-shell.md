---
title: Skapa ett Kubernetes-kluster med Azure Dev Spaces aktiverat - Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster aktiverat för Azure Dev Spaces direkt från din webbläsare utan att installera något.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s
ms.openlocfilehash: 5e2e5cfd22eeedd3554737458caeca0b891b62fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605299"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Skapa ett Kubernetes-kluster med Azure Dev Spaces aktiverat med Azure Cloud Shell

Du kan använda [Azure Cloud Shell](/azure/cloud-shell) för att skapa ett Azure Kubernetes Service-kluster med hjälp av knappen **Prova** på från den här sidan. Om du inte är inloggad följer du anvisningarna för att logga in med ett Azure-konto och skriver sedan kommandona på Azure Cloud Shell-prompten när den visas.

## <a name="create-the-cluster"></a>Skapa klustret

Skapa först resursgruppen i en [region som stöder Azure Dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Skapa ett Kubernetes-kluster med följande kommando:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Det tar några minuter att skapa klustret.  När du är klar visas utdata i JSON-formatet. Leta `provisioningState` efter och kontrollera `Succeeded`att det är .

## <a name="next-steps"></a>Nästa steg

Se [Azure Dev Spaces](/azure/dev-spaces/) för länkar till fullständiga självstudier.

> [!IMPORTANT]
> Många av azure dev spaces-snabbstarter och självstudiekurser använder Azure Dev Spaces CLI för att utföra åtgärder. Du kan inte installera AZURE Dev Spaces CLI i Azure Cloud Shell.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service