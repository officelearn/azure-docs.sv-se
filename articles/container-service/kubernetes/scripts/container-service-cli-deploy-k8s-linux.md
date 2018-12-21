---
title: Skriptexempel för Azure CLI – Skapa ACS Linux Kubernetes-kluster| Microsoft Docs
description: Skriptexempel för Azure CLI – Skapa ACS Linux Kubernetes-kluster
services: container-service
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, DC/OS, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 11549e730e38c72e09ed2e94a72113c6e7e5bbb9
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52996275"
---
# <a name="deprecated-create-an-azure-container-service-kubernetes-linux-cluster"></a>(INAKTUELL) Skapa en Azure Container Service med Kubernetes Linux-kluster

[!INCLUDE [ACS deprecation](../../../../includes/container-service-kubernetes-deprecation.md)]

Det här exemplet skapar ett Azure Container Service-kluster som kör Kubernetes för Linux-baserade behållare.

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

```azurecli
az group create --name myResourceGroup --location eastus

az acs create \
  --orchestrator-type kubernetes \
  --resource-group myResourceGroup \
  --name myK8SCluster \
  --generate-ssh-keys
```

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az acs create](https://docs.microsoft.com/cli/azure/acs#az-acs-create) | Skapar ett ACS-kluster. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Azure Container Service CLI-skriptexempel finns i [Azure Container Service-dokumentationen](../cli-samples.md).

