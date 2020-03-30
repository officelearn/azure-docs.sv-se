---
title: Lägga till eller ta bort rolltilldelningar med Azure RBAC och Azure CLI
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänsthuvudnamn eller hanterade identiteter med hjälp av Azure Role-based Access Control (RBAC) och Azure CLI.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b32df50715d5e7276861e0696df1bd6ceb3f684e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245673"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Lägga till eller ta bort rolltilldelningar med Azure RBAC och Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]I den här artikeln beskrivs hur du tilldelar roller med Azure CLI.

## <a name="prerequisites"></a>Krav

Om du vill lägga till eller ta bort rolltilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write`och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)
- [Bash i Azure Cloud Shell](/azure/cloud-shell/overview) eller Azure [CLI](/cli/azure)

## <a name="get-object-ids"></a>Hämta objekt-ID:er

Om du vill lägga till eller ta bort rolltilldelningar kan du behöva ange ett objekts unika ID. ID:et har `11111111-1111-1111-1111-111111111111`formatet: . Du kan hämta ID:t med Azure-portalen eller Azure CLI.

### <a name="user"></a>Användare

Om du vill hämta objekt-ID:t för en Azure AD-användare kan du använda [az ad user show](/cli/azure/ad/user#az-ad-user-show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Grupp

Om du vill hämta objekt-ID:t för en Azure AD-grupp kan du använda [az ad group show](/cli/azure/ad/group#az-ad-group-show) eller az ad group [list](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Program

Om du vill hämta objekt-ID:t för ett Azure AD-tjänsthuvudnamn (identitet som används av ett program) kan du använda [az ad sp-listan](/cli/azure/ad/sp#az-ad-sp-list). För ett tjänsthuvudnamn använder du objekt-ID:t och **inte** program-ID: et.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

I RBAC, för att bevilja åtkomst, lägger du till en rolltilldelning.

### <a name="user-at-a-resource-group-scope"></a>Användare i ett resursgruppomfång

Om du vill lägga till en rolltilldelning för en användare i en resursgruppsomfångst använder du [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

I följande exempel tilldelas rollen *Deltagare i virtuell dator* till *patlong\@contoso.com* användare i resursgruppen för *pharma-sales:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Använda det unika roll-ID:et

Det finns ett par gånger när ett rollnamn kan ändras, till exempel:

- Du använder din egen anpassade roll och du bestämmer dig för att ändra namnet.
- Du använder en förhandsgranskningsroll som har **(Förhandsgranska)** i namnet. När rollen släpps får rollen ett nytt namn.

> [!IMPORTANT]
> En förhandsversion tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Även om en roll har bytt namn ändras inte roll-ID:et. Om du använder skript eller automatisering för att skapa dina rolltilldelningar är det bäst att använda det unika roll-ID:t i stället för rollnamnet. Om en roll har bytt namn är det därför mer sannolikt att skripten fungerar.

Om du vill lägga till en rolltilldelning med hjälp av det unika roll-ID:t i stället för rollnamnet använder du [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

I följande exempel tilldelas rollen [Deltagare i virtuell dator](built-in-roles.md#virtual-machine-contributor) till den *patlong\@contoso.com* användaren i resursgruppen för *pharma-sales.* För att få det unika roll-ID:t kan du använda [azure role definition list](/cli/azure/role/definition#az-role-definition-list) eller se [Inbyggda roller för Azure-resurser](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Grupp i ett prenumerationsomfång

Om du vill lägga till en rolltilldelning för en grupp använder du [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Information om hur du hämtar objekt-ID:et för gruppen finns i [Hämta objekt-ID: er](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

I följande exempel tilldelas *rollen Läsare* till gruppen Ann *Mack Team* med ID 2222222-2222-2222-2222-2222222222222222222222 i ett prenumerationsomfång.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Gruppera i ett resursomfattning

Om du vill lägga till en rolltilldelning för en grupp använder du [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Information om hur du hämtar objekt-ID:et för gruppen finns i [Hämta objekt-ID: er](#get-object-ids).

I följande exempel tilldelas rollen *Deltagare i virtuell dator* till gruppen Ann Mack *Team* med ID 2222222-2222-2222-2222-2222222222222222 på ett resursomfattning för ett virtuellt nätverk med namnet *pharma-sales-project-network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Program på ett resursgruppomfång

Om du vill lägga till en rolltilldelning för ett program använder du [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Information om hur du hämtar programmets objekt-ID finns i [Hämta objekt-ID: er](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

I följande exempel tilldelas rollen *Deltagare i virtuell dator* till ett program med objekt-ID 4444444-4444-4444-4444-44444444444444444444 på resursgruppen för *läkemedelsförsäljning.*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Användare i ett prenumerationsomfattning

Om du vill lägga till en rolltilldelning för en användare i ett prenumerationsomfattning använder du [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). För att få prenumerations-ID:t hittar du det på **prenumerationsbladet** i Azure-portalen eller så kan du använda [az-kontolistan](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

I följande exempel tilldelas *rollen Läsare* till *\@annm-example.com* användare i ett prenumerationsomfång.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Användare i en hanteringsgruppomfattning

Om du vill lägga till en rolltilldelning för en användare i en hanteringsgruppomfång använder du [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Om du vill hämta hanteringsgrupp-ID:t hittar du det på bladet **Hanteringsgrupper** i Azure-portalen eller så kan du använda [listan över az-kontohanteringsgrupper](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

I följande exempel tilldelas rollen *Faktureringsläsare* till *\@alain example.com* användare i en hanteringsgruppomfång.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Nytt huvudman för tjänsten

Om du skapar ett nytt tjänsthuvudnamn och omedelbart försöker tilldela en roll till tjänstens huvudnamn, kan den rolltilldelningen misslyckas i vissa fall. Om du till exempel använder ett skript för att skapa en ny hanterad identitet och sedan försöker tilldela en roll till tjänstens huvudnamn, kan rolltilldelningen misslyckas. Orsaken till detta fel är sannolikt en replikeringsfördröjning. Tjänstens huvudnamn skapas i en region. Rolltilldelningen kan dock förekomma i en annan region som inte har replikerat tjänstens huvudnamn ännu. Om du vill åtgärda det här scenariot bör du ange huvudtypen när du skapar rolltilldelningen.

Om du vill lägga till en rolltilldelning `--assignee-object-id`använder du `--assignee-principal-type` [az-rolltilldelningsskapande](/cli/azure/role/assignment#az-role-assignment-create), anger ett värde för och anger sedan . `ServicePrincipal`

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

I följande exempel tilldelas rollen *Deltagare i virtuell dator* till den *msi-testhanterade* identiteten i resursgruppen *för läkemedelsförsäljning:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

I RBAC, för att ta bort åtkomst, tar du bort en rolltilldelning med hjälp av [az-rolltilldelning borttagning:](/cli/azure/role/assignment#az-role-assignment-delete)

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

I följande exempel tas rolltilldelningen för deltagare i *virtuell dator* bort från den *patlong\@contoso.com* användaren i resursgruppen för *läkemedelsförsäljning:*

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

I följande exempel tas *rollen Reader* bort från gruppen Ann *Mack Team* med ID 2222222-2222-2222-2222-222222222222222222222 i ett prenumerationsomfång. Information om hur du hämtar objekt-ID:et för gruppen finns i [Hämta objekt-ID: er](#get-object-ids).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

I följande exempel tas rollen *Faktureringsläsare* bort från *\@alain-example.com* användare i hanteringsgruppens omfång. Om du vill ha id:t för hanteringsgruppen kan du använda [listan över az-kontohanteringsgrupper](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Nästa steg

- [Lista rolltilldelningar med Azure RBAC och Azure CLI](role-assignments-list-cli.md)
- [Använd Azure CLI för att hantera Azure-resurser och resursgrupper](../azure-resource-manager/cli-azure-resource-manager.md)
