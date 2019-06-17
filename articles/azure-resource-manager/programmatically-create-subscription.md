---
title: Skapa Azure Enterprise-prenumerationer programmässigt | Microsoft Docs
description: Lär dig hur du skapar ytterligare Azure Enterprise- eller Enterprise Dev/Test-prenumerationer programmässigt.
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
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 7985451eb2bb5e9fd4fbcfb3d2fcf35149122c15
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65796066"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programmässigt skapa Azure Enterprise-prenumerationer (förhandsversion)

Azure-kund på [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), kan du skapa EA (MS-AZR - 0017 P) och prenumerationer för utveckling/testning i EA (MS-AZR - 0148 P) programmässigt. I den här artikeln får du lära dig hur du skapar prenumerationer via programmering med hjälp av Azure Resource Manager.

När du skapar en Azure-prenumeration från detta API kan regleras den prenumerationen av avtalet som du anskaffade Microsoft Azure-tjänster från Microsoft eller en auktoriserad återförsäljare. Mer information finns i [juridisk Information om Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste ha en ägarrollen på konto för Enhetsregistreringshanterare som du vill skapa prenumerationer under. Det finns två sätt att få dessa roller:

* Din administratör för registreringen kan [se du Kontoägare](https://ea.azure.com/helpdocs/addNewAccount) (inloggning krävs) vilket gör att du äger Registreringskontot. Följ instruktionerna i e-postinbjudan felmeddelandet för att manuellt skapa en första prenumeration. Bekräfta konton och manuellt skapa en inledande EA-prenumeration innan du fortsätter till nästa steg. Bara att lägga till kontot till registreringen räcker inte.

* Befintliga ägare till Registreringskontot kan [bevilja dig åtkomst](grant-access-to-create-subscription.md). På samma sätt, om du vill använda ett huvudnamn för tjänsten för att skapa EA-prenumerationen måste du [ge tjänstobjektet möjlighet att skapa prenumerationer](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Hitta konton som du har åtkomst till

När du har lagt till en Azure EA-registrering kontots ägare kan använder Azure kontot till registreringen relationen för att avgöra var att debitera prenumeration. Alla prenumerationer som skapats under kontot debiteras mot EA-avtal som kontot tillhör. Om du vill skapa prenumerationer, måste du skicka in värden om registreringskontot och användarens huvudnamn till äger prenumerationen. 

För att köra följande kommandon, måste du vara inloggad på den Kontoägare *hemkatalog*, vilket är den katalog som prenumerationer skapas som standard.

## <a name="resttabrest"></a>[REST](#tab/rest)

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

Använd den `principalName` egenskapen att identifiera det konto som du vill prenumerationer att debiteras till. Kopiera den `name` på det kontot. Exempel: Om du vill skapa prenumerationer under den SignUpEngineering@contoso.com konto för enhetsregistreringshanterare som du vill kopiera ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Det här är objekt-ID för registreringskontot. Klistra in det här värdet någonstans så att du kan använda den i nästa steg som `enrollmentAccountObjectId`.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Öppna [Azure Cloud Shell](https://shell.azure.com/) och välj PowerShell.

Använd den [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet för att lista alla registreringskonton som du har åtkomst till.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure svarar med en lista över registreringskonton som du har åtkomst till:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Använd den `principalName` egenskapen att identifiera det konto som du vill prenumerationer att debiteras till. Kopiera den `ObjectId` på det kontot. Exempel: Om du vill skapa prenumerationer under den SignUpEngineering@contoso.com konto för enhetsregistreringshanterare som du vill kopiera ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Klistra in den här objekt-ID någonstans så att du kan använda den i nästa steg som den `enrollmentAccountObjectId`.

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd den [az fakturering konto för enhetsregistreringshanterare lista](https://aka.ms/EASubCreationPublicPreviewCLI) kommando för att lista alla registreringskonton som du har åtkomst till.

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

Använd den `principalName` egenskapen att identifiera det konto som du vill prenumerationer att debiteras till. Kopiera den `name` på det kontot. Exempel: Om du vill skapa prenumerationer under den SignUpEngineering@contoso.com konto för enhetsregistreringshanterare som du vill kopiera ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Det här är objekt-ID för registreringskontot. Klistra in det här värdet någonstans så att du kan använda den i nästa steg som `enrollmentAccountObjectId`.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Skapa prenumerationer under en viss registreringskontot

I följande exempel skapas en prenumeration med namnet *Dev-teamet prenumeration* i registreringskontot valde i föregående steg. Prenumerationen-erbjudandet är *MS-AZR - 0017P* (vanliga Microsoft Enterprise Agreement). Det även lägger till två användare som ägare av RBAC för prenumerationen.

# <a name="resttabrest"></a>[REST](#tab/rest)

Gör följande för begäran, byta ut `<enrollmentAccountObjectId>` med den `name` kopieras från det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Om du vill ange ägare kan läsa [så här hämtar du ID: N för användarobjektet](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

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

| Elementnamn  | Obligatoriskt | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nej      | String | Visningsnamnet för prenumerationen. Om inte anges, är den inställd på namnet på erbjudandet, som ”Microsoft Azure Enterprise”.                                 |
| `offerType`   | Ja      | String | Erbjudande för prenumerationen. Två alternativ för EA är [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktion) och [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (utveckling/testning, måste vara [aktiverat EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nej       | String | Objekt-ID för alla användare som du vill lägga till som ägare RBAC för prenumerationen när den skapas.  |

I svaret, få tillbaka en `subscriptionOperation` objekt för övervakning. När prenumerationen datafabriken har skapats i `subscriptionOperation` objektet kommer att returnera en `subscriptionLink` objekt som har prenumerations-ID.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Först installerar den här förhandsversionsmodulen genom att köra `Install-Module Az.Subscription -AllowPrerelease`. Kontrollera `-AllowPrerelease` fungerar, installera en ny version av PowerShellGet från [hämta PowerShellGet-modul](/powershell/gallery/installing-psget).

Kör den [New-AzSubscription](/powershell/module/az.subscription) kommandot nedan och Ersätt `<enrollmentAccountObjectId>` med den `ObjectId` som samlas in i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Om du vill ange ägare kan läsa [så här hämtar du ID: N för användarobjektet](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Elementnamn  | Obligatoriskt | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nej      | String | Visningsnamnet för prenumerationen. Om inte anges, är den inställd på namnet på erbjudandet, som ”Microsoft Azure Enterprise”.                                 |
| `OfferType`   | Ja      | String | Erbjudande för prenumerationen. Två alternativ för EA är [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktion) och [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (utveckling/testning, måste vara [aktiverat EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ja       | String | Objekt-ID för registreringskontot att prenumerationen har skapats under och faktureras till. Det här värdet är ett GUID som du får från `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Nej       | String | Objekt-ID för alla användare som du vill lägga till som ägare RBAC för prenumerationen när den skapas.  |
| `OwnerSignInName`    | Nej       | String | E-postadress för alla användare som du vill lägga till som ägare RBAC för prenumerationen när den skapas. Du kan använda den här parametern i stället för `OwnerObjectId`.|
| `OwnerApplicationId` | Nej       | String | Program-ID för alla huvudnamn för tjänsten som du vill lägga till som ägare RBAC för prenumerationen när den skapas. Du kan använda den här parametern i stället för `OwnerObjectId`. När du använder den här parametern måste tjänstens huvudnamn måste ha [läsbehörighet till katalogen](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

En fullständig lista över alla parametrar finns i [New AzSubscription](/powershell/module/az.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Installera först förhandsversion tillägget genom att köra `az extension add --name subscription`.

Kör den [az konto skapa](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) kommandot nedan och Ersätt `<enrollmentAccountObjectId>` med den `name` du kopierade i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Om du vill ange ägare kan läsa [så här hämtar du ID: N för användarobjektet](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elementnamn  | Obligatoriskt | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nej      | String | Visningsnamnet för prenumerationen. Om inte anges, är den inställd på namnet på erbjudandet, som ”Microsoft Azure Enterprise”.                                 |
| `offer-type`   | Ja      | String | Erbjudande för prenumerationen. Två alternativ för EA är [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktion) och [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (utveckling/testning, måste vara [aktiverat EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ja       | String | Objekt-ID för registreringskontot att prenumerationen har skapats under och faktureras till. Det här värdet är ett GUID som du får från `az billing enrollment-account list`. |
| `owner-object-id`      | Nej       | String | Objekt-ID för alla användare som du vill lägga till som ägare RBAC för prenumerationen när den skapas.  |
| `owner-upn`    | Nej       | String | E-postadress för alla användare som du vill lägga till som ägare RBAC för prenumerationen när den skapas. Du kan använda den här parametern i stället för `owner-object-id`.|
| `owner-spn` | Nej       | String | Program-ID för alla huvudnamn för tjänsten som du vill lägga till som ägare RBAC för prenumerationen när den skapas. Du kan använda den här parametern i stället för `owner-object-id`. När du använder den här parametern måste tjänstens huvudnamn måste ha [läsbehörighet till katalogen](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

En fullständig lista över alla parametrar finns i [az konto skapa](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Begränsningar i Azure Enterprise-prenumeration har skapats API

- Azure Enterprise-prenumerationer kan skapas med den här API: et.
- Det finns en inledande högst 50 prenumerationer per konto för enhetsregistreringshanterare, men du kan [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) att öka gränsen till 200. Efter det, går du bara skapa prenumerationer via Azure Kontocenter.
- Det måste finnas minst en EA eller utveckling/testning i EA-prenumerationer under kontot, vilket innebär att ägare har gått igenom manuell registrering minst en gång.
- Användare som inte är kontoinnehavare, men har lagts till i ett konto för registrering via RBAC, det går inte att skapa prenumerationer med hjälp av Kontocenter.
- Du kan inte välja klientorganisationen för prenumerationen som ska skapas i. Prenumerationen skapas alltid i startklientorganisation till Kontoägaren. Om du vill flytta prenumerationen till en annan klient Se [ändra prenumerationen klient](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Nästa steg

* Ett exempel om hur du skapar prenumerationer med hjälp av .NET finns i [exempelkoden på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Nu när du har skapat en prenumeration kan ge du den möjligheten till andra användare och tjänstens huvudnamn. Mer information finns i [bevilja behörighet att skapa Azure Enterprise-prenumerationer (förhandsversion)](grant-access-to-create-subscription.md).
* Mer information om hur du hanterar stora mängder prenumerationer med hanteringsgrupper finns [organisera dina resurser med Azure-hanteringsgrupper](management-groups-overview.md)
