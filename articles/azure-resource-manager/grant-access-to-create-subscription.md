---
title: Bevilja behörighet att skapa Azure-företagsprenumerationer | Microsoft Docs
description: Lär dig att ge en användare eller tjänstens huvudnamn möjlighet att skapa Azure Enterprise prenumerationer via programmering.
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: jlian
ms.openlocfilehash: 4c5d505f431ef684b73adc04629464883d336a5b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238289"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Bevilja behörighet att skapa prenumerationer Azure Enterprise (förhandsversion)

Som en Azure-kund på [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), du kan ge en annan användare eller tjänst huvudnamn behörighet att skapa prenumerationer debiteras till ditt konto. I den här artikeln får du lära dig hur du använder [rollbaserad åtkomstkontroll (RBAC)](../active-directory/role-based-access-control-configure.md) att dela möjlighet att skapa prenumerationer och hur du granskar skapande av prenumerationen.

Om du vill skapa en prenumeration finns [programmässigt skapa prenumerationer Azure Enterprise (förhandsversion)](programmatically-create-subscription.md).

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegera åtkomst till ett konto för registrering med RBAC

Att ge en annan användare eller tjänstens huvudnamn möjlighet att skapa prenumerationer mot ett visst konto [ge dem en RBAC ägarrollen vid registrering kontots omfattning](../active-directory/role-based-access-control-manage-access-rest.md). I följande exempel ger en användare i klienten med `principalId` av `<userObjectId>` (för SignUpEngineering@contoso.com) en ägare roll för kontot registrering. Du hittar registrering konto-ID och ägar-ID [programmässigt skapa prenumerationer Azure Enterprise (förhandsversion)](programmatically-create-subscription.md).

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```
När rollen som ägare har tilldelats definitionsområdet registrering konto, Azure svarar med information för rolltilldelningen:

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Använd den [ny AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) att ge en annan användare ägare åtkomst till ditt konto för registrering.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd den [az rolltilldelning skapa](../active-directory/role-based-access-control-manage-access-azure-cli.md) att ge en annan användare ägare åtkomst till ditt konto för registrering.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

När en användare blir en RBAC ägare för ditt konto för registrering, kan de skapa prenumerationer under den programmässigt. En prenumeration som skapats av delegerade användare har fortfarande ursprungliga ägare som tjänstadministratör, men den har även delegerade användare som ägare som standard. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Granska som skapade prenumerationer med hjälp av aktivitetsloggar

Om du vill spåra prenumerationer som skapats via detta API använder den [klient aktivitet loggen API](/rest/api/monitor/tenantactivitylogs). Det går för närvarande inte att använda PowerShell, CLI eller Azure-portalen för att spåra prenumeration.

1. Som administratör innehavare av Azure AD-klient [utöka behörighet](../active-directory/role-based-access-control-tenant-admin-access.md) sedan tilldela en läsarroll granskning användaren definitionsområdet `/providers/microsoft.insights/eventtypes/management`.
1. Som användare granskning anropa den [klient aktivitet loggen API](/rest/api/monitor/tenantactivitylogs) prenumeration skapa aktiviteter. Exempel:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> För att enkelt anropa denna API från kommandoraden, försök [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Nästa steg

* Nu när användaren eller tjänstens huvudnamn har behörighet att skapa en prenumeration, kan du använda identitet till [programmässigt skapa prenumerationer Azure Enterprise](programmatically-create-subscription.md).
* Ett exempel på hur du skapar prenumerationer med hjälp av .NET finns [exempelkoden på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Läs mer om Azure Resource Manager och dess API: er i [översikt över Azure Resource Manager](resource-group-overview.md).
* Mer information om hur du hanterar många prenumerationer med hjälp av Hanteringsgrupper finns [ordna dina resurser med Azure-Hanteringsgrupper](management-groups-overview.md)
* En omfattande bästa praxis riktlinjer för stora organisationer på prenumerationen styrning finns [Azure enterprise kodskelett - normativ prenumeration styrning](/azure/architecture/cloud-adoption-guide/subscription-governance)
