---
title: Konfigurera Service Fabric Mesh CLI | Microsoft Docs
description: Lär dig hur du konfigurerar Azure Service Fabric Mesh CLI.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c30f4b9de279f8c02b7f6bc7fa7d9765972899b1
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977439"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Konfigurera Service Fabric Mesh CLI
Service Fabric Mesh-kommandoradsgränssnittet (CLI) krävs för att distribuera och hantera resurser i Service Fabric Mesh. 

För förhandsversionen är Azure Service Fabric Mesh CLI skrivet som ett tillägg till Azure CLI. Du kan installera det i Azure Cloud Shell eller en lokal installation av Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-service-fabric-mesh-cli-locally"></a>Installera Service Fabric Mesh CLI lokalt
Om du väljer att installera och använda CLI lokalt måste du installera Azure CLI version 2.0.43 eller senare. Kör `az --version` för att hitta versionen. Om du vill installera eller uppgradera till den senaste versionen av CLI kan du läsa [Installera Azure CLI][azure-cli-install].

Installera Azure Service Fabric Mesh CLI-tilläggsmodulen med hjälp av följande kommando. 

```azurecli-interactive
az extension add --name mesh
```

Kör följande kommando om du behöver uppdatera en befintlig Azure Service Fabric Mesh CLI-modul.

```azurecli-interactive
az extension update --name mesh
```
## <a name="next-steps"></a>Nästa steg

Du kan även konfigurera [Windows-utvecklingsmiljön](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Få svar på [vanliga frågor och problem](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
