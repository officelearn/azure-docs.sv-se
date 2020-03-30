---
title: Bevilja åtkomst för att skapa Azure Enterprise-prenumerationer
description: Lär dig hur du ger en användare eller tjänst huvudnamn möjligheten att programmässigt skapa Azure Enterprise-prenumerationer.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478882"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Bevilja åtkomst för att skapa Azure Enterprise-prenumerationer (förhandsversion)

Som Azure-kund på [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)kan du ge en annan användare eller tjänst huvudbehörighet behörighet att skapa prenumerationer faktureras ditt konto. I den här artikeln får du lära dig hur du använder [rollbaserad åtkomstkontroll (RBAC)](../../active-directory/role-based-access-control-configure.md) för att dela möjligheten att skapa prenumerationer och hur du granskar skapande av prenumerationer. Du måste ha rollen Ägare på det konto du vill dela.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Bevilja åtkomst

Om du vill [skapa prenumerationer under ett registreringskonto](programmatically-create-subscription.md)måste användarna ha [rollen RBAC-ägare](../../role-based-access-control/built-in-roles.md#owner) på det kontot. Du kan ge en användare eller en grupp användare rollen RBAC-ägare på ett registreringskonto genom att följa dessa steg:

1. Hämta objekt-ID:t för det registreringskonto som du vill bevilja åtkomst till

    Om du vill ge andra rollen RBAC-ägare på ett registreringskonto måste du antingen vara kontoägare eller RBAC-ägare för kontot.

    # <a name="rest"></a>[Resten](#tab/rest)

    Begär att få lista alla registreringskonton som du har åtkomst till:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure svarar med en lista över alla registreringskonton som du har åtkomst till:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    Använd `principalName` egenskapen för att identifiera det konto som du vill ge RBAC-ägare åtkomst till. Kopiera `name` kontots konto. Om du till exempel vill ge RBAC-ägare åtkomst till SignUpEngineering@contoso.com ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```registreringskontot kopierar du . Det här är objekt-ID:t för registreringskontot. Klistra in det här värdet någonstans så att `enrollmentAccountObjectId`du kan använda det i nästa steg som .

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell)

    Använd cmdleten [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) för att lista alla registreringskonton som du har åtkomst till. Välj **Prova det** för att öppna Azure Cloud [Shell](https://shell.azure.com/). Om du vill klistra in koden högerklickar du på skalfönstren och väljer **Klistra in**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure svarar med en lista över registreringskonton som du har åtkomst till:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Använd `principalName` egenskapen för att identifiera det konto som du vill ge RBAC-ägare åtkomst till. Kopiera `ObjectId` kontots konto. Om du till exempel vill ge RBAC-ägare åtkomst till SignUpEngineering@contoso.com ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```registreringskontot kopierar du . Klistra in det här objekt-ID:t någonstans så `enrollmentAccountObjectId`att du kan använda det i nästa steg som .

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    Använd kommandot [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) för att lista alla registreringskonton som du har åtkomst till. Välj **Prova det** för att öppna Azure Cloud [Shell](https://shell.azure.com/). Om du vill klistra in koden högerklickar du på skalfönstren och väljer **Klistra in**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure svarar med en lista över registreringskonton som du har åtkomst till:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    Använd `principalName` egenskapen för att identifiera det konto som du vill ge RBAC-ägare åtkomst till. Kopiera `name` kontots konto. Om du till exempel vill ge RBAC-ägare åtkomst till SignUpEngineering@contoso.com ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```registreringskontot kopierar du . Det här är objekt-ID:t för registreringskontot. Klistra in det här värdet någonstans så att `enrollmentAccountObjectId`du kan använda det i nästa steg som .

1. <a id="userObjectId"></a>Hämta objekt-ID för den användare eller grupp som du vill ge rollen RBAC-ägare till

    1. Sök på Azure Active Directory i **Azure-portalen**.
    1. Om du vill ge en användare åtkomst klickar du på **Användare** i menyn till vänster. Om du vill bevilja åtkomst till en grupp klickar du på **Grupper**.
    1. Välj den användare eller grupp som du vill ge rollen RBAC-ägare till.
    1. Om du har valt en användare hittar du objekt-ID:t på profilsidan. Om du har markerat en grupp finns objekt-ID:et på sidan Översikt. Kopiera **ObjectID** genom att klicka på ikonen till höger om textrutan. Klistra in den här någonstans så att `userObjectId`du kan använda den i nästa steg som .

1. Ge användaren eller gruppen rollen RBAC-ägare på registreringskontot

    Med hjälp av de värden som du samlat in i de två första stegen ger du användaren eller gruppen rollen RBAC-ägare på registreringskontot.

    # <a name="rest"></a>[Resten](#tab/rest-2)

    Kör följande kommando ```<enrollmentAccountObjectId>``` och `name` ersätt med det du```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```kopierade i det första steget ( ). Ersätt ```<userObjectId>``` med det objekt-ID som du kopierade från det andra steget.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    När ägarrollen har tilldelats i omfånget för registreringskontot svarar Azure med information om rolltilldelningen:

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

    # <a name="powershell"></a>[Powershell](#tab/azure-powershell-2)

    Kör följande [budet ny-AzRoleAssignment](../../active-directory/role-based-access-control-manage-access-powershell.md) och ersätt med ```<enrollmentAccountObjectId>``` det `ObjectId` insamlade steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ersätt ```<userObjectId>``` med det objekt-ID som samlats in i det andra steget.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    Kör följande kommando [för att skapa a-rolltilldelning](../../active-directory/role-based-access-control-manage-access-azure-cli.md) och ersätt med ```<enrollmentAccountObjectId>``` det `name` du kopierade i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ersätt ```<userObjectId>``` med det objekt-ID som samlats in i det andra steget.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    När en användare blir EN RBAC Ägare för din registrering redovisa, de kan [programmatiskt skapa abonemang](programmatically-create-subscription.md) under den. En prenumeration som skapats av en delegerad användare har fortfarande den ursprungliga kontoägaren som tjänstadministratör, men den har också den delegerade användaren som RBAC-ägare som standard.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Granska vem som skapade prenumerationer med hjälp av aktivitetsloggar

Om du vill spåra prenumerationer som skapats via det här API:et använder du [API:et för klientaktivitetsloggen](/rest/api/monitor/tenantactivitylogs). Det är för närvarande inte möjligt att använda PowerShell, CLI eller Azure-portalen för att spåra skapande av prenumerationer.

1. Som innehavaradministratör av Azure AD-klienten kan du [höja åtkomsten](../../active-directory/role-based-access-control-tenant-admin-access.md) och sedan tilldela en läsarroll till granskningsanvändaren inom omfånget `/providers/microsoft.insights/eventtypes/management`.
1. Som granskaranvändare anropar du [klientaktivitetsloggens API](/rest/api/monitor/tenantactivitylogs) för att se aktiviteter för att skapa prenumerationer. Exempel:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Du kan anropa det här API:et från kommandoraden med hjälp av [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Nästa steg

* Nu när användaren eller tjänstens huvudnamn har behörighet att skapa en prenumeration kan du använda den identiteten för att [programmässigt skapa Azure Enterprise-prenumerationer](programmatically-create-subscription.md).
* Ett exempel på hur du skapar prenumerationer med .NET finns [i exempelkod på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Mer information om Azure Resource Manager och dess API:er finns i [översikt över Azure Resource Manager](overview.md).
* Mer information om hur du hanterar ett stort antal prenumerationer med hjälp av hanteringsgrupper finns i [Ordna dina resurser med Azure-hanteringsgrupper](../../governance/management-groups/overview.md)
* Information om hur du ser en omfattande vägledning om bästa praxis för stora organisationer när det gäller prenumerationsstyrning finns i [Azure enterprise scaffold – föreskrivande prenumerationsstyrning](/azure/architecture/cloud-adoption-guide/subscription-governance)
