---
title: Tillförlitlig diskvolym för service fabric med servicetygnät
description: Lär dig hur du lagrar tillstånd i ett Azure Service Fabric Mesh-program genom att montera Service Fabric Reliable Disk-baserad volym i behållaren med Azure CLI.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: f26fe70afe7d9e2872f06ac6da7143556278b1b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497962"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Montera tillförlitlig diskbaserad serviceinfrastruktur-baserad volym i ett Service Fabric Mesh-program 
Den vanligaste metoden för att bevara tillståndet med behållarappar är att använda fjärrlagring som Azure File Storage eller databas som Azure Cosmos DB. Detta medför betydande läs- och skrivnätverksfördröjning till fjärrarkivet.

Den här artikeln visar hur du lagrar tillstånd i tillförlitlig disk med servicelagring genom att montera en volym inuti behållaren för ett Service Fabric Mesh-program.
Service Fabric Reliable Disk ger volymer för lokala läsningar med skrivningar som replikeras i Service Fabric Cluster för hög tillgänglighet. Detta tar bort nätverkanrop för läsningar och minskar nätverksfördröjningen för skrivningar. Om behållaren startar om eller flyttas till en annan nod visas samma volym som den äldre. Således är det både effektivt och högtillgänge.

I det här exemplet har Counter-programmet en ASP.NET Core-tjänst med en webbsida som visar räknarvärde i en webbläsare.

Den `counterService` periodvis läser ett räknarvärde från en fil, ökar den och skriver tillbaka den till filen. Filen lagras i en mapp som är monterad på volymen som backas upp av Service Fabric Reliable Disk.

## <a name="prerequisites"></a>Krav

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här uppgiften. Om du vill använda Azure CLI `az --version` med `azure-cli (2.0.43)`den här artikeln kontrollerar du att returnerar minst .  Installera (eller uppdatera) azure service fabric mesh CLI-tilläggsmodulen genom att följa dessa [instruktioner](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure och konfigurera din prenumeration.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp som programmet ska distribueras till. Följande kommando skapar en resursgrupp som namnges på `myResourceGroup` en plats i östra USA. Om du ändrar namnet på resursgruppen nedan bör du komma ihåg att ändra det i alla kommandon som följer.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Distribuera mallen

Följande kommando distribuerar ett Linux-program med mallen [counter.sfreliablevolume.linux.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Om du vill distribuera ett Windows-program använder du [mallen counter.sfreliablevolume.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Tänk på att större behållaravbildningar kan ta längre tid att distribuera.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Du kan också se tillståndet för distributionen med kommandot

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Lägg märke till namnet på gatewayresursen som har resurstyp som `Microsoft.ServiceFabricMesh/gateways`. Detta kommer att användas för att få offentliga IP-adressen för appen.

## <a name="open-the-application"></a>Öppna programmet

När programmet har distribuerats får du ipAddress för gateway-resursen för appen. Använd gatewaynamnet som du lade märke till i avsnittet ovan.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

Utdata ska ha `ipAddress` en egenskap som är den offentliga IP-adressen för tjänstens slutpunkt. Öppna den från en webbläsare. Den visar en webbsida där räknarvärdet uppdateras varje sekund.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Kontrollera att programmet kan använda volymen

Programmet skapar en fil `counter.txt` som namnges i volymen inuti `counter/counterService` mappen. Innehållet i den här filen är det räknarvärde som visas på webbsidan.

## <a name="delete-the-resources"></a>Ta bort resurserna

Ta ofta bort de resurser som du inte längre använder i Azure. Om du vill ta bort de resurser som är relaterade till det här exemplet tar du bort resursgruppen där de distribuerades (som tar bort allt som är associerat med resursgruppen) med följande kommando:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- Visa exempelprogrammet För tillförlitlig volymdisk för service fabric på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Mer information om Service Fabric-resursmodellen finns i avsnittet om [Service Fabric Mesh-resursmodellen](service-fabric-mesh-service-fabric-resources.md).
- Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).
