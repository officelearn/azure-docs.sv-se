---
title: Skapa Azure Enterprise-prenumerationer programmässigt | Microsoft Docs
description: Lär dig hur du skapar ytterligare Azure Enterprise- eller Enterprise Dev/Test-prenumerationer programmässigt.
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
ms.openlocfilehash: 2bfa9944d85fde65ad8dbd73ddda11fa405df2f8
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358373"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programmässigt skapa Azure Enterprise-prenumerationer (förhandsversion)

Azure-kund på [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), kan du skapa EA (MS-AZR - 0017 P) och prenumerationer för utveckling/testning i EA (MS-AZR - 0148 P) programmässigt. I den här artikeln får du lära dig hur du skapar prenumerationer via programmering med hjälp av Azure Resource Manager.

När du skapar en Azure-prenumeration från detta API kan regleras den prenumerationen av avtalet som du anskaffade Microsoft Azure-tjänster från Microsoft eller en auktoriserad återförsäljare. Mer information finns i [juridisk Information om Microsoft Azure](https://azure.microsoft.com/support/legal/).

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en rollen som Prenumerationsägare eller deltagare för registrering-kontot som du vill skapa prenumerationer under. Det finns två sätt att få dessa roller:

* Din administratör för registreringen kan [se du Kontoägare](https://ea.azure.com/helpdocs/addNewAccount) (inloggning krävs) som gör dig till ägare till Registreringskontot. Följ instruktionerna i e-postinbjudan felmeddelandet för att manuellt skapa en första prenumeration. Bekräfta konton och manuellt skapa en inledande EA-prenumeration innan du fortsätter till nästa steg. Bara att lägga till kontot till registreringen räcker inte.

* Befintliga ägare till Registreringskontot kan [bevilja dig åtkomst](grant-access-to-create-subscription.md). På samma sätt, om du vill använda ett huvudnamn för tjänsten för att skapa EA-prenumerationen måste du [ge tjänstobjektet möjlighet att skapa prenumerationer](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Hitta konton som du har åtkomst till

När du har lagt till en Azure EA-registrering kontots ägare kan använder Azure kontot till registreringen relationen för att avgöra var att debitera prenumeration. Alla prenumerationer som skapats under kontot debiteras mot EA-avtal som kontot tillhör. Om du vill skapa prenumerationer, måste du skicka in värden om registreringskontot och användarens huvudnamn till äger prenumerationen. 

För att köra följande kommandon, måste du vara inloggad på den Kontoägare *hemkatalog*, vilket är den katalog som prenumerationer skapas som standard.

# <a name="resttabrest"></a>[REST](#tab/rest)

Begäran att lista alla registreringskonton:

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Använd den [Get-AzureRmEnrollmentAccount-kommandot](/powershell/module/azurerm.billing/get-azurermenrollmentaccount) för att lista alla registreringskonton du har åtkomst till.

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Azure svarar med en lista med objekt-ID och e-postadresser av konton.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd den [az fakturering konto för enhetsregistreringshanterare lista](https://aka.ms/EASubCreationPublicPreviewCLI) kommando för att lista alla registreringskonton som du har åtkomst till.

```azurecli-interactive 
az billing enrollment-account list
```

Azure svarar med en lista med objekt-ID och e-postadresser av konton.

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

Använd den `principalName` egenskapen att identifiera det konto som du vill prenumerationer att debiteras till. Använd den `id` som den `enrollmentAccount` värde som används för att skapa prenumerationen i nästa steg.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Skapa prenumerationer under en viss registreringskontot 

I följande exempel skapas en begäran om att skapa prenumeration med namnet *Dev-teamet prenumeration* och erbjudandet är *MS-AZR - 0017P* (regelbundna EA). Konto för enhetsregistreringshanterare är `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (platshållarvärdet, det här värdet är ett GUID), vilket är registreringskontot för SignUpEngineering@contoso.com. Det även lägger till två användare som ägare av RBAC för prenumerationen.

# <a name="resttabrest"></a>[REST](#tab/rest)

Använd den `id` av den `enrollmentAccount` i sökvägen för begäran om att skapa prenumerationen.

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
| `displayName` | Nej      | Sträng | Visningsnamnet för prenumerationen. Om inte anges, är den inställd på namnet på erbjudandet, som ”Microsoft Azure Enterprise”.                                 |
| `offerType`   | Ja      | Sträng | Erbjudande för prenumerationen. Två alternativ för EA är [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktion) och [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (utveckling/testning, måste vara [aktiverat EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nej       | Sträng | Objekt-ID för alla användare som du vill lägga till som ägare RBAC för prenumerationen när den skapas.  |

I svaret, få tillbaka en `subscriptionOperation` objekt för övervakning. När prenumerationen datafabriken har skapats i `subscriptionOperation` objektet kommer att returnera en `subscriptionLink` objekt som har prenumerations-ID.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

För att använda den här förhandsversionsmodulen måste du installera det genom att köra `Install-Module AzureRM.Subscription -AllowPrerelease` första. Kontrollera `-AllowPrerelease` fungerar, installera en ny version av PowerShellGet från [hämta PowerShellGet-modul](/powershell/gallery/installing-psget).

Använd den [New-AzureRmSubscription](/powershell/module/azurerm.subscription) tillsammans med `enrollmentAccount` objekt-ID som den `EnrollmentAccountObjectId` parametern för att skapa en ny prenumeration. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Elementnamn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nej      | Sträng | Visningsnamnet för prenumerationen. Om inte anges, är den inställd på namnet på erbjudandet, som ”Microsoft Azure Enterprise”.                                 |
| `OfferType`   | Ja      | Sträng | Erbjudande för prenumerationen. Två alternativ för EA är [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktion) och [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (utveckling/testning, måste vara [aktiverat EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ja       | Sträng | Objekt-ID för registreringskontot att prenumerationen har skapats under och faktureras till. Det här värdet är ett GUID som du får från `Get-AzureRmEnrollmentAccount`. |
| `OwnerObjectId`      | Nej       | Sträng | Objekt-ID för alla användare som du vill lägga till som ägare RBAC för prenumerationen när den skapas.  |
| `OwnerSignInName`    | Nej       | Sträng | E-postadress för alla användare som du vill lägga till som ägare RBAC för prenumerationen när den skapas. Du kan använda den här parametern i stället för `OwnerObjectId`.|
| `OwnerApplicationId` | Nej       | Sträng | Program-ID för alla huvudnamn för tjänsten som du vill lägga till som ägare RBAC för prenumerationen när den skapas. Du kan använda den här parametern i stället för `OwnerObjectId`.| 

En fullständig lista över alla parametrar finns i [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

För att använda det här preview-tillägget måste du installera det genom att köra `az extension add --name subscription` första.

Använd den [az konto skapa](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) tillsammans med `enrollmentAccount` objekt-ID som den `enrollment-account-object-id` parametern för att skapa en ny prenumeration.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elementnamn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nej      | Sträng | Visningsnamnet för prenumerationen. Om inte anges, är den inställd på namnet på erbjudandet, som ”Microsoft Azure Enterprise”.                                 |
| `offer-type`   | Ja      | Sträng | Erbjudande för prenumerationen. Två alternativ för EA är [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktion) och [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (utveckling/testning, måste vara [aktiverat EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ja       | Sträng | Objekt-ID för registreringskontot att prenumerationen har skapats under och faktureras till. Det här värdet är ett GUID som du får från `az billing enrollment-account list`. |
| `owner-object-id`      | Nej       | Sträng | Objekt-ID för alla användare som du vill lägga till som ägare RBAC för prenumerationen när den skapas.  |
| `owner-upn`    | Nej       | Sträng | E-postadress för alla användare som du vill lägga till som ägare RBAC för prenumerationen när den skapas. Du kan använda den här parametern i stället för `owner-object-id`.|
| `owner-spn` | Nej       | Sträng | Program-ID för alla huvudnamn för tjänsten som du vill lägga till som ägare RBAC för prenumerationen när den skapas. Du kan använda den här parametern i stället för `owner-object-id`.| 

En fullständig lista över alla parametrar finns i [az konto skapa](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Begränsningar i Azure Enterprise-prenumeration har skapats API

- Azure Enterprise-prenumerationer kan skapas med den här API: et.
- Det finns en gräns på 50 prenumerationer per konto. Efter det kan du bara skapa prenumerationer med hjälp av Kontocenter.
- Det måste finnas minst en EA eller utveckling/testning i EA-prenumerationer under kontot, vilket innebär att ägare har gått igenom manuell registrering minst en gång.
- Användare som inte är kontoinnehavare, men har lagts till i ett konto för registrering via RBAC, det går inte att skapa prenumerationer med hjälp av Kontocenter.
- Du kan inte välja klientorganisationen för prenumerationen som ska skapas i. Prenumerationen skapas alltid i startklientorganisation till Kontoägaren. Om du vill flytta prenumerationen till en annan klient Se [ändra prenumerationen klient](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Nästa steg

* Ett exempel om hur du skapar prenumerationer med hjälp av .NET finns i [exempelkoden på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Nu när du har skapat en prenumeration kan ge du den möjligheten till andra användare och tjänstens huvudnamn. Mer information finns i [bevilja behörighet att skapa Azure Enterprise-prenumerationer (förhandsversion)](grant-access-to-create-subscription.md).
* Mer information om hur du hanterar stora mängder prenumerationer med hanteringsgrupper finns [organisera dina resurser med Azure-hanteringsgrupper](management-groups-overview.md)
