---
title: Bevilja åtkomst till att skapa Azure Enterprise-prenumerationer | Microsoft Docs
description: Lär dig att ge en användare eller tjänstens huvudnamn möjlighet att skapa Azure Enterprise-prenumerationer programmässigt.
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 7a2397328f715dbf63246e8d4aaa789b5986b3b4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112571"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Bevilja åtkomst till att skapa Azure Enterprise-prenumerationer (förhandsversion)

Azure-kund på [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), du kan ge en annan användaren eller tjänstens huvudnamn behörighet att skapa prenumerationer faktureras till ditt konto. I den här artikeln får du lära dig hur du använder [rollbaserad åtkomstkontroll (RBAC)](../active-directory/role-based-access-control-configure.md) att dela möjligheten att skapa prenumerationer och hur du granskar prenumeration som skapats. Du måste ha rollen ägare för kontot som du vill dela.

För att skapa en prenumeration, se [programmässigt skapa Azure Enterprise-prenumerationer (förhandsversion)](programmatically-create-subscription.md).

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegera åtkomst till ett konto för enhetsregistreringshanterare med RBAC

Ge en annan användare eller tjänstens huvudnamn möjligheten att skapa prenumerationer mot ett specifikt konto [ge dem en RBAC ägarrollen definitionsområdet registreringskontot](../active-directory/role-based-access-control-manage-access-rest.md). I följande exempel ger en användare i klientorganisationen med `principalId` av `<userObjectId>` (för SignUpEngineering@contoso.com) en ägarrollen på registreringskontot. Du hittar registrering konto-ID och ägar-ID [programmässigt skapa Azure Enterprise-prenumerationer (förhandsversion)](programmatically-create-subscription.md).

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

När rollen ägare har tilldelats på kontoomfånget registrering Azure svarar med information rolltilldelningen:

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd den [New AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) att ge en annan användare ägaråtkomst till ditt konto för registrering.

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd den [az-rolltilldelning skapa](../active-directory/role-based-access-control-manage-access-azure-cli.md) att ge en annan användare ägaråtkomst till ditt konto för registrering.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

När en användare blir ägare RBAC för ditt konto för enhetsregistreringshanterare, kan de programmässigt skapa prenumerationer under den. En prenumeration som skapats av en delegerad användare har fortfarande ursprungliga ägare som tjänstadministratören, men det har även den delegerade användaren som ägare som standard. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Granska som skapade prenumerationer med hjälp av aktivitetsloggar

För att spåra de prenumerationer som skapats via den här API: et måste använda den [klient aktivitet Log API](/rest/api/monitor/tenantactivitylogs). Det är för närvarande inte möjligt att använda PowerShell, CLI eller Azure-portalen för att spåra prenumeration har skapats.

1. Som innehavaradministratör av Azure AD-klienten kan du [höja åtkomsten](../active-directory/role-based-access-control-tenant-admin-access.md) och sedan tilldela en läsarroll till granskningsanvändaren inom omfånget `/providers/microsoft.insights/eventtypes/management`.
1. Som granskning användare, anropa den [klient aktivitet Log API](/rest/api/monitor/tenantactivitylogs) att se prenumeration skapa aktiviteter. Exempel:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Du kan anropa det här API:et från kommandoraden med hjälp av [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Nästa steg

* Nu när användaren eller tjänstens huvudnamn har fått behörighet att skapa en prenumeration, kan du använda det identitet som [programmässigt skapa Azure Enterprise-prenumerationer](programmatically-create-subscription.md).
* Ett exempel om hur du skapar prenumerationer med hjälp av .NET finns i [exempelkoden på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Läs mer om Azure Resource Manager och dess API: er i [översikt över Azure Resource Manager](resource-group-overview.md).
* Mer information om hur du hanterar stora mängder prenumerationer med hanteringsgrupper finns [organisera dina resurser med Azure-hanteringsgrupper](management-groups-overview.md)
* En omfattande metodvägledning för stora organisationer på Prenumerationsåtgärder finns i [Azure enterprise scaffold - förebyggande Prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance)
