---
title: Skapa ett Kubernetes-kluster med Azure dev Spaces aktiverat – Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster som är aktiverat för Azure dev Spaces direkt från webbläsaren utan att installera något.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
ms.openlocfilehash: 35ea52b940c74b34817b4ed12d224de83f4dda74
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771112"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Skapa ett Kubernetes-kluster med Azure dev Spaces aktiverat med Azure Cloud Shell

Du kan använda [Azure Cloud Shell](/azure/cloud-shell) för att skapa ett Azure Kubernetes service-kluster genom att använda knappen **prova** från den här sidan. Om du inte har loggat in, följer du anvisningarna för att logga in med ett Azure-konto och skriver sedan kommandona i Azure Cloud Shell prompt när det visas.

## <a name="create-the-cluster"></a>Skapa klustret

Skapa först resurs gruppen i en [region som stöder Azure dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Skapa ett Kubernetes-kluster med följande kommando:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

Det tar några minuter att skapa klustret.  När du är klar visas utdata i JSON-format. Leta efter `provisioningState` och kontrol lera att den är `Succeeded`.

## <a name="next-steps"></a>Nästa steg

I [Azure dev Spaces](/azure/dev-spaces/) finns länkar till fullständiga självstudier.

> [!IMPORTANT]
> Många av våra snabb starter och självstudier för Azure dev Spaces använder Azure dev Spaces CLI för att utföra åtgärder. Det går inte att installera Azure dev Spaces CLI i Azure Cloud Shell.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service