---
title: Store tillstånd genom att montera Azure Files baserat volym inuti behållare i Service Fabric-nät program | Microsoft Docs
description: Lär dig hur du lagrar tillstånd genom att montera Azure Files baserat volym inuti behållare i Service Fabric-nät program med hjälp av Azure CLI.
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
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 94a4e17e6285893520a2f6482b32a69b1229e2fa
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090640"
---
# <a name="store-state-by-mounting-azure-files-based-volume-in-service-fabric-mesh-application"></a>Store genom att montera Azure Files statusbaserad volym i Service Fabric-nät program

Den här artikeln visar hur du lagrar tillstånd i Azure Files genom att montera en volym inuti behållaren i ett Service Fabric-nät program. I det här exemplet har räknaren program en ASP.NET Core-tjänst med en webbsida som visar räknarvärde i en webbläsare. 

Den `counterService` perodically läser ett värde för prestandaräknaren från en fil, ökar den och skriva tillbaka till filen. Filen lagras i en mapp som är monterad på den volym som backas upp av Azure Files-resurs. 

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurera Service Fabric nät CLI 
Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här uppgiften. Installera Azure Service Fabric nät CLI-tillägg-modulen genom att följa dessa [instruktioner](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure och ange din prenumeration.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-file-share"></a>Skapa en filresurs 
Skapa en Azure-filresurs genom att följa de här [instruktioner](/azure/storage/files/storage-how-to-create-file-share). Lagringskontonamn, din lagringskontonyckel och namn på filresurs är refereras till som `<storageAccountName>`, `<storageAccountKey>`, och `<fileShareName>` i följande anvisningar.

## <a name="create-resource-group"></a>Skapa resursgrupp
Skapa en resursgrupp för att distribuera programmet till. Du kan använda en befintlig resursgrupp och hoppa över detta steg. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Distribuera mallen

Skapa program och relaterade resurser med följande kommando och ange värden för `storageAccountName`, `storageAccountKey` och `fileShareName` från föregående steg.

Den `storageAccountKey` parametern i mallen är en `securestring`. Den visas inte i distributionens status och `az mesh service show` kommandon. Se till att den har angetts korrekt i följande kommando.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

Föregående kommando distribuerar en Linux-program med [mesh_rp.linux.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Om du vill distribuera ett Windows-program kan använda [mesh_rp.windows.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Windows-behållaravbildningar är större än Linux-behållaravbildningar och kan ta längre tid att distribuera.

Om några minuter bör kommandot returnerar med:

`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Öppna programmet
När programmet har distribuerats hämta den offentliga IP-adressen för tjänsteslutpunkt och öppna den i en webbläsare. Den visar en webbsida med räknarvärdet håller på att uppdateras varje sekund.

Distributionskommandot returnerar den offentliga IP-adressen för tjänsteslutpunkt. Du kan också kan du också fråga nätverksresurs för att hitta den offentliga IP-adressen till tjänstens slutpunkt. 
 
Resursnamnet nätverk för det här programmet är `counterAppNetwork`, hämta information om den med hjälp av följande kommando. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Kontrollera att programmet ska kunna använda volym
Programmet skapar en fil med namnet `counter.txt` i filen dela inuti `counter/counterService` mapp. Innehållet i den här filen är räknarvärdet som visas på sidan.

Filen laddas ned med ett verktyg som gör att en filresurs i Azure Files-surfning. Den [Microsoft Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/) är ett exempel på sådant verktyg.

## <a name="delete-the-resources"></a>Ta bort resurser

Om du vill spara de begränsade resurser som tilldelats för förhandsversionen kan du ta bort resurserna ofta. Ta bort resurser relaterade till det här exemplet genom att ta bort resursgruppen som de har distribuerats.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Nästa steg

- Visa Azure Files volym exempelprogrammet på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Läs mer om Service Fabric-Resursmodell i [nät Resursmodell i Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Om du vill veta mer om Service Fabric-nät kan läsa den [Service Fabric-nät översikt](service-fabric-mesh-overview.md).
