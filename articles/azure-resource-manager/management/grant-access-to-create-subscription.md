---
title: Bevilja åtkomst för att skapa Azure Enterprise-prenumerationer
description: Lär dig hur du ger användare eller tjänstens huvud namn möjlighet att program mässigt skapa Azure Enterprise-prenumerationer.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: aef9c6781c87ff4e84e46de711308319755e4630
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042079"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Bevilja åtkomst för att skapa Azure Enterprise-prenumerationer (för hands version)

Som Azure-kund på [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)kan du ge en annan användare eller tjänst huvud behörighet för att skapa prenumerationer som debiteras ditt konto. I den här artikeln får du lära dig hur du använder [Azures rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) för att dela möjligheten att skapa prenumerationer och hur du granskar skapande av prenumerationer. Du måste ha ägar rollen för det konto som du vill dela.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Bevilja åtkomst

För att kunna [skapa prenumerationer under ett registrerings konto](programmatically-create-subscription.md)måste användarna ha [rollen RBAC-ägare](../../role-based-access-control/built-in-roles.md#owner) på det kontot. Du kan ge en användare eller en grupp av användare rollen RBAC-ägare på ett registrerings konto genom att följa dessa steg:

1. Hämta objekt-ID för det registrerings konto som du vill bevilja åtkomst till

    Om du vill ge andra rollen RBAC-ägare på ett registrerings konto måste du antingen vara konto ägare eller en RBAC-ägare av kontot.

    # <a name="rest"></a>[REST](#tab/rest)

    Begäran om att lista alla registrerings konton som du har åtkomst till:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure svarar med en lista över alla registrerings konton som du har åtkomst till:

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

    Använd `principalName` egenskapen för att identifiera det konto som du vill ge RBAC-ägare åtkomst till. Kopiera `name` kontot för det kontot. Om du till exempel vill ge RBAC-ägare åtkomst till SignUpEngineering@contoso.com registrerings kontot kopierar du ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Detta är objekt-ID: t för registrerings kontot. Klistra in det här värdet någonstans så att du kan använda det i nästa steg som `enrollmentAccountObjectId` .

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Använd cmdleten [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) för att visa en lista över alla registrerings konton som du har åtkomst till. Välj **försök** att öppna [Azure Cloud Shell](https://shell.azure.com/). Om du vill klistra in koden högerklickar du på Shell-Fönstren och väljer **Klistra in**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure svarar med en lista med registrerings konton som du har åtkomst till:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Använd `principalName` egenskapen för att identifiera det konto som du vill ge RBAC-ägare åtkomst till. Kopiera `ObjectId` kontot för det kontot. Om du till exempel vill ge RBAC-ägare åtkomst till SignUpEngineering@contoso.com registrerings kontot kopierar du ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Klistra in detta objekt-ID någonstans så att du kan använda det i nästa steg som `enrollmentAccountObjectId` .

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    Använd kommandot [AZ Bill Enrollment-Account List](https://aka.ms/EASubCreationPublicPreviewCLI) för att visa en lista över alla registrerings konton som du har åtkomst till. Välj **försök** att öppna [Azure Cloud Shell](https://shell.azure.com/). Om du vill klistra in koden högerklickar du på Shell-Fönstren och väljer **Klistra in**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure svarar med en lista med registrerings konton som du har åtkomst till:

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

    Använd `principalName` egenskapen för att identifiera det konto som du vill ge RBAC-ägare åtkomst till. Kopiera `name` kontot för det kontot. Om du till exempel vill ge RBAC-ägare åtkomst till SignUpEngineering@contoso.com registrerings kontot kopierar du ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` . Detta är objekt-ID: t för registrerings kontot. Klistra in det här värdet någonstans så att du kan använda det i nästa steg som `enrollmentAccountObjectId` .

1. <a id="userObjectId"></a>Hämta objekt-ID för den användare eller grupp som du vill ge rollen RBAC-ägare att

    1. Sök på **Azure Active Directory**i Azure Portal.
    1. Om du vill ge en användare åtkomst klickar du på **användare** på menyn till vänster. Om du vill bevilja åtkomst till en grupp klickar du på **grupper**.
    1. Välj den användare eller grupp som du vill ge rollen RBAC-ägare.
    1. Om du har valt en användare hittar du objekt-ID på profil sidan. Om du har valt en grupp visas objekt-ID: t på sidan Översikt. Kopiera **ObjectID** genom att klicka på ikonen till höger om text rutan. Klistra in det någonstans så att du kan använda det i nästa steg som `userObjectId` .

1. Bevilja användaren eller gruppen rollen RBAC-ägare på registrerings kontot

    Använd de värden som du samlade in i de första två stegen och ge användaren eller gruppen rollen RBAC-ägare på registrerings kontot.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Kör följande kommando och Ersätt ```<enrollmentAccountObjectId>``` med `name` du kopierade i det första steget ( ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ). Ersätt ```<userObjectId>``` med det objekt-ID som du kopierade från det andra steget.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    När ägar rollen har tilldelats till registrerings konto omfånget svarar Azure med information om roll tilldelningen:

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

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    Kör följande [New-AzRoleAssignment-](../../role-based-access-control/role-assignments-powershell.md) kommando och Ersätt ```<enrollmentAccountObjectId>``` med det `ObjectId` insamlade i det första steget ( ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ). Ersätt ```<userObjectId>``` med det objekt-ID som samlats in i det andra steget.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    Kör kommandot för att [skapa AZ roll tilldelning](../../role-based-access-control/role-assignments-cli.md) , ersätta ```<enrollmentAccountObjectId>``` med `name` du kopierade i det första steget ( ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ). Ersätt ```<userObjectId>``` med det objekt-ID som samlats in i det andra steget.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    När en användare blir en RBAC-ägare för ditt registrerings konto kan de [skapa prenumerationer via programmering](programmatically-create-subscription.md) . En prenumeration som skapats av en delegerad användare har fortfarande den ursprungliga konto ägaren som tjänst administratör, men den har också den delegerade användaren som en RBAC-ägare som standard.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Granska vem som skapade prenumerationer med aktivitets loggar

Om du vill spåra de prenumerationer som skapats via detta API använder du [klient aktivitets logg-API: et](/rest/api/monitor/tenantactivitylogs). Det går för närvarande inte att använda PowerShell, CLI eller Azure Portal för att spåra skapande av prenumeration.

1. Som innehavaradministratör av Azure AD-klienten kan du [höja åtkomsten](../../role-based-access-control/elevate-access-global-admin.md) och sedan tilldela en läsarroll till granskningsanvändaren inom omfånget `/providers/microsoft.insights/eventtypes/management`. Den här åtkomsten är tillgänglig i rollen [läsare](../../role-based-access-control/built-in-roles.md#reader) , rollen [övervaknings deltagare](../../role-based-access-control/built-in-roles.md#monitoring-contributor) eller en [anpassad roll](../../role-based-access-control/custom-roles.md).
1. Som gransknings användare anropar du [klient aktivitets logg-API: et](/rest/api/monitor/tenantactivitylogs) för att se aktiviteter för att skapa prenumerationer. Exempel:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Du kan anropa det här API:et från kommandoraden med hjälp av [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Nästa steg

* Nu när användaren eller tjänstens huvud namn har behörighet att skapa en prenumeration kan du använda den identiteten för att [program mässigt skapa Azure Enterprise-prenumerationer](programmatically-create-subscription.md).
* Ett exempel på hur du skapar prenumerationer med hjälp av .NET finns i [exempel kod på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Mer information om Azure Resource Manager och dess API: er finns i [Azure Resource Manager översikt](overview.md).
* Mer information om hur du hanterar ett stort antal prenumerationer med hjälp av hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](../../governance/management-groups/overview.md)
* För att se en omfattande metod för bästa praxis för stora organisationer om prenumerations styrning, se [Azure Enterprise-Autogenerera – preskripte-styrning av prenumerationer](/azure/architecture/cloud-adoption-guide/subscription-governance)
