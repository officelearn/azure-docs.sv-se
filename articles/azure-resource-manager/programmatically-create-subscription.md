---
title: Programmässigt skapa prenumerationer Azure Enterprise | Microsoft Docs
description: Lär dig hur du skapar ytterligare Azure Enterprise eller utveckling och testning Enterprise prenumerationer via programmering.
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
ms.date: 4/30/2018
ms.author: jlian
ms.openlocfilehash: 308202addbca447ee0dab7a55a1ad2b3b6600a10
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603329"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programmässigt skapa prenumerationer Azure Enterprise (förhandsversion)

Som en Azure-kund på [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), kan du skapa EA (MS-AZR - 0017 P) och prenumerationer för utveckling och testning i EA (MS-AZR - 0148 P) programmässigt. Om du vill ge en annan användare eller tjänstens huvudnamn behörighet att skapa prenumerationer debiteras till ditt konto, kan du ge dem [rollbaserad åtkomstkontroll (RBAC)](../active-directory/role-based-access-control-configure.md) tillgång till ditt konto för registrering. 

> [!IMPORTANT]
> Azure-prenumeration(er) som skapats via detta API regleras av avtalet som du har fått Microsoft Azure-tjänster från Microsoft eller en auktoriserad återförsäljare. Läs mer i [juridisk Information för Microsoft Azure](https://azure.microsoft.com/support/legal/).

I den här artikeln kommer du att:

> [!div class="checklist"]
> * Lär dig hur du skapar prenumerationer via programmering med hjälp av Azure Resource Manager
> * Förstå hur RBAC kan användas för att dela möjlighet att skapa prenumerationer debiteras EA-konto

Se även den [.NET exempelkod på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).

## <a name="ask-your-ea-enrollment-admin-to-add-you-as-account-owner"></a>Be administratören lägga till dig som Kontoägaren EA registrering

Börja genom att be administratören registrering [lägga till dig som Kontoägare i med hjälp av portalen EA](https://ea.azure.com/helpdocs/addNewAccount) (logga in på krävs). Följ anvisningarna i e-postinbjudan felmeddelandet för att manuellt skapa en första prenumeration.

> [!IMPORTANT]
> Du måste bekräfta konton och manuellt skapa en första EA-prenumeration innan du fortsätter till nästa steg. Bara att lägga till kontot till registrering är inte tillräckligt.

## <a name="find-accounts-you-have-access-to"></a>Konton som du har åtkomst till

När du har lagt till en Azure EA-registrering som Kontoägare använder Azure relationen konto för registrering för att avgöra var att debitera prenumeration. Om du vill skapa prenumerationer först ta reda på vilka konton för registrering av du har åtkomst till. Om du för närvarande är en EA Kontoägare och försök att använda den här API: et, kontrollerar Azure för följande villkor:

- Ditt konto har lagts till en EA-registrering
- Du har en eller flera EA eller EA utveckling och testning prenumerationer, vilket innebär att du har gått igenom manuell registrering minst en gång
- Du är inloggad i den Kontoägaren *hemkatalog*, vilket är den katalog som prenumerationer skapas som standard

Om ovanstående tre villkor uppfylls, en `enrollmentAccount` resurs returneras och du kan börja skapa prenumerationer under det kontot. Alla prenumerationer som skapats under kontot debiteras mot EA-registrering som kontot finns i.

# <a name="resttabrest"></a>[REST](#tab/rest)

Begär för att lista alla konton för registrering:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure svarar med en lista över alla registrering konton som du har åtkomst till:

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Använd den [kommandot Get-AzureRmEnrollmentAccount](/powershell/module/azurerm.billing/get-azurermenrollmentaccount) lista över alla konton för registrering av du har åtkomst till.

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Azure svarar med en lista med objekt-ID och e-postadresser för konton.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Använd den [az fakturering registrering-listan över](https://aka.ms/EASubCreationPublicPreviewCLI) kommando för att lista alla registrering konton som du har åtkomst till.

```azurecli-interactive 
az billing enrollment-account list
```
Azure svarar med en lista med objekt-ID och e-postadresser för konton.

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
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

Använd den `principalName` egenskapen för att identifiera det konto som du vill prenumerationer att debiteras. Använd den `id` som den `enrollmentAccount` värde som används för att skapa prenumerationen i nästa steg.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Skapa prenumerationer under ett konto för registrering av specifika 

I följande exempel skapas en begäran om att skapa prenumeration med namnet *Dev Team prenumeration* och prenumerationserbjudande *MS-AZR - 0017P* (reguljära EA). Konto för registrering är `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (platshållarvärde, detta är ett GUID), som är registrering konto för SignUpEngineering@contoso.com. Även läggs två användare som RBAC ägare för prenumerationen.

# <a name="resttabrest"></a>[REST](#tab/rest)

Använd den `id` av den `enrollmentAccount` i sökvägen för en begäran att skapa prenumerationen.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Elementnamn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nej      | Sträng | Visningsnamnet för prenumerationen. Om inget anges anges till namnet på erbjudanden som ”Microsoft Azure Enterprise”.                                 |
| `offerType`   | Ja      | Sträng | Erbjudande för prenumerationen. De två alternativen för EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktion) och [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (utveckling och testning, måste vara [aktiverat EA-portal](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nej       | Sträng | Objekt-ID för alla användare som du vill lägga till som ägare RBAC på prenumerationen när den skapas.  |

I svaret, få tillbaka en `subscriptionOperation` objekt för övervakning. När prenumerationen skapas är klar visas den `subscriptionOperation` objekt returneras en `subscriptionLink` -objekt som har prenumerations-ID.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill använda den här preview-modulen, installerar du den genom att köra `Install-Module AzureRM.Subscription -AllowPrerelease` första. Kontrollera `-AllowPrerelease` fungerar, installera den senaste versionen av PowerShellGet från [Hämtningsmodul PowerShellGet](/powershell/gallery/installing-psget).

Använd den [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) tillsammans med `enrollmentAccount` objekt-ID som den `EnrollmentAccountObjectId` parametern för att skapa en ny prenumeration. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Elementnamn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nej      | Sträng | Visningsnamnet för prenumerationen. Om inget anges anges till namnet på erbjudanden som ”Microsoft Azure Enterprise”.                                 |
| `OfferType`   | Ja      | Sträng | Erbjudande för prenumerationen. De två alternativen för EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktion) och [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (utveckling och testning, måste vara [aktiverat EA-portal](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ja       | Sträng | Objekt-ID för kontot registrering att prenumerationen skapas och debiteras. Detta är ett GUID-värde som du får från `Get-AzureRmEnrollmentAccount`. |
| `OwnerObjectId`      | Nej       | Sträng | Objekt-ID för alla användare som du vill lägga till som ägare RBAC på prenumerationen när den skapas.  |
| `OwnerSignInName`    | Nej       | Sträng | E-postadress för alla användare som du vill lägga till som ägare RBAC på prenumerationen när den skapas. Du kan använda den här parametern i stället för `OwnerObjectId`.|
| `OwnerApplicationId` | Nej       | Sträng | Program-ID för alla huvudnamn för tjänsten som du vill lägga till som ägare RBAC på prenumerationen när den skapas. Du kan använda den här parametern i stället för `OwnerObjectId`.| 

En fullständig lista över alla parametrar finns [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill använda den här preview-tillägget, installerar du den genom att köra `az extension add --name subscription` första.

Använd den [az kontot skapa](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) tillsammans med `enrollmentAccount` objekt-ID som den `enrollment-account-object-id` parametern för att skapa en ny prenumeration.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elementnamn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nej      | Sträng | Visningsnamnet för prenumerationen. Om inget anges anges till namnet på erbjudanden som ”Microsoft Azure Enterprise”.                                 |
| `offer-type`   | Ja      | Sträng | Erbjudande för prenumerationen. De två alternativen för EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktion) och [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (utveckling och testning, måste vara [aktiverat EA-portal](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ja       | Sträng | Objekt-ID för kontot registrering att prenumerationen skapas och debiteras. Detta är ett GUID-värde som du får från `az billing enrollment-account list`. |
| `owner-object-id`      | Nej       | Sträng | Objekt-ID för alla användare som du vill lägga till som ägare RBAC på prenumerationen när den skapas.  |
| `owner-upn`    | Nej       | Sträng | E-postadress för alla användare som du vill lägga till som ägare RBAC på prenumerationen när den skapas. Du kan använda den här parametern i stället för `owner-object-id`.|
| `owner-spn` | Nej       | Sträng | Program-ID för alla huvudnamn för tjänsten som du vill lägga till som ägare RBAC på prenumerationen när den skapas. Du kan använda den här parametern i stället för `owner-object-id`.| 

En fullständig lista över alla parametrar finns [az kontot skapa](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegera åtkomst till ett konto för registrering med RBAC

Att ge en annan användare eller tjänstens huvudnamn möjlighet att skapa prenumerationer mot ett visst konto [ge dem en RBAC ägarrollen vid registrering kontots omfattning](../active-directory/role-based-access-control-manage-access-rest.md). I följande exempel ger en användare i klienten med `principalId` av `<userObjectId>` (för SignUpEngineering@contoso.com) en ägare roll för kontot registrering. 

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

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Begränsningar i Azure Enterprise prenumerationen skapas API

- Azure Enterprise-prenumerationer kan skapas med den här API: et.
- Det finns en gräns på 50 prenumerationer per konto. Efter det kan prenumerationer endast skapas med hjälp av Kontocenter.
- Det måste finnas minst en EA eller EA utveckling och testning prenumerationer under kontot, vilket innebär att Kontoägaren har gått igenom manuell registrering minst en gång.
- Användare som inte är ägare konto men som har lagts till i ett konto för registrering via RBAC, det går inte att skapa prenumerationer med hjälp av Kontocenter.
- Du kan inte välja klientorganisationen för prenumerationen ska skapas i. Prenumerationen skapas alltid i hemmet klienten till Kontoägaren. Om du vill flytta prenumerationen till en annan klient, finns [ändra prenumerationen klienten](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Nästa steg

* Ett exempel på hur du skapar prenumerationer med hjälp av .NET finns [exempelkoden på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Läs mer om Azure Resource Manager och dess API: er i [översikt över Azure Resource Manager](resource-group-overview.md).
* Mer information om hur du hanterar många prenumerationer med hjälp av Hanteringsgrupper finns [ordna dina resurser med Azure-Hanteringsgrupper](management-groups-overview.md)
* En omfattande bästa praxis riktlinjer för stora organisationer på prenumerationen styrning finns [Azure enterprise kodskelett - normativ prenumeration styrning](/azure/architecture/cloud-adoption-guide/subscription-governance)
