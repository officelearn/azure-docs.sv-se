---
title: Skapa Azure-prenumerationer programmatiskt
description: Lär dig hur du skapar ytterligare Azure-prenumerationer programmatiskt.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/26/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62989c21333e53fcb58b4b637802c8b697ae970e
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371447"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Skapa Azure-prenumerationer programmatiskt (förhandsversion)

Azure-kunder som har ett faktureringskonto med [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft-kundavtal (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) or [Microsoft-partneravtal (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) kan skapa prenumerationer programmatiskt. I den här artikeln får du lära dig hur du skapar prenumerationer programmatiskt med hjälp av Azure Resource Manager.

När du skapar en Azure-prenumeration programmatiskt styrs prenumerationen av det avtal som du fick när du erhöll Azure-tjänster från Microsoft eller en auktoriserad återförsäljare. Mer information finns i [Juridisk information om Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Hantera prenumerationer för ett EA-faktureringskonto

Använd informationen i följande avsnitt för att skapa EA-prenumerationer.

### <a name="prerequisites"></a>Krav

För att kunna skapa en prenumeration måste du ha en ägarroll för ett registreringskonto. Det finns två sätt att få denna roll:

* Företagsadministratören för din registrering kan [utse dig till kontoägare](https://ea.azure.com/helpdocs/addNewAccount) (inloggning krävs), vilket innebär att du blir ägare till registreringskontot.

* En befintlig ägare till registreringskontot kan [ge dig åtkomst](grant-access-to-create-subscription.md). Om du vill skapa en EA-prenumeration med hjälp av ett tjänsthuvudnamn måste du på liknande sätt [bevilja tjänsthuvudnamnet behörighet att skapa prenumerationer](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Hitta konton som du har åtkomst till

När du har lagt till ett registreringskonto som är associerat med en kontoägare använder Azure konto-till-registrering-relationen för att ta reda på var prenumerationsavgifterna ska debiteras. Alla prenumerationer som skapats under kontot debiteras till den EA-registrering som kontot ingår i. För att kunna skapa prenumerationer måste du ange värden om registreringskontot och användarens huvudnamn så att du är ägare för prenumerationen.

För att kunna köra följande kommandon måste du vara inloggad på kontoägarens *hemkatalog*, vilket är den katalog som prenumerationer skapas i som standard.

### <a name="rest"></a>[REST](#tab/rest)

Begära en lista över alla registreringskonton som du har åtkomst till:

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

Använd egenskapen `principalName` för att identifiera det konto som du vill ska debiteras för prenumerationerna. Kopiera `name` för det kontot. Om du till exempel vill skapa prenumerationer under registreringskontot SignUpEngineering@contoso.com kopierar du ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Den här identifieraren är objekt-ID:t för registreringskontot. Klistra in det här värdet någonstans så att du kan använda det som `enrollmentAccountObjectId` i nästa steg.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Öppna [Azure Cloud Shell](https://shell.azure.com/) och välj PowerShell.

Använd cmdleten [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) för att visa en lista över alla registreringskonton som du har åtkomst till.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure skickar ett svar med en lista över de registreringskonton som du har åtkomst till:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Använd egenskapen `principalName` för att identifiera det konto som du vill ska debiteras för prenumerationerna. Kopiera `ObjectId` för det kontot. Om du till exempel vill skapa prenumerationer under registreringskontot SignUpEngineering@contoso.com kopierar du ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Klistra in det här objekt-ID:t någonstans så att du kan använda det som `enrollmentAccountObjectId` i nästa steg.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) för att visa en lista över alla registreringskonton som du har åtkomst till.

```azurecli-interactive
az billing enrollment-account list
```

Azure skickar ett svar med en lista över de registreringskonton som du har åtkomst till:

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

Använd egenskapen `principalName` för att identifiera det konto som du vill ska debiteras för prenumerationerna. Kopiera `name` för det kontot. Om du till exempel vill skapa prenumerationer under registreringskontot SignUpEngineering@contoso.com kopierar du ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Den här identifieraren är objekt-ID:t för registreringskontot. Klistra in det här värdet någonstans så att du kan använda det som `enrollmentAccountObjectId` i nästa steg.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Skapa prenumerationer under ett specifikt registreringskonto

I följande exempel skapas en prenumeration med namnet *Dev Team Subscription* i det registreringskonto som valdes i föregående steg. Prenumerationserbjudandet är *MS-AZR-0017P* (vanligt Microsoft Enterprise-avtal). Du kan även lägga till två användare som Azure RBAC-ägare för prenumerationen.

### <a name="rest"></a>[REST](#tab/rest)

Kör följande förfrågan och ersätt `<enrollmentAccountObjectId>` med värdet för `name` som du kopierade i första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Om du vill ange ägare kan du läsa om [hur du erhåller objekt:ID:n för ägare](grant-access-to-create-subscription.md#userObjectId).

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
| `displayName` | Nej      | Sträng | Prenumerationens visningsnamn. Ange namnet på erbjudandet, till exempel ”Microsoft Azure Enterprise”, om det inte redan har angetts.                                 |
| `offerType`   | Ja      | Sträng | Prenumerationens erbjudande. De två alternativen för EA är [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (användning i produktion) och [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (utveckling/testning, måste [aktiveras med EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Inga       | Sträng | Objekt-ID för användare som du vill lägga till som Azure RBAC-ägare för prenumerationen när den skapas.  |

I sidhuvudet `Location` i svaret du får finns en URL som du kan använda för att fråga efter status för skapandet av prenumerationen. När prenumerationen har skapats returnerar ett GET-anrop till `Location`-url:en ett `subscriptionLink`-objekt, som innehåller prenumerations-ID:t. Mer information finns i [dokumentationen om prenumerations-API:er](/rest/api/subscription/)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kör `Install-Module Az.Subscription` för att installera den senaste versionen av modulen som innehåller cmdleten `New-AzSubscription`. Se [Hämta PowerShellGet-modul](/powershell/scripting/gallery/installing-psget) om du vill installera en nyare version av PowerShellGet.

Kör kommandot [New-AzSubscription](/powershell/module/az.subscription) nedan och ersätt `<enrollmentAccountObjectId>` med `ObjectId` som samlades in i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Om du vill ange ägare kan du lära dig [hur du erhåller objekt:ID:n för ägare](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Elementnamn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nej      | Sträng | Prenumerationens visningsnamn. Ange namnet på erbjudandet, till exempel ”Microsoft Azure Enterprise”, om det inte redan har angetts.                                 |
| `OfferType`   | Ja      | Sträng | Prenumerationens erbjudande. De två alternativen för EA är [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (användning i produktion) och [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (utveckling/testning, måste [aktiveras med EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ja       | Sträng | Objekt-ID:t för det registreringskonto som prenumerationen skapades under och som debiteras. Det här värdet är ett GUID som du får från `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Inga       | Sträng | Objekt-ID för användare som du vill lägga till som Azure RBAC-ägare för prenumerationen när den skapas.  |
| `OwnerSignInName`    | Inga       | Sträng | E-postadress för användare som du vill lägga till som Azure RBAC-ägare för prenumerationen när den skapas. Du kan använda den här parametern i stället för `OwnerObjectId`.|
| `OwnerApplicationId` | Inga       | Sträng | Program-ID för tjänsthuvudnamn som du vill lägga till som Azure RBAC-ägare för prenumerationen när den skapas. Du kan använda den här parametern i stället för `OwnerObjectId`. När du använder den här parametern måste tjänsthuvudnamnet ha [läsbehörighet till katalogen](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Se [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription) för en fullständig lista över alla parametrar.


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Börja med att installera det här tillägget (förhandsversion) genom att köra `az extension add --name subscription`.

Kör kommandot [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) nedan och ersätt `<enrollmentAccountObjectId>` med `name` som du kopierade i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Om du vill ange ägare kan du lära dig [hur du erhåller objekt:ID:n för ägare](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elementnamn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nej      | Sträng | Prenumerationens visningsnamn. Ange namnet på erbjudandet, till exempel ”Microsoft Azure Enterprise”, om det inte redan har angetts.                                 |
| `offer-type`   | Ja      | Sträng | Prenumerationens erbjudande. De två alternativen för EA är [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (användning i produktion) och [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (utveckling/testning, måste [aktiveras med EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ja       | Sträng | Objekt-ID:t för det registreringskonto som prenumerationen skapades under och som debiteras. Det här värdet är ett GUID som du får från `az billing enrollment-account list`. |
| `owner-object-id`      | Inga       | Sträng | Objekt-ID för användare som du vill lägga till som Azure RBAC-ägare för prenumerationen när den skapas.  |
| `owner-upn`    | Inga       | Sträng | E-postadressen för användare som du vill lägga till som Azure RBAC-ägare för prenumerationen när den skapas. Du kan använda den här parametern i stället för `owner-object-id`.|
| `owner-spn` | Inga       | Sträng | Program-ID för tjänsthuvudnamn som du vill lägga till som Azure RBAC-ägare för prenumerationen när den skapas. Du kan använda den här parametern i stället för `owner-object-id`. När du använder den här parametern måste tjänsthuvudnamnet ha [läsbehörighet till katalogen](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Se [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) för en fullständig lista över alla parametrar.

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Begränsningar för API:et för att skapa Azure Enterprise-prenumerationer

- Det går bara att skapa Azure Enterprise-prenumerationer med det här API:et.
- Det finns en gräns på 2 000 prenumerationer per registreringskonto. Därefter kan du bara skapa fler prenumerationer för kontot i Azure-portalen. Skapa ett till registreringskonto om du vill skapa fler prenumerationer via API:et.
- Användare som inte är kontoägare, men som har lagts till i ett registreringskonto via Azure RBAC, kan inte skapa prenumerationer i Azure-portalen.
- Du kan inte välja den klientorganisation som prenumerationen ska skapas i. Prenumerationen skapas alltid i kontoägarens hemklientorganisation. Information om hur du flyttar prenumerationen till en annan klientorganisation finns i avsnittet om att [ändra klientorganisation för prenumerationer](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Skapa prenumerationer för ett MCA-konto

### <a name="prerequisites"></a>Krav

För att kunna skapa prenumerationer måste du ha rollen ägare, deltagare eller skapare av Azure-prenumerationen i ett fakturaavsnitt eller rollen ägare eller deltagare i en faktureringsprofil eller ett faktureringskonto. Mer information finns i [Roller och uppgifter för prenumerationsfakturering](understand-mca-roles.md#subscription-billing-roles-and-tasks).

I exemplet nedan används REST-API:er. PowerShell och Azure CLI stöds inte för närvarande.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Sök efter faktureringskonton som du har åtkomst till

Skicka begäran nedan om du vill se en lista över alla faktureringskonton.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API-svaret visar alla faktureringskonton som du har åtkomst till.

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
Använd egenskapen `displayName` för att identifiera det faktureringskonto som du vill skapa prenumerationer för. Se till att agreementType för kontot är *MicrosoftCustomerAgreement*. Kopiera `name` för kontot.  Om du till exempel vill skapa en prenumeration för `Contoso`-faktureringskontot kopierar du `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Klistra in det här värdet någonstans så att du kan använda det i nästa steg.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Hitta fakturaavsnitt för att skapa prenumerationer

Avgifterna för din prenumeration visas i ett avsnitt på fakturan för en faktureringsprofil. Använd följande API för att hämta listan över fakturaavsnitt och faktureringsprofiler som du har behörighet att skapa Azure-prenumerationer på.

Kör följande förfrågan och ersätt `<billingAccountName>` med värdet för `name` som du kopierade i första steget (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
API-svaret visar alla fakturaavsnitt och de faktureringsprofiler som du har behörighet att skapa prenumerationer på:

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

Använd egenskapen `invoiceSectionDisplayName` för att identifiera det fakturaavsnitt som du vill skapa prenumerationer för. Kopiera `invoiceSectionId`, `billingProfileId` och ett `skuId` för fakturaavsnittet. Om du till exempel vill skapa en prenumeration av typen `Microsoft Azure plan` för fakturaavsnittet `Development` kopierar du `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` och `0001`. Klistra in dessa värden någonstans så att du kan använda dem i nästa steg.

### <a name="create-a-subscription-for-an-invoice-section"></a>Skapa en prenumeration för ett fakturaavsnitt

I följande exempel skapas en prenumeration med namnet *Dev Team subscription* av typen *Microsoft Azure Plan* för fakturaavsnittet *Development*. Prenumerationen debiteras till faktureringsprofilen för *Contoso Finance* och visas på avsnittet *Development* på fakturan.

Skicka följande begäran och ersätt `<invoiceSectionId>` med `invoiceSectionId` som kopierats från det andra steget (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Du måste skicka `billingProfileId` och `skuId` som kopierades från det andra steget i parametrarna för begäran till API:et. Om du vill ange ägare kan du lära dig [hur du erhåller objekt:ID:n för ägare](grant-access-to-create-subscription.md#userObjectId).

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
| `displayName` | Ja      | Sträng | Prenumerationens visningsnamn.|
| `billingProfileId`   | Ja      | Sträng | ID:t för den faktureringsprofil som ska debiteras för prenumerationsavgifterna.  |
| `skuId` | Ja      | Sträng | Det SKU-ID som bestämmer typen av Azure-plan. |
| `owners`      | Inga       | Sträng | Objekt-ID för användare eller tjänsthuvudnamn som du vill lägga till som Azure RBAC-ägare för prenumerationen när den skapas.  |
| `costCenter` | Inga      | Sträng | Kostnadsstället som är associerat med prenumerationen. Det visas i CSV-filen för användning. |
| `managementGroupId` | Inga      | Sträng | ID:t för den hanteringsgrupp som prenumerationen ska läggas till i. Information om hur du hämtar listan med hanteringsgrupper finns i [Hanteringsgrupper – Lista – API](/rest/api/resources/managementgroups/list). Använd ID:t för en hanteringsgrupp från API:et. |

I svaret får du tillbaka ett `subscriptionCreationResult`-objekt för övervakning. När prenumerationen har skapats returnerar `subscriptionCreationResult`-objektet ett `subscriptionLink`-objekt, som har prenumerations-ID:t.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Skapa prenumerationer för ett MPA-faktureringskonto

### <a name="prerequisites"></a>Krav

Du måste ha rollen global administratör eller administratör i din organisations molnlösningsleverantörskonto för att skapa en prenumeration för ditt faktureringskonto. Mer information finns i [Partnercenter – Tilldela användarroller och behörigheter](/partner-center/permissions-overview).

I exemplet nedan används REST-API:er. PowerShell och Azure CLI stöds inte för närvarande.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Sök efter faktureringskonton som du har åtkomst till

Skicka begäran nedan om du vill se en lista över alla faktureringskonton som du har åtkomst till.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API-svaret visar en lista över faktureringskontona.

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
Använd egenskapen `displayName` för att identifiera det faktureringskonto som du vill skapa prenumerationer för. Se till att agreementType för kontot är *MicrosoftPartnerAgreement*. Kopiera `name` för kontot. Om du till exempel vill skapa en prenumeration för `Contoso`-faktureringskontot kopierar du `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Klistra in det här värdet någonstans så att du kan använda det i nästa steg.

### <a name="find-customers-that-have-azure-plans"></a>Hitta kunder som har Azure-planer

Gör följande begäran och ersätt `<billingAccountName>` med `name` som kopierades från det första steget (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) för att visa en lista över alla kunder i faktureringskontot som du kan skapa Azure-prenumerationer för.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
API-svaret visar en lista över kunder i faktureringskontot med Azure-planer. Du kan skapa prenumerationer för de här kunderna.

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

Använd egenskapen `displayName` för att identifiera kunden som du vill skapa prenumerationer för. Kopiera `id` för kunden. Om du till exempel vill skapa en prenumeration för `Fabrikam toys` kopierar du `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Klistra in det här värdet någonstans så att du kan använda det i kommande steg.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Valfritt för indirekta leverantörer: Hämta återförsäljarna för en kund

Om du är en indirekt leverantör i modellen med två nivåer för CSP kan välja en återförsäljare när de skapar prenumerationer för kunder.

Skicka följande begäran och ersätt `<customerId>` med `id` som kopierades från det andra steget (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) för att visa en lista över alla återförsäljare som är tillgängliga för en kund.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
API-svaret visar återförsäljarna för kunden:

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
Använd egenskapen `description` för att identifiera den återförsäljare som ska associeras med prenumerationen. Kopiera `resellerId` för återförsäljaren. Om du till exempel vill associera `Wingtip` kopierar du `3xxxxx`. Klistra in det här värdet någonstans så att du kan använda det i nästa steg.

### <a name="create-a-subscription-for-a-customer"></a>Skapa en prenumeration för en kund

I följande exempel skapas en prenumeration med namnet *Dev Team subscription* för *Fabrikam toys*, och återförsäljaren *Wingtip* associeras med prenumerationen. T

Skicka följande begäran och ersätt `<customerId>` med `id` som kopierats från det andra steget (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Skicka det valfria *resellerId* som kopierades från det andra steget i parametrarna för begäran till API:et.

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
| `displayName` | Ja      | Sträng | Prenumerationens visningsnamn.|
| `skuId` | Ja      | Sträng | SKU-ID för Azure-planen. Använd *0001* för prenumerationer av typen Microsoft Azure plan |
| `resellerId`      | Inga       | Sträng | MPN-ID för återförsäljaren som ska associeras med prenumerationen.  |

I svaret får du tillbaka ett `subscriptionCreationResult`-objekt för övervakning. När prenumerationen har skapats returnerar `subscriptionCreationResult`-objektet ett `subscriptionLink`-objekt, som har prenumerations-ID:t.

## <a name="next-steps"></a>Nästa steg

* Ett exempel på hur du skapar en EA-prenumeration (Enterprise-avtal) med .NET finns i [exempelkod på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Nu när du har skapat en prenumeration kan du bevilja den möjligheten till andra användare och tjänsthuvudnamn. Mer information finns i [Bevilja behörighet att skapa Azure Enterprise-prenumerationer (förhandsversion)](grant-access-to-create-subscription.md).
* Mer information om hur du hanterar ett stort antal prenumerationer med hanteringsgrupper finns i [Ordna resurser med hanteringsgrupper i Azure](../../governance/management-groups/overview.md)