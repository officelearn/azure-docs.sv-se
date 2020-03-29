---
title: Avere vFXT lösning utan ägare - Azure
description: Lösning för att tillåta användare utan prenumerationsägare behörighet att distribuera Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 1b411fe465a67f8ea5421ac0dc93348b4e92e8ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153283"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Auktorisera icke-ägare för att distribuera Avere vFXT

Dessa instruktioner är en lösning som gör att en användare utan prenumerationsägare har behörighet att skapa ett Avere vFXT för Azure-system.

(Det rekommenderade sättet att distribuera Avere vFXT-systemet är att låta en användare med ägarbehörighet göra stegen för att skapa, vilket förklaras i [Förbereda för att skapa Avere vFXT](avere-vfxt-prereqs.md).)  

Lösningen innebär att skapa en ytterligare åtkomstroll som ger användarna tillräcklig behörighet för att installera klustret. Rollen måste skapas av en prenumerationsägare och en ägare måste tilldela den till lämpliga användare.

En [prenumerationsägare](avere-vfxt-prereqs.md) måste också godkänna användarvillkoren för avere vFXT marketplace image.

> [!IMPORTANT]
> Alla dessa steg måste vidtas av en användare med ägarbehörighet för prenumerationen som ska användas för klustret.

1. Kopiera dessa rader och spara dem i `averecreatecluster.json`en fil (till exempel ). Använd ditt prenumerations-ID i utdraget. `AssignableScopes`

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

1. Kör det här kommandot för att skapa rollen:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Exempel:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Tilldela den här rollen till den användare som ska skapa klustret:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

När du har slutfört den här processen ger rollen alla användare som tilldelats den följande behörigheter för prenumerationen:

* Skapa och konfigurera nätverksinfrastrukturen
* Skapa klusterstyrenheten
* Köra klusterskapande skript från klusterstyrenheten för att skapa klustret
