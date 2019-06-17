---
title: Så här skapar du ett Kubernetes-kluster som har aktiverats för Azure Dev lagringsutrymmen med hjälp av Azure Cloud Shell
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster som har aktiverats för Azure Dev blanksteg direkt från din webbläsare utan att installera något.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, tjänsten nät, tjänsten nät routning, kubectl, k8s
ms.openlocfilehash: cd0c8c3c26feefe3448ada1cf1575706cd17e525
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808684"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Skapa ett Kubernetes-kluster med Azure Cloud Shell

Du kan använda [Azure Cloud Shell](/azure/cloud-shell) att skapa ett Azure Kubernetes Service-kluster med hjälp av den **prova** knappen från den här sidan. Om du inte är inloggad, följ anvisningarna för att logga in med ett Azure-konto, så Skriv kommandon i Azure Cloud Shell-prompten när den visas.

## <a name="create-the-cluster"></a>Skapa klustret

Börja med att skapa resursgruppen i ett [region som stöder Azure Dev blanksteg][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Skapa ett Kubernetes-kluster med följande kommando:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

Det tar några minuter att skapa klustret.  När du är klar visas utdata i JSON-format. Leta efter `provisioningState` och kontrollera att den har `Succeeded`.

## <a name="next-steps"></a>Nästa steg

Se [Azure Dev blanksteg](/azure/dev-spaces/) länkar till fullständig självstudier.

> [!IMPORTANT]
> Många av de Azure Dev blanksteg snabbstarter och självstudier kan använda Azure Dev blanksteg CLI för att utföra åtgärder. Du kan inte installera Azure Dev blanksteg CLI i Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations