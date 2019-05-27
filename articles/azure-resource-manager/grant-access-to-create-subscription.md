---
title: Bevilja åtkomst till att skapa Azure Enterprise-prenumerationer | Microsoft Docs
description: Lär dig att ge en användare eller tjänstens huvudnamn möjlighet att skapa Azure Enterprise-prenumerationer programmässigt.
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: 81f3edcfcffad1b9b6d0ab4b49769601196049ad
ms.sourcegitcommit: f4469b7bb1f380bf9dddaf14763b24b1b508d57c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66179844"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Bevilja åtkomst till att skapa Azure Enterprise-prenumerationer (förhandsversion)

Azure-kund på [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), du kan ge en annan användaren eller tjänstens huvudnamn behörighet att skapa prenumerationer faktureras till ditt konto. I den här artikeln får du lära dig hur du använder [rollbaserad åtkomstkontroll (RBAC)](../active-directory/role-based-access-control-configure.md) att dela möjligheten att skapa prenumerationer och hur du granskar prenumeration som skapats. Du måste ha rollen ägare för kontot som du vill dela.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Bevilja åtkomst

Att [skapa prenumerationer under ett konto för enhetsregistreringshanterare](programmatically-create-subscription.md), användarna måste ha den [RBAC ägarrollen](../role-based-access-control/built-in-roles.md#owner) på det kontot. Du kan ge en användare eller en grupp användare RBAC ägarrollen för ett konto för registrering genom att följa dessa steg:

1. Hämta objekt-ID för konto för enhetsregistreringshanterare som du vill bevilja åtkomst till

    Om du vill bevilja andra RBAC ägarrollen för ett konto för registrering, måste antingen vara ägare eller en RBAC-ägare till kontot.

    # <a name="resttabrest"></a>[REST](#tab/rest)

    Begäran att lista alla registreringskonton som du har åtkomst till:

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

    Använd den `principalName` egenskapen att identifiera det konto som du vill ge RBAC-ägaråtkomst till. Kopiera den `name` på det kontot. Exempel: Om du vill ge RBAC-ägaråtkomst till den SignUpEngineering@contoso.com konto för enhetsregistreringshanterare som du vill kopiera ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Det här är objekt-ID för registreringskontot. Klistra in det här värdet någonstans så att du kan använda den i nästa steg som `enrollmentAccountObjectId`.

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    Använd den [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet för att lista alla registreringskonton som du har åtkomst till. Välj **prova** att öppna [Azure Cloud Shell](https://shell.azure.com/). Om du vill klistra in koden, högerklickar du på windows-gränssnittet och klicka sedan **klistra in**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure svarar med en lista över registreringskonton som du har åtkomst till:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Använd den `principalName` egenskapen att identifiera det konto som du vill ge RBAC-ägaråtkomst till. Kopiera den `ObjectId` på det kontot. Exempel: Om du vill ge RBAC-ägaråtkomst till den SignUpEngineering@contoso.com konto för enhetsregistreringshanterare som du vill kopiera ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Klistra in den här objekt-ID någonstans så att du kan använda den i nästa steg som den `enrollmentAccountObjectId`.

    # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

    Använd den [az fakturering konto för enhetsregistreringshanterare lista](https://aka.ms/EASubCreationPublicPreviewCLI) kommando för att lista alla registreringskonton som du har åtkomst till. Välj **prova** att öppna [Azure Cloud Shell](https://shell.azure.com/). Om du vill klistra in koden, högerklickar du på windows-gränssnittet och klicka sedan **klistra in**.

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

    Använd den `principalName` egenskapen att identifiera det konto som du vill ge RBAC-ägaråtkomst till. Kopiera den `name` på det kontot. Exempel: Om du vill ge RBAC-ägaråtkomst till den SignUpEngineering@contoso.com konto för enhetsregistreringshanterare som du vill kopiera ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Det här är objekt-ID för registreringskontot. Klistra in det här värdet någonstans så att du kan använda den i nästa steg som `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Hämta objekt-ID för användaren eller gruppen som du vill ge rollen RBAC-ägare till

    1. Sök i Azure-portalen på **Azure Active Directory**.
    1. Om du vill ge en användaråtkomst, klickar du på **användare** i menyn till vänster. Om du vill bevilja åtkomst till en grupp klickar du på **grupper**.
    1. Välj den användare eller grupp som du vill ge rollen RBAC-ägare till.
    1. Om du har valt en användare hittar objekt-ID på profilsidan. Om du har valt en grupp ska objekt-ID vara på översiktssidan. Kopiera den **ObjectID** genom att klicka på ikonen till höger om rutan. Klistra in den här någonstans så att du kan använda den i nästa steg som `userObjectId`.

1. Bevilja användaren eller gruppen RBAC ägarrollen på konto för enhetsregistreringshanterare

    Med de värden som du samlade in i de två första stegen, användaren eller gruppen RBAC ägarrollen på registreringskontot.

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    Kör följande kommando ersätter ```<enrollmentAccountObjectId>``` med den `name` du kopierade i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ersätt ```<userObjectId>``` med objekt-ID som du kopierade från det andra steget.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

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

    # <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

    Kör följande [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) kommando, ersätta ```<enrollmentAccountObjectId>``` med den `ObjectId` som samlas in i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ersätt ```<userObjectId>``` med objektet ID har samlats in i det andra steget.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[Azure CLI](#tab/azure-cli-2)

    Kör följande [az-rolltilldelning skapa](../active-directory/role-based-access-control-manage-access-azure-cli.md) kommando, ersätta ```<enrollmentAccountObjectId>``` med den `name` du kopierade i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ersätt ```<userObjectId>``` med objektet ID har samlats in i det andra steget.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    När en användare blir ägare RBAC för ditt konto för enhetsregistreringshanterare, kan de [programmässigt skapa prenumerationer](programmatically-create-subscription.md) under den. En prenumeration som skapats av en delegerad användare har fortfarande ursprungliga ägare som tjänstadministratören, men det har även den delegerade användaren som ägare RBAC som standard.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Granska som skapade prenumerationer med hjälp av aktivitetsloggar

För att spåra de prenumerationer som skapats via den här API: et måste använda den [klient aktivitet Log API](/rest/api/monitor/tenantactivitylogs). Det är för närvarande inte möjligt att använda PowerShell, CLI eller Azure-portalen för att spåra prenumeration har skapats.

1. Som innehavaradministratör av Azure AD-klienten kan du [höja åtkomsten](../active-directory/role-based-access-control-tenant-admin-access.md) och sedan tilldela en läsarroll till granskningsanvändaren inom omfånget `/providers/microsoft.insights/eventtypes/management`.
1. Som granskning användare, anropa den [klient aktivitet Log API](/rest/api/monitor/tenantactivitylogs) att se prenumeration skapa aktiviteter. Exempel:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Du kan anropa det här API:et från kommandoraden med hjälp av [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Nästa steg

* Nu när användaren eller tjänstens huvudnamn har fått behörighet att skapa en prenumeration, kan du använda det identitet som [programmässigt skapa Azure Enterprise-prenumerationer](programmatically-create-subscription.md).
* Ett exempel om hur du skapar prenumerationer med hjälp av .NET finns i [exempelkoden på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Läs mer om Azure Resource Manager och dess API: er i [översikt över Azure Resource Manager](resource-group-overview.md).
* Mer information om hur du hanterar stora mängder prenumerationer med hanteringsgrupper finns [organisera dina resurser med Azure-hanteringsgrupper](management-groups-overview.md)
* En omfattande metodvägledning för stora organisationer på Prenumerationsåtgärder finns i [Azure enterprise scaffold - förebyggande Prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance)
