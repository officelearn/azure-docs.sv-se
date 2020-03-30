---
title: Skapa Azure-prenumerationer programmässigt
description: Lär dig hur du skapar ytterligare Azure-prenumerationer programmässigt.
author: amberbhargava
ms.topic: conceptual
ms.date: 03/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 33f1d154f438b917b79cd299e81c9078e2f2e81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460406"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Skapa Azure-prenumerationer programmässigt (förhandsversion)

Azure-kunder med ett [enterprise agreement(EA),](https://azure.microsoft.com/pricing/enterprise-agreement/) [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) eller [Microsoft Partner Agreement (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) faktureringskonto kan skapa prenumerationer programmässigt. I den här artikeln får du lära dig hur du skapar prenumerationer programmässigt med Azure Resource Manager.

När du skapar en Azure-prenumeration programmässigt styrs den prenumerationen av det avtal enligt vilket du erhöll Azure-tjänster från Microsoft eller en auktoriserad återförsäljare. Mer information finns i [Microsoft Azure Legal Information](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Skapa prenumerationer för ett EA-faktureringskonto

Använd informationen i följande avsnitt för att skapa EA-prenumerationer.

### <a name="prerequisites"></a>Krav

Du måste ha en ägarroll på ett registreringskonto för att kunna skapa en prenumeration. Det finns två sätt att få rollen:

* Företagsadministratören för din registrering kan [göra dig till kontoägare](https://ea.azure.com/helpdocs/addNewAccount) (inloggning krävs) vilket gör dig till ägare av registreringskontot.

* En befintlig ägare av registreringskontot kan [ge dig åtkomst](grant-access-to-create-subscription.md). Om du vill använda ett tjänsthuvudnamn för att skapa en EA-prenumeration måste du på samma sätt [ge tjänsten huvudnamn möjlighet att skapa prenumerationer](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Hitta konton som du har tillgång till

När du har lagts till i ett registreringskonto som är kopplat till en kontoägare använder Azure relationen för konto till registrering för att avgöra var prenumerationsavgifterna ska faktureras. Alla prenumerationer som skapas under kontot faktureras till EA-registreringen som kontot finns i. Om du vill skapa prenumerationer måste du skicka in värden om registreringskontot och användarens huvudnamn för att äga prenumerationen.

Om du vill köra följande kommandon måste du vara inloggad på kontoägarens *arbetskatalog*, som är den katalog som prenumerationer skapas i som standard.

### <a name="rest"></a>[Resten](#tab/rest)

Begär att få lista alla registreringskonton som du har åtkomst till:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

API-svaret visar alla registreringskonton som du har åtkomst till:

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

Använd `principalName` egenskapen för att identifiera det konto som du vill att prenumerationer ska faktureras. Kopiera `name` kontots konto. Om du till exempel vill skapa SignUpEngineering@contoso.com prenumerationer under registreringskontot kopierar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```du . Den här identifieraren är objekt-ID för registreringskontot. Klistra in det här värdet någonstans så att `enrollmentAccountObjectId`du kan använda det i nästa steg som .

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Öppna [Azure Cloud Shell](https://shell.azure.com/) och välj PowerShell.

Använd cmdleten [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) för att lista alla registreringskonton som du har åtkomst till.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure svarar med en lista över registreringskonton som du har åtkomst till:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Använd `principalName` egenskapen för att identifiera det konto som du vill att prenumerationer ska faktureras. Kopiera `ObjectId` kontots konto. Om du till exempel vill skapa SignUpEngineering@contoso.com prenumerationer under registreringskontot kopierar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```du . Klistra in det här objekt-ID:t någonstans så `enrollmentAccountObjectId`att du kan använda det i nästa steg som .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) för att lista alla registreringskonton som du har åtkomst till.

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

Använd `principalName` egenskapen för att identifiera det konto som du vill att prenumerationer ska faktureras. Kopiera `name` kontots konto. Om du till exempel vill skapa SignUpEngineering@contoso.com prenumerationer under registreringskontot kopierar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```du . Den här identifieraren är objekt-ID för registreringskontot. Klistra in det här värdet någonstans så att `enrollmentAccountObjectId`du kan använda det i nästa steg som .

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Skapa prenumerationer under ett specifikt registreringskonto

I följande exempel skapas en prenumeration med namnet *Dev Team-prenumeration* i det registreringskonto som valdes i föregående steg. Prenumerationserbjudandet är *MS-AZR-0017P* (vanligt Microsoft Enterprise-avtal). Det lägger också till två användare som RBAC-ägare för prenumerationen.

### <a name="rest"></a>[Resten](#tab/rest)

Kör följande förfrågan och ersätt `<enrollmentAccountObjectId>` med värdet för `name` som du kopierade i första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Om du vill ange ägare kan du läsa om hur du [hämtar användarobjekt-ID: er](grant-access-to-create-subscription.md#userObjectId).

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

| Elementnamn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Inga      | String | Prenumerationens visningsnamn. Om det inte anges är det inställt på namnet på erbjudandet, till exempel "Microsoft Azure Enterprise".                                 |
| `offerType`   | Ja      | String | Erbjudandet om abonnemanget. De två alternativen för EA är [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktionsanvändning) och [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, måste [aktiveras med EA-portalen).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `owners`      | Inga       | String | Objekt-ID för alla användare som du vill lägga till som RBAC-ägare i prenumerationen när den skapas.  |

I svaret får du `subscriptionOperation` tillbaka ett objekt för övervakning. När skapandet av prenumerationen `subscriptionOperation` är klar `subscriptionLink` returnerar objektet ett objekt som har prenumerations-ID.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Installera först den här `Install-Module Az.Subscription -AllowPrerelease`förhandsversionen genom att köra . Installera en ny version av PowerShellGet från [Get PowerShellGet Module](/powershell/scripting/gallery/installing-psget). `-AllowPrerelease`

Kör kommandot [New-AzSubscription](/powershell/module/az.subscription) nedan `<enrollmentAccountObjectId>` och `ObjectId` ersätt med de```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```insamlade i det första steget ( ). Om du vill ange ägare kan du läsa om hur du [hämtar användarobjekt-ID: er](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Elementnamn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Inga      | String | Prenumerationens visningsnamn. Om det inte anges är det inställt på namnet på erbjudandet, till exempel "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Ja      | String | Erbjudandet om abonnemanget. De två alternativen för EA är [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktionsanvändning) och [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, måste [aktiveras med EA-portalen).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `EnrollmentAccountObjectId`      | Ja       | String | Objekt-ID för registreringskontot som prenumerationen skapas under och faktureras till. Det här värdet är ett `Get-AzEnrollmentAccount`GUID som du får från . |
| `OwnerObjectId`      | Inga       | String | Objekt-ID för alla användare som du vill lägga till som RBAC-ägare i prenumerationen när den skapas.  |
| `OwnerSignInName`    | Inga       | String | E-postadressen till alla användare som du vill lägga till som RBAC-ägare i prenumerationen när den skapas. Du kan använda den `OwnerObjectId`här parametern i stället för .|
| `OwnerApplicationId` | Inga       | String | Program-ID:n för alla tjänsthuvudnamn som du vill lägga till som RBAC-ägare i prenumerationen när den skapas. Du kan använda den `OwnerObjectId`här parametern i stället för . När du använder den här parametern måste tjänstens huvudnamn ha [läsbehörighet till katalogen](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

En fullständig lista över alla parametrar finns i [Ny-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Installera först det här `az extension add --name subscription`förhandsgranskningstillägget genom att köra .

Kör kommandot [az account](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) create `<enrollmentAccountObjectId>` nedan `name` och ersätt med det```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```du kopierade i det första steget ( ). Om du vill ange ägare kan du läsa om hur du [hämtar användarobjekt-ID: er](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elementnamn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Inga      | String | Prenumerationens visningsnamn. Om det inte anges är det inställt på namnet på erbjudandet, till exempel "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Ja      | String | Erbjudandet om abonnemanget. De två alternativen för EA är [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktionsanvändning) och [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, måste [aktiveras med EA-portalen).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `enrollment-account-object-id`      | Ja       | String | Objekt-ID för registreringskontot som prenumerationen skapas under och faktureras till. Det här värdet är ett `az billing enrollment-account list`GUID som du får från . |
| `owner-object-id`      | Inga       | String | Objekt-ID för alla användare som du vill lägga till som RBAC-ägare i prenumerationen när den skapas.  |
| `owner-upn`    | Inga       | String | E-postadressen till alla användare som du vill lägga till som RBAC-ägare i prenumerationen när den skapas. Du kan använda den `owner-object-id`här parametern i stället för .|
| `owner-spn` | Inga       | String | Program-ID:n för alla tjänsthuvudnamn som du vill lägga till som RBAC-ägare i prenumerationen när den skapas. Du kan använda den `owner-object-id`här parametern i stället för . När du använder den här parametern måste tjänstens huvudnamn ha [läsbehörighet till katalogen](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

En fullständig lista över alla parametrar finns i [az-konto skapa](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Begränsningar för API för att skapa Azure Enterprise-prenumerationer

- Endast Azure Enterprise-prenumerationer kan skapas med det här API:et.
- Det finns en gräns på 500 prenumerationer per registreringskonto. Därefter kan fler prenumerationer för kontot bara skapas i Azure-portalen. Om du vill skapa fler prenumerationer via API:et skapar du ett annat registreringskonto.
- Användare som inte är kontoägare, men som har lagts till i ett registreringskonto via RBAC, kan inte skapa prenumerationer i Azure-portalen.
- Du kan inte välja klienten för prenumerationen som ska skapas i. Prenumerationen skapas alltid i kontoägarens hemklient. Information om hur du flyttar prenumerationen till en annan klient finns i [ändra prenumerationsklient .](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)


## <a name="create-subscriptions-for-an-mca-account"></a>Skapa prenumerationer för ett MCA-konto

### <a name="prerequisites"></a>Krav

Du måste ha en ägar-, deltagar- eller Azure-prenumerationsskapare roll på ett fakturaavsnitt eller en ägar- eller deltagarroll i en faktureringsprofil eller ett faktureringskonto för att kunna skapa prenumerationer. Mer information finns i [Roller och uppgifter för prenumerationsfakturering](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks).

I exemplet nedan används REST-API:er. PowerShell och Azure CLI stöds inte för närvarande.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Hitta faktureringskonton som du har åtkomst till

Gör begäran nedan för att lista alla faktureringskonton.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API-svaret visar de faktureringskonton som du har åtkomst till.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Använd `displayName` egenskapen för att identifiera det faktureringskonto som du vill skapa prenumerationer på. Se till att godkännertypen av kontot är *MicrosoftCustomerAgreement*. Kopiera `name` kontots konto.  Om du till exempel vill skapa `Contoso` en prenumeration för faktureringskontot kopierar `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`du . Klistra in det här värdet någonstans så att du kan använda det i nästa steg.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Söka efter fakturaavsnitt för att skapa prenumerationer

Avgifterna för din prenumeration visas på ett avsnitt i en faktureringsprofils faktura. Använd följande API för att hämta listan över fakturaavsnitt och faktureringsprofiler som du har behörighet att skapa Azure-prenumerationer på.

Kör följande förfrågan och ersätt `<billingAccountName>` med värdet för `name` som du kopierade i första steget (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
API-svaret visar alla fakturaavsnitt och deras faktureringsprofiler som du har åtkomst till för att skapa prenumerationer på:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

Använd `invoiceSectionDisplayName` egenskapen för att identifiera det fakturaavsnitt som du vill skapa prenumerationer på. Kopiera `invoiceSectionId`avsnittet `billingProfileId` och en `skuId` för faktura. Om du till exempel vill skapa `Microsoft Azure plan` en `Development` prenumeration av typen `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`för `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` fakturaavsnittet kopierar du , och `0001`. Klistra in dessa värden någonstans så att du kan använda dem i nästa steg.

### <a name="create-a-subscription-for-an-invoice-section"></a>Skapa en prenumeration för ett fakturaavsnitt

I följande exempel skapas en prenumeration med namnet *Dev Team-prenumeration* av typen *Microsoft Azure Plan* för avsnittet Utvecklingsfaktura. *Development* Prenumerationen faktureras *contoso-ekonomis faktureringsprofil* och visas i avsnittet *Utveckling* på fakturan.

Gör följande begäran `<invoiceSectionId>` och `invoiceSectionId` ersätt den med det```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```kopierade från det andra steget ( ). Du skulle behöva skicka `billingProfileId` `skuId` och kopieras från det andra steget i parametrarna för begäran för API. Om du vill ange ägare kan du läsa om hur du [hämtar användarobjekt-ID: er](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Elementnamn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ja      | String | Prenumerationens visningsnamn.|
| `billingProfileId`   | Ja      | String | ID:t för faktureringsprofilen som debiteras för prenumerationens avgifter.  |
| `skuId` | Ja      | String | Sku-ID:n som bestämmer typen av Azure-plan. |
| `owners`      | Inga       | String | Objekt-ID för alla användare eller tjänsthuvudnamn som du vill lägga till som RBAC-ägare i prenumerationen när den skapas.  |
| `costCenter` | Inga      | String | Kostnadsstället som är associerat med prenumerationen. Det dyker upp i användningen csv-filen. |
| `managementGroupId` | Inga      | String | ID:et för den ledningsgrupp som prenumerationen ska läggas till i. Mer om du vill hämta listan över hanteringsgrupper finns i [Hanteringsgrupper - Lista API](/rest/api/resources/managementgroups/list). Använd ID:t för en hanteringsgrupp från API:et. |

I svaret får du `subscriptionCreationResult` tillbaka ett objekt för övervakning. När skapandet av prenumerationen `subscriptionCreationResult` är klar `subscriptionLink` returnerar objektet ett objekt som har prenumerations-ID.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Skapa prenumerationer för ett MPA-faktureringskonto

### <a name="prerequisites"></a>Krav

Du måste ha en global administratörs- eller administratörsagentroll i organisationens molnlösningsleverantörskonto för att kunna skapa prenumeration för ditt faktureringskonto. Mer information finns i [Partnercenter - Tilldela användarroller och behörigheter](https://docs.microsoft.com/partner-center/permissions-overview).

I exemplet nedan används REST-API:er. PowerShell och Azure CLI stöds inte för närvarande.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Hitta de faktureringskonton som du har tillgång till

Gör begäran nedan för att lista alla faktureringskonton som du har åtkomst till.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API-svaret listar faktureringskontona.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Använd `displayName` egenskapen för att identifiera det faktureringskonto som du vill skapa prenumerationer på. Se till att godkännertypen för kontot är *MicrosoftPartnerAgreement*. Kopiera `name` för kontot. Om du till exempel vill skapa `Contoso` en prenumeration för faktureringskontot kopierar `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`du . Klistra in det här värdet någonstans så att du kan använda det i nästa steg.

### <a name="find-customers-that-have-azure-plans"></a>Hitta kunder som har Azure-abonnemang

Gör följande begäran `<billingAccountName>` och `name` ersätt med det kopierade från det första steget (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) för att lista alla kunder i faktureringskontot som du kan skapa Azure-prenumerationer för.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
API-svaret listar kunderna i faktureringskontot med Azure-abonnemang. Du kan skapa prenumerationer för dessa kunder.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

Använd `displayName` egenskapen för att identifiera kunden som du vill skapa prenumerationer på. Kopiera `id` för kunden. Om du till exempel vill skapa `Fabrikam toys`en prenumeration `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`för kopierar du . Klistra in det här värdet någonstans för att använda det i de efterföljande stegen.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Valfritt för indirekta leverantörer: Hämta återförsäljarna för en kund

Om du är indirekt leverantör i CSP-modellen på två nivåer kan du ange en återförsäljare när du skapar prenumerationer för kunder.

Gör följande begäran `<customerId>` och `id` ersätt med det kopierade från det andra steget (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) för att lista alla återförsäljare som är tillgängliga för en kund.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
API-svaret listar återförsäljarna för kunden:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
Använd `description` egenskapen för att identifiera återförsäljaren som ska associeras med prenumerationen. Kopiera `resellerId` för återförsäljaren. Om du till exempel `Wingtip`vill associera kopieras du `3xxxxx`. Klistra in det här värdet någonstans så att du kan använda det i nästa steg.

### <a name="create-a-subscription-for-a-customer"></a>Skapa en prenumeration för en kund

I följande exempel skapas en prenumeration med namnet *Dev Team-prenumeration* för *Fabrikam-leksaker* och *associerar Wingtip-återförsäljare* till prenumerationen. T

Gör följande begäran `<customerId>` och `id` ersätt den med det```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```kopierade från det andra steget ( ). Skicka den valfria *återförsäljaren Kopierad* från det andra steget i API:ets parametrar för begäran.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Elementnamn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ja      | String | Prenumerationens visningsnamn.|
| `skuId` | Ja      | String | Sku-ID:n för Azure-planen. Använda *0001* för prenumerationer av typen Microsoft Azure Plan |
| `resellerId`      | Inga       | String | MPN-ID för återförsäljaren som ska associeras med prenumerationen.  |

I svaret får du `subscriptionCreationResult` tillbaka ett objekt för övervakning. När skapandet av prenumerationen `subscriptionCreationResult` är klar `subscriptionLink` returnerar objektet ett objekt som har prenumerations-ID.

## <a name="next-steps"></a>Nästa steg

* Ett exempel på hur du skapar en EA-prenumeration (Enterprise Agreement) med .NET finns [i exempelkod på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Nu när du har skapat en prenumeration kan du bevilja den möjligheten till andra användare och tjänsthuvudnamn. Mer information finns i [Bevilja åtkomst för att skapa Azure Enterprise-prenumerationer (förhandsversion)](grant-access-to-create-subscription.md).
* Mer information om hur du hanterar ett stort antal prenumerationer med hjälp av hanteringsgrupper finns i [Ordna dina resurser med Azure-hanteringsgrupper](../../governance/management-groups/overview.md)
