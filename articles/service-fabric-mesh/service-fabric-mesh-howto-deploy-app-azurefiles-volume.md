---
title: Distribuera en Service Fabric-nät app som använder Azure Files volym | Microsoft Docs
description: Lär dig hur du distribuerar ett program som använder Azure Files-volymen för att Service Fabric nät med hjälp av Azure CLI.
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
ms.openlocfilehash: fb9740efaa9a1033d6602180f5750f6fb550c048
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076496"
---
# <a name="deploy-a-service-fabric-mesh-application-that-uses-the-azure-files-volume"></a>Distribuera ett Service Fabric-nät program som använder Azure Files-volym
Det här exemplet visar användningen av lagringsvolymer i en behållare i Azure Service Fabric-nät. Som en del av det här exemplet:

- Skapa en filresurs med [Azure Files](/azure/storage/files/storage-files-introduction) 
- Referens som delar som en volym för en behållarinstans som vi kommer att distribuera
  - När behållaren börjar monterar den den resursen som en specifik plats i behållaren
- Den kod som körs i behållaren skriver en textfil till den platsen
- Kontrollera att filen har skrivits korrekt i resursen som säkerhetskopierar volymen

## <a name="example-json-templates"></a>Exempel-JSON-mallar

Linux: [https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.linux.json](https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.linux.json)

Windows: [https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.windows.json](https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.windows.json)

## <a name="create-the-azure-files-file-share"></a>Skapa en filresurs i Azure Files

Följ instruktionerna i den [dokumentation för Azure Files](/azure/storage/files/storage-how-to-create-file-share) att skapa en filresurs för att programmet ska använda.

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurera Service Fabric nät CLI
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra dessa steg. Om du väljer att installera och använda CLI lokalt måste du installera Azure CLI version 2.0.35 eller senare. Kör `az --version` för att hitta versionen. Om du vill installera eller uppgradera till den senaste versionen av CLI, se [installera Azure CLI 2.0] [azure-cli-installationen].

Installera modulen för Azure Service Fabric nät CLI-tillägg. För förhandsversionen av skrivs Azure Service Fabric nät CLI som ett tillägg till Azure CLI.

```azurecli-interactive
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure och Ställ in din prenumeration.

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-resource-group"></a>Skapa resursgrupp
Skapa en resursgrupp (RG) för att distribuera det här exemplet eller du kan använda en befintlig resursgrupp och hoppa över detta steg. Förhandsgranskningen är endast tillgängliga i `eastus` plats.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus
```

## <a name="deploy-the-template"></a>Distribuera mallen
Skapa program och relaterade resurser med hjälp av något av följande kommandon.

För Linux:

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.linux.json
```

För Windows:

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.windows.json
```

Följ anvisningarna för att ange namn på filresurs, kontonamnet och kontonyckeln för Azure-filresursen som innehåller volymen. I en minut eller så vill returnera med `"provisioningState": "Succeeded"`.

Parametern password i mallen är av `string` typ för enkel användning. Det ska visas på skärmen i klartext och distributionens status.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Kontrollera att programmet ska kunna använda volym
Programmet skapar en fil med namnet _data.txt_ i filresursen (om den inte redan finns). Innehållet i den här filen är ett tal som ökas med 30 sekunders mellanrum av programmet. Kontrollera att exemplet fungerar genom att öppna den _data.txt_ med jämna mellanrum och kontrollera att antalet håller på att uppdateras.

Filen laddas ned med ett verktyg som gör att en filresurs i Azure Files-surfning. Den [Microsoft Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/) är ett exempel på sådant verktyg.

## <a name="next-steps"></a>Nästa steg

- Visa Azure Files volym exempelprogrammet på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/azurefiles-volume).
- Läs mer om Service Fabric-Resursmodell i [nät Resursmodell i Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Om du vill veta mer om Service Fabric-nät kan läsa den [Service Fabric-nät översikt](service-fabric-mesh-overview.md).
