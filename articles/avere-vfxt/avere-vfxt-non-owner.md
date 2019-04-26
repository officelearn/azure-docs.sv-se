---
title: Avere vFXT icke-ägarnoder lösning – Azure
description: En lösning för att tillåta användare utan prenumeration ägarbehörighet för att distribuera Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: e72e6d969649de09389ee38b94e874fad98ee08f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409217"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Auktorisera icke-ägare för att distribuera Avere vFXT

Dessa instruktioner är en lösning som låter en användare utan prenumeration ägare behörighet för att skapa en Avere vFXT för Azure-systemet.

(Det rekommenderade sättet att distribuera Avere vFXT system är att låta en användare med ägarprivilegier gör att skapa steg som beskrivs i [förberedelser för att skapa Avere vFXT](avere-vfxt-prereqs.md).)  

Lösningen innebär att du skapar en roll för ytterligare åtkomst som ger användarna behörighet att installera klustret. Rollen måste skapas av en prenumerationsägare och en ägare måste tilldela den till rätt användare. 

En prenumerant måste också [godkänna användningsvillkoren](avere-vfxt-prereqs.md) för Avere vFXT marketplace-avbildning. 

> [!IMPORTANT] 
> Alla dessa steg vidtas av en användare med ägarprivilegier för prenumerationen som ska användas för klustret.

1. Kopiera dessa rader och spara dem i en fil (till exempel `averecreatecluster.json`). Använd ditt prenumerations-ID i den `AssignableScopes` instruktionen.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",
   
           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. Kör följande kommando för att skapa rollen:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Exempel:
    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Tilldela den här rollen till användaren som skapar klustret:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Alla användare som tilldelats den här rollen har följande behörigheter för prenumerationen efter den här proceduren: 

* Skapa och konfigurera nätverksinfrastrukturen
* Skapa kluster-kontrollant
* Kör skripten för att skapa klustret från kluster-kontrollanten för att skapa klustret
