---
title: Distribuera en app till Azure Service Fabric nät med hjälp av en mall | Microsoft Docs
description: Lär dig hur du distribuerar en .NET Core-program till Service Fabric-nät från en mall med Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 356e8019f9a4a64cb1c1c113d0f44990aa4e0f95
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42056727"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>Distribuera ett Service Fabric-nät program till Service Fabric nät med hjälp av en mall
Den här artikeln visar hur du distribuerar en .NET Core-program till Service Fabric-nät med hjälp av en mall. När du är klar har du ett röstningsprogram med en ASP.NET-kärnans webbklient som sparar röstningsresultat i en serverdelstjänst i klustret. Klient använder DNS för att matcha adressen för backend-tjänst.

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurera Service Fabric Mesh CLI 
Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här uppgiften. Installera Azure Service Fabric Mesh CLI-tilläggsmodulen genom att följa de här [instruktionerna](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure och konfigurera din prenumeration.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Skapa resursgrupp
Skapa en resursgrupp som programmet ska distribueras till. Du kan använda en befintlig resursgrupp och hoppa över det här steget. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Distribuera programmet
Skapa ditt program i en resurs-grupp med den `deployment create` kommando.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

Föregående kommando distribuerar ett Windows-program med [mesh_rp.windows.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json). Om du vill distribuera ett program för Linux, använda [mesh_rp.linux.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json). Windows-containeravbildningar är större än Linux-containeravbildningar och kan ta längre tid att distribuera.

Om några minuter bör kommandot returnerar med:

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>` 

## <a name="open-the-application"></a>Öppna programmet
När programmet har distribuerats hämta den offentliga IP-adressen för tjänsteslutpunkt och öppna den i en webbläsare. Den visar följande webbsida. 

![Röstningsprogrammet](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

Du kan nu lägga till röstningsalternativ i programmet och rösta på det eller ta bort röstningsalternativen.

Distributionskommandot returnerar den offentliga IP-adressen för tjänsteslutpunkt. Du kan också kan du också fråga nätverksresurs för att hitta den offentliga IP-adressen till tjänstens slutpunkt. 

Resursnamnet nätverk för det här programmet är `VotingAppNetwork`, hämta information om den med hjälp av följande kommando. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="check-the-application-details"></a>Kontrollera programinformationen
Du kan kontrollera programmets status med kommandot `app show`. Programnamn för det distribuerade programmet är ”VotingApp”, så hämta information om den. 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

## <a name="list-the-deployed-applications"></a>Lista över distribuerade program
Med kommandot "app list" kan du hämta en lista över program som du har distribuerat till prenumerationen. 

```azurecli-interactive
az mesh app list -o table
```

## <a name="clean-up-resources"></a>Rensa resurser
När du inte längre behöver programmet och är relaterade resurser, ta bort resursgruppen som innehåller dessa. 

```azurecli-interactive
az group delete --resource-group myResourceGroup  
``` 

## <a name="next-steps"></a>Nästa steg
- Visa röstning på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp).
- Om du vill veta mer om Service Fabric-nät kan läsa den [Service Fabric-nät översikt](service-fabric-mesh-overview.md).


