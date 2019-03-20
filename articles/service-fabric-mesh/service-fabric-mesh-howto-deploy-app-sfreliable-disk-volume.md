---
title: Använda med hög tillgänglighet Service Fabric tillförlitliga diskvolymen i nät för Azure Service Fabric-program | Microsoft Docs
description: Lär dig hur du lagrar tillstånd i ett Azure Service Fabric nät program genom att montera Service Fabric tillförlitliga baserat diskvolymen i behållaren med hjälp av Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: ashishnegi
manager: raunakpandya
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: b5e4ad30a65b25140cfb2c80dd15d8cd28fb827b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57850861"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Montera med hög tillgänglighet Service Fabric tillförlitliga baserat diskvolymen i ett Service Fabric-nät program 
Den vanliga metoden för att bevara tillstånd med behållarappar är att använda Fjärrlagring som Azure File Storage eller databas som Azure Cosmos DB. Detta medför betydande läsning och skrivning Nätverksfördröjningen till arkivet för fjärråtkomst.

Den här artikeln visar hur du lagrar tillstånd i högtillgänglig Service Fabric tillförlitliga Disk genom att montera en volym inuti behållaren i ett Service Fabric-nät program.
Service Fabric tillförlitliga Disk ger volymer för lokala läsningar skrivningar replikeras inom Service Fabric-kluster för hög tillgänglighet. Detta tar bort nätverksanrop för läsningar och minskar Nätverksfördröjningen för skrivningar. Om behållaren startas om eller flyttas till en annan nod, visas ny behållarinstans på samma volym som äldre. Det är därför både effektivt och med hög tillgänglighet.

I det här exemplet har räknaren programmet en ASP.NET Core-tjänst med en webbsida som visar räknarvärde i en webbläsare.

Den `counterService` regelbundet läser ett värde för prestandaräknaren från en fil, ökar den och skriva tillbaka till filen. Filen lagras i en mapp som är monterad på den volym som backas upp av Service Fabric tillförlitliga Disk.

## <a name="prerequisites"></a>Förutsättningar

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här uppgiften. Om du vill använda Azure CLI med den här artikeln, kontrollerar du att `az --version` returnerar minst `azure-cli (2.0.43)`.  Installera (eller uppdatera) Azure Service Fabric nät CLI-tillägg-modulen genom att följa dessa [instruktioner](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure och konfigurera din prenumeration.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp som programmet ska distribueras till. Följande kommando skapar en resursgrupp med namnet `myResourceGroup` på en plats i östra USA. Om du ändrar resursgruppens namn i kommandot nedan, Kom ihåg att ändra den i alla kommandon som följer.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Distribuera mallen

Följande kommando distribuerar en Linux-program med den [counter.sfreliablevolume.linux.json mallen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). För att distribuera ett Windows-program kan använda den [counter.sfreliablevolume.windows.json mallen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Tänk på att större behållaravbildningar kan ta längre tid att distribuera.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Du kan även visa statusen för distributionen med kommandot

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Lägg märke till namnet på gatewayresursen som har resursen skriver som `Microsoft.ServiceFabricMesh/gateways`. Detta används hämta offentliga IP-adressen för appen.

## <a name="open-the-application"></a>Öppna programmet

När programmet har distribuerats kan du hämta IP-adress till gatewayresursen för appen. Använda såg du på gatewaynamnet ovanför avsnittet.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --gateway-name counterGateway
```

Utdata bör ha en egenskap `ipAddress` som är den offentliga IP-adressen för tjänsteslutpunkt. Öppna en webbläsare. En webbsida visas med räknarvärdet håller på att uppdateras varje sekund.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Kontrollera att programmet ska kunna använda volym

Programmet skapar en fil med namnet `counter.txt` i volym inuti `counter/counterService` mapp. Innehållet i den här filen är räknarvärdet som visas på sidan.

## <a name="delete-the-resources"></a>Ta bort resurser

Ofta ta bort de resurser som du inte längre använder i Azure. Ta bort de resurser som rör det här exemplet genom att ta bort resursgruppen som de har distribuerats (som tar bort allt som är associerade med resursgruppen) med följande kommando:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- Visa exempelprogram för Service Fabric tillförlitliga volym Disk på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Mer information om Service Fabric-resursmodellen finns i avsnittet om [Service Fabric Mesh-resursmodellen](service-fabric-mesh-service-fabric-resources.md).
- Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).
