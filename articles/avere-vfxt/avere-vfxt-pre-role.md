---
title: Skapa Avere rollen med inga kontrollenhet – Avere vFXT för Azure
description: Metod för att skapa den nödvändiga RBAC-rollen utan kluster-styrenhet VM
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 547a0e0ac5254408a4064d627ec4c1e7c354a433
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634532"
---
# <a name="create-the-avere-vfxt-cluster-runtime-access-role-without-a-controller"></a>Skapa Avere vFXT runtime åtkomst klusterrollen utan en kontrollant

Det här dokumentet visar en metod för att skapa klusterrollen noden åtkomst från kommandoraden innan du skapar klustret controller VM. 

För att skapa den från klustret kontrollanten, läsa [skapa klusterrollen noden åtkomst](avere-vfxt-deploy.md#create-the-cluster-node-access-role). Controller avbildningen innehåller en prototyp roll-fil. Du kan uppdatera filen med ditt prenumerations-ID och använda den för att definiera rollen lokalt på styrenheten VM.

## <a name="create-an-azure-rbac-role"></a>Skapa en Azure RBAC-roll

Avere vFXT systemet använder [rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) för att auktorisera vFXT klusternoderna att utföra nödvändiga åtgärder.  

Som en del av normal vFXT klusteråtgärden, enskilda vFXT noder måste till exempel läsa Azure resursegenskaper, hantera lagring och styra inställningar för nätverksgränssnittet andra noder. 

Den här rollen används för vFXT noderna, inte för kluster-styrenheten VM.  

Följ dessa steg om du vill skapa rollen innan kontrollanten: 

1. Öppna Azure Cloud Shell i Azure-portalen eller bläddra till [ https://shell.azure.com ](https://shell.azure.com).

1. Använd Azure CLI-kommando för att växla till prenumerationen vFXT:

   ```az account set --subscription YOUR_SUBSCRIPTION_ID```

1. Med dessa kommandon för att hämta rolldefinitionen från marketplace-avbildning och redigera den. 

   ```bash
   wget -O- https://averedistribution.blob.core.windows.net/public/vfxtdistdoc.tgz | tar zxf - avere-cluster.json
   vi avere-cluster.json
   ``` 

4. Redigera filen för att inkludera ditt prenumerations-ID och ta bort rad ovanför den. Spara filen som ``avere-cluster.json``.

   ![Konsolen textredigerare som visar prenumerations-ID och den ”ta bort den här raden” markeras för borttagning](media/avere-vfxt-edit-role.png)

5. Skapa rollen:  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

När du skapar klustret, anger du namnet på rollen (i det här fallet `avere-cluster`). Skriptet som skapar klustret tilldelar rollen till nyligen skapade klusternoderna. 

## <a name="sample-cluster-node-runtime-role-definition"></a>Exemplet kluster noden runtime rolldefinition

> [!IMPORTANT] 
> Den här exempeldefinition hämtades från en före-GA-versionen av produkten. Om den version som du får med aktuella produktdistributionen skiljer, använder du den här versionen.

```json

{
    "AssignableScopes": [
        "/subscriptions/YOUR_SUBSCRIPTION_ID_GOES_HERE"
    ],
    "Name": "avere-cluster",
    "IsCustom": "true",
    "Description": "Avere cluster runtime role",
    "NotActions": [],
    "Actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
    ],
    "DataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
    ]
}

```