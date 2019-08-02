---
title: Skapa Azure Enterprise-prenumerationer program mässigt | Microsoft Docs
description: Lär dig hur du skapar ytterligare Azure Enterprise-eller Enterprise dev/test-prenumerationer program mässigt.
services: azure-resource-manager
author: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 701b35d99cb98009ec0116c23eaeab94ff967f51
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678940"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Skapa Azure Enterprise-prenumerationer program mässigt (för hands version)

Som Azure-kund på [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)kan du skapa EA-prenumerationer (MS-AZR-0017P) och EA dev/test (MS-AZR-0148P) program mässigt. I den här artikeln får du lära dig hur du skapar prenumerationer program mässigt med hjälp av Azure Resource Manager.

När du skapar en Azure-prenumeration från det här API: et regleras den prenumerationen enligt det avtal som du fick Microsoft Azure tjänster från Microsoft eller en auktoriserad åter försäljare. Läs mer i [Microsoft Azure juridisk information](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en ägar roll på det registrerings konto som du vill skapa prenumerationer under. Det finns två sätt att hämta dessa roller:

* Din registrerings administratör kan [göra en konto ägare](https://ea.azure.com/helpdocs/addNewAccount) (inloggning krävs) som gör att du får en ägare till registrerings kontot. Följ anvisningarna i e-postinbjudan som du får för att skapa en första prenumeration manuellt. Bekräfta konto ägarskapet och skapa en inledande EA-prenumeration manuellt innan du fortsätter till nästa steg. Det räcker bara att lägga till kontot i registreringen.

* En befintlig ägare av registrerings kontot kan [ge dig åtkomst](grant-access-to-create-subscription.md). På samma sätt måste du, om du vill använda ett huvud namn för tjänsten för att skapa en EA-prenumeration, [ge tjänsten huvud kontot möjlighet att skapa prenumerationer](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Hitta konton som du har åtkomst till

När du har lagt till en Azure EA-registrering som konto ägare använder Azure konto-till-registrerings-relationen för att avgöra var prenumerations avgifterna ska faktureras. Alla prenumerationer som skapas under kontot debiteras mot den EA-registrering som kontot finns i. Om du vill skapa prenumerationer måste du skicka in värden för registrerings kontot och användarens huvud namn för att äga prenumerationen. 

Om du vill köra följande kommandon måste du vara inloggad på konto ägarens *Hem Katalog*, som är den katalog som prenumerationer skapas i som standard.

## <a name="resttabrest"></a>[REST](#tab/rest)

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

`principalName` Använd egenskapen för att identifiera det konto som du vill debitera prenumerationer på. Kopiera kontot `name` för det kontot. Om du till exempel vill skapa prenumerationer under SignUpEngineering@contoso.com registrerings kontot kopierar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```du. Detta är objekt-ID: t för registrerings kontot. Klistra in det här värdet någonstans så att du kan använda det i nästa steg `enrollmentAccountObjectId`som.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Öppna [Azure Cloud Shell](https://shell.azure.com/) och välj PowerShell.

Använd cmdleten [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) för att visa en lista över alla registrerings konton som du har åtkomst till.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure svarar med en lista med registrerings konton som du har åtkomst till:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
`principalName` Använd egenskapen för att identifiera det konto som du vill debitera prenumerationer på. Kopiera kontot `ObjectId` för det kontot. Om du till exempel vill skapa prenumerationer under SignUpEngineering@contoso.com registrerings kontot kopierar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```du. Klistra in detta objekt-ID någonstans så att du kan använda det i nästa steg som `enrollmentAccountObjectId`.

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [AZ Bill Enrollment-Account List](https://aka.ms/EASubCreationPublicPreviewCLI) för att visa en lista över alla registrerings konton som du har åtkomst till.

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

`principalName` Använd egenskapen för att identifiera det konto som du vill debitera prenumerationer på. Kopiera kontot `name` för det kontot. Om du till exempel vill skapa prenumerationer under SignUpEngineering@contoso.com registrerings kontot kopierar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```du. Detta är objekt-ID: t för registrerings kontot. Klistra in det här värdet någonstans så att du kan använda det i nästa steg `enrollmentAccountObjectId`som.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Skapa prenumerationer under ett angivet registrerings konto

I följande exempel skapas en prenumeration med namnet *dev team-prenumeration* i det registrerings konto som valts i föregående steg. Prenumerations erbjudandet är *MS-AZR-0017P* (vanliga Microsoft Enterprise-avtal). Du kan också lägga till två användare som RBAC-ägare för prenumerationen.

# <a name="resttabrest"></a>[REST](#tab/rest)

Gör följande begäran och Ersätt `<enrollmentAccountObjectId>` med den `name` kopierade från det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Om du vill ange ägare kan du läsa mer om [hur du hämtar användar objekt-ID: n](grant-access-to-create-subscription.md#userObjectId).

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

| Element namn  | Obligatorisk | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nej      | Sträng | Visnings namnet för prenumerationen. Om inget annat anges anges namnet på erbjudandet, t. ex. "Microsoft Azure Enterprise".                                 |
| `offerType`   | Ja      | Sträng | Prenumerationens erbjudande. De två alternativen för EA är [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktions användning) och [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, måste aktive ras [med hjälp av EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nej       | Sträng | Objekt-ID för en användare som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas.  |

I svaret får du tillbaka ett `subscriptionOperation` objekt för övervakning. När prenumerationen har `subscriptionOperation` skapats returnerar objektet ett `subscriptionLink` -objekt som har prenumerations-ID: t.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Installera först den här förhands gransknings modulen genom att köra `Install-Module Az.Subscription -AllowPrerelease`. För att se `-AllowPrerelease` till att fungerar måste du installera en ny version av PowerShellGet från [Hämta PowerShellGet-modulen](/powershell/gallery/installing-psget).

Kör kommandot [New-AzSubscription](/powershell/module/az.subscription) nedan och Ersätt `<enrollmentAccountObjectId>` det med den `ObjectId` insamlade informationen i det```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```första steget (). Om du vill ange ägare kan du läsa mer om [hur du hämtar användar objekt-ID: n](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Element namn  | Obligatorisk | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nej      | Sträng | Visnings namnet för prenumerationen. Om inget annat anges anges namnet på erbjudandet, t. ex. "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Ja      | Sträng | Prenumerationens erbjudande. De två alternativen för EA är [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktions användning) och [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, måste aktive ras [med hjälp av EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ja       | Sträng | Objekt-ID för det registrerings konto som prenumerationen skapas under och faktureras till. Det här värdet är ett GUID som du får `Get-AzEnrollmentAccount`från. |
| `OwnerObjectId`      | Nej       | Sträng | Objekt-ID för en användare som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas.  |
| `OwnerSignInName`    | Nej       | Sträng | E-postadressen till en användare som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas. Du kan använda den här parametern i `OwnerObjectId`stället för.|
| `OwnerApplicationId` | Nej       | Sträng | Program-ID för alla tjänst objekt som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas. Du kan använda den här parametern i `OwnerObjectId`stället för. När du använder den här parametern måste tjänstens huvud namn ha [Läs behörighet till katalogen](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Om du vill se en fullständig lista över alla parametrar, se [New-AzSubscription](/powershell/module/az.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Installera först detta för hands versions tillägg `az extension add --name subscription`genom att köra.

Kör kommandot [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) nedan och Ersätt `<enrollmentAccountObjectId>` med det `name` du kopierade i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Om du vill ange ägare kan du läsa mer om [hur du hämtar användar objekt-ID: n](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Element namn  | Obligatorisk | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nej      | Sträng | Visnings namnet för prenumerationen. Om inget annat anges anges namnet på erbjudandet, t. ex. "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Ja      | Sträng | Prenumerationens erbjudande. De två alternativen för EA är [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktions användning) och [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, måste aktive ras [med hjälp av EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ja       | Sträng | Objekt-ID för det registrerings konto som prenumerationen skapas under och faktureras till. Det här värdet är ett GUID som du får `az billing enrollment-account list`från. |
| `owner-object-id`      | Nej       | Sträng | Objekt-ID för en användare som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas.  |
| `owner-upn`    | Nej       | Sträng | E-postadressen till en användare som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas. Du kan använda den här parametern i `owner-object-id`stället för.|
| `owner-spn` | Nej       | Sträng | Program-ID för alla tjänst objekt som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas. Du kan använda den här parametern i `owner-object-id`stället för. När du använder den här parametern måste tjänstens huvud namn ha [Läs behörighet till katalogen](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Om du vill se en fullständig lista över alla parametrar, se [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Begränsningar för Azures API för att skapa prenumerationer

- Det går bara att skapa Azure Enterprise-prenumerationer med det här API: et.
- Det finns en gräns på 200 prenumerationer per registrerings konto. Efter det kan prenumerationer bara skapas via konto Center. Skapa ett annat registrerings konto om du vill skapa fler prenumerationer via API: et.
- Användare som inte är konto ägare, men som har lagts till i ett registrerings konto via RBAC, kan inte skapa prenumerationer med hjälp av konto centret.
- Du kan inte välja klienten för den prenumeration som ska skapas i. Prenumerationen skapas alltid i konto ägarens hem klient. Information om hur du flyttar prenumerationen till en annan klient finns i [ändra prenumerations klient](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Nästa steg

* Ett exempel på hur du skapar prenumerationer med hjälp av .NET finns i [exempel kod på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Nu när du har skapat en prenumeration kan du bevilja den möjligheten till andra användare och tjänstens huvud namn. Mer information finns i [bevilja åtkomst till skapa Azure Enterprise-prenumerationer (för hands version)](grant-access-to-create-subscription.md).
* Mer information om hur du hanterar ett stort antal prenumerationer med hjälp av hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](management-groups-overview.md)
