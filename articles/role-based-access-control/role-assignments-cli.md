---
title: Lägga till eller ta bort roll tilldelningar med Azure RBAC och Azure CLI
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av rollbaserad åtkomst kontroll (RBAC) och Azure CLI i Azure.
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
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472000"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Lägga till eller ta bort roll tilldelningar med Azure RBAC och Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] den här artikeln beskriver hur du tilldelar roller med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

Om du vill lägga till eller ta bort roll tilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write`-och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)
- [Bash i Azure Cloud Shell](/azure/cloud-shell/overview) eller [Azure CLI](/cli/azure)

## <a name="get-object-ids"></a>Hämta objekt-ID: n

Om du vill lägga till eller ta bort roll tilldelningar kan du behöva ange ett unikt ID för ett objekt. ID: t har formatet: `11111111-1111-1111-1111-111111111111`. Du kan hämta ID: t med hjälp av Azure Portal eller Azure CLI.

### <a name="user"></a>Användare

För att hämta objekt-ID för en Azure AD-användare kan du använda [AZ AD User show](/cli/azure/ad/user#az-ad-user-show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Grupp

För att hämta objekt-ID för en Azure AD-grupp kan du använda [AZ AD Group show](/cli/azure/ad/group#az-ad-group-show) eller [AZ AD Group List](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Program

För att hämta objekt-ID för ett Azure AD-tjänstens huvud namn (identitet som används av ett program) kan du använda [AZ AD SP-lista](/cli/azure/ad/sp#az-ad-sp-list). För ett huvud namn för tjänsten använder du objekt-ID: t och **inte** program-ID: t.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Lägg till en roll tilldelning

I RBAC för att bevilja åtkomst lägger du till en roll tilldelning.

### <a name="user-at-a-resource-group-scope"></a>Användare vid en resurs grupps omfång

Om du vill lägga till en roll tilldelning för en användare i ett resurs grupps omfång använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

I följande exempel tilldelas rollen *virtuell dator deltagare* till *patlong\@contoso.com* -användare i resurs grupps omfånget *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Använda det unika roll-ID: t

Det finns ett par gånger när ett roll namn kan ändras, till exempel:

- Du använder din egen anpassade roll och du bestämmer dig för att ändra namnet.
- Du använder en förhands gransknings roll som har **(förhands granskning)** i namnet. När rollen släpps får rollen ett nytt namn.

> [!IMPORTANT]
> En för hands version tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Även om en roll får ett nytt namn ändras inte roll-ID: t. Om du använder skript eller automatisering för att skapa roll tilldelningar, är det en bra idé att använda det unika roll-ID: t i stället för roll namnet. Därför är skripten mer sannolika om du byter namn på en roll.

Om du vill lägga till en roll tilldelning med det unika roll-ID: t i stället för roll namnet använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

I följande exempel tilldelas rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) till *patlong\@contoso.com* -användare i resurs grupps omfånget *Pharma-Sales* . Om du vill hämta det unika roll-ID: t kan du använda [AZ roll definitions lista](/cli/azure/role/definition#az-role-definition-list) eller se [inbyggda roller för Azure-resurser](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Gruppera i ett prenumerations omfång

Om du vill lägga till en roll tilldelning för en grupp använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Information om hur du hämtar objekt-ID för gruppen finns i [Hämta objekt](#get-object-ids)-ID: n.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

I följande exempel tilldelas rollen *Reader* till den *Ann Mack team* gruppen med ID 22222222-2222-2222-2222-222222222222 i ett prenumerations omfång.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Gruppera i ett resurs omfång

Om du vill lägga till en roll tilldelning för en grupp använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Information om hur du hämtar objekt-ID för gruppen finns i [Hämta objekt](#get-object-ids)-ID: n.

I följande exempel tilldelas rollen *virtuell dator deltagare* till den *Ann Mack team* gruppen med ID 22222222-2222-2222-2222-222222222222 i ett resurs omfång för ett virtuellt nätverk med namnet *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Program i ett resurs grupps omfång

Om du vill lägga till en roll tilldelning för ett program använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Information om hur du hämtar objekt-ID för programmet finns i [Hämta objekt](#get-object-ids)-ID: n.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

I följande exempel tilldelas rollen *virtuell dator deltagare* till ett program med objekt-ID 44444444-4444-4444-4444-444444444444 i resurs grupps omfånget *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Användare i ett prenumerations område

Om du vill lägga till en roll tilldelning för en användare i ett prenumerations omfång använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Om du vill hämta prenumerations-ID: t kan du hitta det på bladet **prenumerationer** i Azure Portal eller så kan du använda [konto listan AZ](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

I följande exempel tilldelas rollen *läsare* rollen *annm\@example.com* -användare i ett prenumerations omfång.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Användare i en hanterings grupps omfattning

Om du vill lägga till en roll tilldelning för en användare i en hanterings grupps omfattning använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Om du vill hämta ID för hanterings grupp kan du hitta det på bladet **hanterings grupper** i Azure Portal eller så kan du använda [AZ Account Management-Group List](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

*I följande exempel tilldelas rollen* *Alain\@example.com* användare i ett hanterings grupps omfång.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Nytt huvud namn för tjänsten

Om du skapar ett nytt huvud namn för tjänsten och sedan omedelbart försöker tilldela en roll till tjänstens huvud namn kan roll tilldelningen inte utföras i vissa fall. Om du till exempel använder ett skript för att skapa en ny hanterad identitet och sedan försöker tilldela en roll till tjänstens huvud namn, kan roll tilldelningen Miss förvänta. Orsaken till det här felet är förmodligen en fördröjning i replikeringen. Tjänstens huvud namn skapas i en region. roll tilldelningen kan dock inträffa i en annan region som ännu inte har replikerat tjänstens huvud namn. För att åtgärda det här scenariot bör du ange typ av huvud konto när du skapar roll tilldelningen.

Om du vill lägga till en roll tilldelning använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create), anger ett värde för `--assignee-object-id`och anger `--assignee-principal-type` till `ServicePrincipal`.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

I följande exempel tilldelas rollen *virtuell dator deltagare* till *MSI-test* -hanterad identitet i resurs grupps omfånget *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

I RBAC för att ta bort åtkomst tar du bort en roll tilldelning genom att använda [AZ roll tilldelning ta bort](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

I följande exempel tas roll tilldelningen för *virtuell dator deltagare* bort från *patlong\@contoso.com* -användaren på resurs gruppen *Pharma-Sales* :

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

I följande exempel tas rollen *läsare* bort från *Ann Mack-team* gruppen med ID 22222222-2222-2222-2222-222222222222 i ett prenumerations omfång. Information om hur du hämtar objekt-ID för gruppen finns i [Hämta objekt](#get-object-ids)-ID: n.

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

I följande exempel tar vi bort rollen för *fakturerings läsaren* från *Alain\@example.com* -användare i hanterings gruppens omfång. Om du vill hämta ID för hanterings gruppen kan du använda [AZ Account Management-Group List](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Nästa steg

- [Lista roll tilldelningar med Azure RBAC och Azure CLI](role-assignments-list-cli.md)
- [Använd Azure CLI för att hantera Azure-resurser och resurs grupper](../azure-resource-manager/cli-azure-resource-manager.md)
