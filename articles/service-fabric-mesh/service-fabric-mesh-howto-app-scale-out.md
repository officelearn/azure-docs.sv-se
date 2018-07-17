---
title: Skala tjänster i ett Azure Service Fabric nät program | Microsoft Docs
description: Lär dig hur du oberoende skala tjänster i ett program som körs på Service Fabric nät med hjälp av Azure CLI.
services: service-fabric
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
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: c0350b767b65aee0c4611bb8fa6f635a651d33dc
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076552"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Skala tjänster i ett program som körs på Service Fabric-nät

Den här artikeln visar hur du oberoende skala mikrotjänster i ett program. I det här exemplet består visuella objekt program av två mikrotjänster; `web` och `worker`. 

Den `web` tjänsten är ett ASP.NET Core-program med en webbsida som visar trianglar i webbläsaren. Webbläsaren visar en triangel för varje instans av den `worker` service. 

Den `worker` tjänsten flyttar på triangeln med ett fördefinierat intervall i området och skickar platsen för triangeln till `web` service. Den använder DNS för att matcha adressen för den `web` service.

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurera Service Fabric nät CLI 
Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här uppgiften. Installera Azure Service Fabric nät CLI-tillägg-modulen genom att följa dessa [instruktioner](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure och ange din prenumeration.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Skapa resursgrupp
Skapa en resursgrupp för att distribuera programmet till. Du kan använda en befintlig resursgrupp och hoppa över detta steg. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>Distribuera programmet med en worker-tjänsten
Skapa ditt program i en resurs-grupp med den `deployment create` kommando.

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
Föregående kommando distribuerar en Linux med hjälp av [mesh_rp.base.linux.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Om du vill distribuera ett Windows-program kan använda [mesh_rp.base.windows.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Windows-behållaravbildningar är större än Linux-behållaravbildningar och kan ta längre tid att distribuera.

Om några minuter bör kommandot returnerar med:

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Öppna programmet
När programmet har distribuerats hämta den offentliga IP-adressen för tjänsteslutpunkt och öppna den i en webbläsare. En webbsida visas med en triangel flytta via utrymmet.

Distributionskommandot returnerar den offentliga IP-adressen för tjänsteslutpunkt. Du kan också fråga nätverksresurs för att hitta den offentliga IP-adressen till tjänstens slutpunkt.
 
Resursnamnet nätverk för det här programmet är `visualObjectsNetwork`, hämta information om den med hjälp av följande kommando. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Skala `worker` service

Skala den `worker` i tre instanser med följande kommando. 

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
Föregående kommando distribuerar en Linux med hjälp av [mesh_rp.scaleout.linux.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Om du vill distribuera ett Windows-program kan använda [mesh_rp.scaleout.windows.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Windows-behållaravbildningar är större än Linux-behållaravbildningar och kan ta längre tid att distribuera.

När programmet har distribuerats bör webbläsaren visa en webbsida för med tre trianglar flytta via utrymmet.

## <a name="delete-the-resources"></a>Ta bort resurser

Om du vill spara de begränsade resurser som tilldelats för förhandsversionen kan du ta bort resurserna ofta. Ta bort resurser relaterade till det här exemplet genom att ta bort resursgruppen som de har distribuerats.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Nästa steg
- Visa exempelprogrammet visuella objekt på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Läs mer om Service Fabric-Resursmodell i [nät Resursmodell i Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Om du vill veta mer om Service Fabric-nät kan läsa den [Service Fabric-nät översikt](service-fabric-mesh-overview.md).