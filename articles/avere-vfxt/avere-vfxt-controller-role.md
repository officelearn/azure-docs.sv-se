---
title: Åtkomst för anpassade kontrollantroll - Avere vFXT för Azure
description: Så här skapar du en anpassad roll för kontrollanten Avere vFXT kluster
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 2a0f4a628764aaa561a5567d3435a42da804a994
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417849"
---
# <a name="customized-controller-access-role"></a>Anpassad åtkomstroll till kontrollenheten

Avere vFXT för Azure-kluster controller använder en hanterad identitet och rollbaserad åtkomstkontroll (RBAC) för att tillåta att skapa och hantera klustret. 

Kontrollanten klustret tilldelas som standard den [inbyggda ägarrollen](../role-based-access-control/built-in-roles.md#owner). Dessutom den styrenheten åtkomst är begränsad till en resursgrupp – det går inte att ändra element utanför klustrets resursgrupp.

Den här artikeln beskriver hur du skapar dina egna rollen för kluster-styrenheten istället för att använda standardinställningen. 

## <a name="edit-the-role-prototype"></a>Redigera roll-prototyp

Starta från rollen prototyp på <https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt>.

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/read",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

Lägg till prenumerations-ID för Avere vFXT för Azure-distribution i AssignableScopes-instruktionen. Anpassa namn och Lägg till eller ändra definitioner efter behov. 

Var försiktig om du begränsar behörigheter. Skapa ett kluster kan misslyckas om den inte har tillräcklig behörighet. 

För hjälp att förstå vad behörighet kontrollanten klustret behöver skapa ett kluster med [öppna ett supportärende](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt). 

Spara din anpassad rolldefinition som en JSON-fil. 

## <a name="define-the-role"></a>Definiera rollen 

Följ dessa steg om du vill lägga till anpassad rolldefinition i prenumerationen. 

1. Öppna Azure Cloud Shell i Azure-portalen eller bläddra till [ https://shell.azure.com ](https://shell.azure.com).

1. Använd Azure CLI-kommando för att växla till prenumerationen vFXT:

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. Skapa rollen:

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   Använd din filnamnet och sökvägen i stället för ```/avere-contributor-custom.json``` i det här exemplet. 

Spara utdata från kommandot rollen definition, den innehåller det roll-ID som du ska ange i mallen för skapande av kluster. 

## <a name="find-the-role-id"></a>Hitta roll-ID

Distributionsmall för Avere vFXT måste rollens globalt unik identifierare (GUID) tilldela en anpassad roll för kontrollanten. 

Rollen GUID är en 32 tecken lång sträng i det här formuläret: 8e3af657-a8ff-443c-a75c-2fe8c4bcb635

Om du vill leta upp din roll GUID, använder du det här kommandot med rollnamnet på din i den ```--name``` parametern.

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
Ange den här strängen i den **roll-ID att skapa ett kluster för Avere** fältet när du distribuerar Avere vFXT för Azure.

## <a name="next-steps"></a>Nästa steg

Läs hur du distribuerar Avere vFXT för Azure i [distribuera vFXT-kluster](avere-vfxt-deploy.md)
