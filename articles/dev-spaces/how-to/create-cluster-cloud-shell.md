---
title: Så här skapar du ett Kubernetes-kluster som är aktiverat för Azure dev Spaces med hjälp av Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster som är aktiverat för Azure dev Spaces direkt från webbläsaren utan att installera något.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
ms.openlocfilehash: dbdc9226e417b3142284386ae3586819cda802d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280099"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Skapa ett Kubernetes-kluster med Azure Cloud Shell

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


[supported-regions]: ../about.md#supported-regions-and-configurations