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
ms.date: 06/05/2018
ms.author: jlian
ms.openlocfilehash: df66ba1ec2c855a24731387210b0127892f5796f
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234792"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programmässigt skapa prenumerationer Azure Enterprise (förhandsversion)

Som en Azure-kund på [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), kan du skapa EA (MS-AZR - 0017 P) och prenumerationer för utveckling och testning i EA (MS-AZR - 0148 P) programmässigt. Lär dig hur du skapar prenumerationer via programmering med hjälp av Azure Resource Manager i den här artikeln.

När du skapar en Azure-prenumeration från den här API: et styrs den prenumerationen av avtal som du har köpt Microsoft Azure-tjänster från Microsoft eller en auktoriserad återförsäljare. Läs mer i [juridisk Information för Microsoft Azure](https://azure.microsoft.com/support/legal/).

## <a name="prerequisites"></a>Förutsättningar

* Ditt konto måste vara en Kontoägaren i en Azure EA-registrering. Om inte, be administratören registrering [lägga till dig som Kontoägare med hjälp av portalen EA](https://ea.azure.com/helpdocs/addNewAccount) (logga in på krävs). Följ anvisningarna i e-postinbjudan felmeddelandet för att manuellt skapa en första prenumeration. Bekräfta konton och manuellt skapa en första EA-prenumeration innan du fortsätter till nästa steg. Bara att lägga till kontot för registrering av räcker inte.

* Om du vill använda ett huvudnamn för tjänsten för att skapa EA prenumerationen måste du [bevilja som tjänstens huvudnamn möjlighet att skapa prenumerationer](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Konton som du har åtkomst till

När du har lagt till en Azure EA-registrering som Kontoägare använder Azure relationen konto för registrering för att avgöra var att debitera prenumeration. Alla prenumerationer som skapats under kontot debiteras mot EA-registrering som kontot finns i. Om du vill skapa prenumerationer måste du skicka in värden om kontot för registrering och användaren säkerhetsobjekt att äga prenumerationen. 

För att köra följande kommandon, måste du vara inloggad till Kontoägare *hemkatalog*, vilket är den katalog som prenumerationer skapas som standard.

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

I följande exempel skapas en begäran om att skapa prenumeration med namnet *Dev Team prenumeration* och prenumerationserbjudande *MS-AZR - 0017P* (reguljära EA). Konto för registrering är `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (platshållare värdet, det här värdet är ett GUID), vilket är kontot för registrering för SignUpEngineering@contoso.com. Även läggs två användare som RBAC ägare för prenumerationen.

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
| `EnrollmentAccountObjectId`      | Ja       | Sträng | Objekt-ID för kontot registrering att prenumerationen skapas och debiteras. Det här värdet är ett GUID som hämtas från `Get-AzureRmEnrollmentAccount`. |
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
| `enrollment-account-object-id`      | Ja       | Sträng | Objekt-ID för kontot registrering att prenumerationen skapas och debiteras. Det här värdet är ett GUID som hämtas från `az billing enrollment-account list`. |
| `owner-object-id`      | Nej       | Sträng | Objekt-ID för alla användare som du vill lägga till som ägare RBAC på prenumerationen när den skapas.  |
| `owner-upn`    | Nej       | Sträng | E-postadress för alla användare som du vill lägga till som ägare RBAC på prenumerationen när den skapas. Du kan använda den här parametern i stället för `owner-object-id`.|
| `owner-spn` | Nej       | Sträng | Program-ID för alla huvudnamn för tjänsten som du vill lägga till som ägare RBAC på prenumerationen när den skapas. Du kan använda den här parametern i stället för `owner-object-id`.| 

En fullständig lista över alla parametrar finns [az kontot skapa](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Begränsningar i Azure Enterprise prenumerationen skapas API

- Azure Enterprise-prenumerationer kan skapas med den här API: et.
- Det finns en gräns på 50 prenumerationer per konto. Efter det kan prenumerationer endast skapas med hjälp av Kontocenter.
- Det måste finnas minst en EA eller EA utveckling och testning prenumerationer under kontot, vilket innebär att Kontoägaren har gått igenom manuell registrering minst en gång.
- Användare som inte är ägare konto men som har lagts till i ett konto för registrering via RBAC, det går inte att skapa prenumerationer med hjälp av Kontocenter.
- Du kan inte välja klientorganisationen för prenumerationen ska skapas i. Prenumerationen skapas alltid i hemmet klienten till Kontoägaren. Om du vill flytta prenumerationen till en annan klient, finns [ändra prenumerationen klienten](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Nästa steg

* Ett exempel på hur du skapar prenumerationer med hjälp av .NET finns [exempelkoden på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Nu när du har skapat en prenumeration kan bevilja du den möjligheten till andra användare och tjänstens huvudnamn. Mer information finns i [bevilja åtkomst till skapa prenumerationer Azure Enterprise (förhandsversion)](grant-access-to-create-subscription.md).
* Mer information om hur du hanterar många prenumerationer med hjälp av Hanteringsgrupper finns [ordna dina resurser med Azure-Hanteringsgrupper](management-groups-overview.md)
