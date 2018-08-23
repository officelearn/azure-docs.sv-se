---
title: Store tillstånd i ett Azure Service Fabric nät program genom att montera en Azure-filer baserat volym inuti behållaren | Microsoft Docs
description: Lär dig hur du lagrar tillstånd i ett Azure Service Fabric nät program genom att montera en Azure-filer baserat volym inuti behållaren med hjälp av Azure CLI.
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
ms.date: 08/09/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 3b350deff2883761af6a3a2b3c5c9ef22235bde0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42058773"
---
# <a name="store-state-in-an-azure-service-fabric-mesh-application-by-mounting-an-azure-files-based-volume-inside-the-container"></a>Store-tillstånd i ett Azure Service Fabric nät program genom att montera en Azure-filer baserat volym inuti behållaren

Den här artikeln visar hur du lagrar tillstånd i Azure Files genom att montera en volym inuti behållaren i ett Service Fabric-nät program. I det här exemplet har räknaren programmet en ASP.NET Core-tjänst med en webbsida som visar räknarvärde i en webbläsare. 

Den `counterService` regelbundet läser ett värde för prestandaräknaren från en fil, ökar den och skriva tillbaka till filen. Filen lagras i en mapp som är monterad på den volym som backas upp av Azure Files-resurs.

## <a name="prerequisites"></a>Förutsättningar

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här uppgiften. Om du vill använda Azure CLI med den här artikeln, kontrollerar du att `az --version` returnerar minst `azure-cli (2.0.43)`.  Installera (eller uppdatera) Azure Service Fabric nät CLI-tillägg-modulen genom att följa dessa [instruktioner](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure och konfigurera din prenumeration.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-file-share"></a>Skapa en filresurs

Skapa en Azure-filresurs genom att följa de här [instruktioner](/azure/storage/files/storage-how-to-create-file-share). Lagringskontonamn, din lagringskontonyckel och namn på filresurs är refereras till som `<storageAccountName>`, `<storageAccountKey>`, och `<fileShareName>` i följande anvisningar. Dessa värden är tillgängliga i Azure portal:
* <storageAccountName> -Under **Lagringskonton**, det är namnet på det lagringskonto som du använde när du skapade filresursen.
* <storageAccountKey> -Välj ditt lagringskonto under **Lagringskonton** och välj sedan **åtkomstnycklar** och använda värde under **key1**.
* <fileShareName> -Välj ditt lagringskonto under **Lagringskonton** och välj sedan **filer**. Namn är namnet på den filresurs som du nyss skapade.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp som programmet ska distribueras till. Följande kommando skapar en resursgrupp med namnet `myResourceGroup` på en plats i östra USA.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Distribuera mallen

Skapa program och relaterade resurser med följande kommando och ange värden för `storageAccountName`, `storageAccountKey` och `fileShareName` från den tidigare [skapa en filresurs](#create-a-file-share) steg.

Den `storageAccountKey` parametern i mallen är en säker sträng. Den visas inte i distributionens status och `az mesh service show` kommandon. Se till att den har angetts korrekt i följande kommando.

Följande kommando distribuerar en Linux-program med den [mesh_rp.linux.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). För att distribuera ett Windows-program kan använda den [mesh_rp.windows.json mallen](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Tänk på att större behållaravbildningar kan ta längre tid att distribuera.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

Om några minuter bör kommandot returnerar med `counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>Öppna programmet

Distributionskommandot returneras den offentliga IP-adressen för tjänsteslutpunkt. När programmet har distribuerats kan du hämta den offentliga IP-adressen för tjänsteslutpunkt och öppna den från en webbläsare. En webbsida visas med räknarvärdet håller på att uppdateras varje sekund.

Resursnamnet nätverk för det här programmet är `counterAppNetwork`. Du kan se information om appen dess beskrivning, plats, resursgrupp osv med hjälp av följande kommando:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Kontrollera att programmet ska kunna använda volym

Programmet skapar en fil med namnet `counter.txt` i filen dela inuti `counter/counterService` mapp. Innehållet i den här filen är räknarvärdet som visas på sidan.

Filen laddas ned med ett verktyg som gör det möjligt att bläddra en Azure Files-filresurs som den [Microsoft Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/).

## <a name="delete-the-resources"></a>Ta bort resurser

Ofta ta bort de resurser som du inte längre använder i Azure. Ta bort de resurser som rör det här exemplet genom att ta bort resursgruppen som de har distribuerats (som tar bort allt som är associerade med resursgruppen) med följande kommando:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- Visa Azure Files volym exempelprogrammet på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Läs mer om Service Fabric-Resursmodell i [nät Resursmodell i Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Om du vill veta mer om Service Fabric-nät kan läsa den [Service Fabric-nät översikt](service-fabric-mesh-overview.md).