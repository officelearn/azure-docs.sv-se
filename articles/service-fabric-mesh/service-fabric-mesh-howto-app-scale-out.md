---
title: Skala tjänster i ett Azure Service Fabric nät program | Microsoft Docs
description: Lär dig hur du oberoende skala tjänster i ett program som körs på Service Fabric nät med hjälp av Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/08/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: e68bcd135c33c7fd83908b8fed0fd098a698fd36
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054138"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Skala tjänster i ett program som körs på Service Fabric-nät

Den här artikeln visar hur du oberoende skala mikrotjänster i ett program. I det här exemplet visuella objekt programmet består av två mikrotjänster: `web` och `worker`.

Den `web` tjänsten är ett ASP.NET Core-program med en webbsida som visar trianglar i webbläsaren. Webbläsaren visar en triangel för varje instans av den `worker` service.

Den `worker` tjänsten flyttar på triangeln med ett fördefinierat intervall i området och skickar platsen för triangeln till `web` service. Den använder DNS för att matcha adressen för den `web` service.

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurera Service Fabric Mesh CLI

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här uppgiften. Installera Azure Service Fabric Mesh CLI-tilläggsmodulen genom att följa de här [instruktionerna](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure och konfigurera din prenumeration.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp som programmet ska distribueras till.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>Distribuera programmet med en worker-tjänsten

Skapa ditt program i en resurs-grupp med den `deployment create` kommando.

I följande exempel distribuerar en Linux-program med den [mesh_rp.base.linux.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). För att distribuera ett Windows-program kan använda den [[mesh_rp.base.windows.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Windows-containeravbildningar är större än Linux-containeravbildningar och kan ta längre tid att distribuera.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

Om några minuter bör kommandot returnerar med:

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Öppna programmet

Distributionskommandot returneras den offentliga IP-adressen för tjänsteslutpunkt. När programmet har distribuerats kan du hämta den offentliga IP-adressen för tjänsteslutpunkt och öppna den från en webbläsare. Den visar en webbsida med en glidande triangel.

Resursnamnet nätverk för det här programmet är `visualObjectsNetwork`. Du kan se information om appen dess beskrivning, plats, resursgrupp osv med hjälp av följande kommando:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Skala `worker` service

Skala den `worker` i tre instanser med följande kommando. I följande exempel distribuerar en Linux-program med den [mesh_rp.scaleout.linux.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). För att distribuera ett Windows-program kan använda den [mesh_rp.scaleout.windows.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Tänk på att större behållaravbildningar kan ta längre tid att distribuera.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```

När programmet har distribuerats visas en webbsida med tre glidande trianglar i webbläsaren.

## <a name="delete-the-resources"></a>Ta bort resurser

Ofta ta bort de resurser som du inte längre använder i Azure. Ta bort de resurser som rör det här exemplet genom att ta bort resursgruppen som de har distribuerats (som tar bort allt som är associerade med resursgruppen) med följande kommando:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- Visa exempelprogrammet visuella objekt på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Läs mer om Service Fabric-Resursmodell i [nät Resursmodell i Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Om du vill veta mer om Service Fabric-nät kan läsa den [Service Fabric-nät översikt](service-fabric-mesh-overview.md).