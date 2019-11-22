---
title: Skapa Azure-prenumerationer program mässigt
description: Lär dig hur du skapar ytterligare Azure-prenumerationer program mässigt.
author: amberb
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: banders
ms.openlocfilehash: 757a542c8583f6a2b3f73e8144b6281438d75ef2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273577"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Skapa Azure-prenumerationer program mässigt (för hands version)

Azure-kunder med ett [Enterprise-avtal (EA](https://azure.microsoft.com/pricing/enterprise-agreement/)), ett fakturerings konto för [Microsoft kund avtal (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) eller [Microsoft partner Agreement (MPa)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) kan skapa prenumerationer program mässigt. I den här artikeln får du lära dig hur du skapar prenumerationer program mässigt med hjälp av Azure Resource Manager.

När du skapar en Azure-prenumeration via programmering, styrs prenumerationen av avtalet enligt vilket du fick Azure-tjänster från Microsoft eller en auktoriserad åter försäljare. Läs mer i [Microsoft Azure juridisk information](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Skapa prenumerationer för ett EA-fakturerings konto

### <a name="prerequisites"></a>Krav

Du måste ha en ägar roll på ett registrerings konto för att kunna skapa en prenumeration. Det finns två sätt att hämta rollen:

* Företags administratören för din registrering kan [göra en konto ägare](https://ea.azure.com/helpdocs/addNewAccount) (inloggning krävs) som gör att du får en ägare till registrerings kontot.

* En befintlig ägare av registrerings kontot kan [ge dig åtkomst](grant-access-to-create-subscription.md). På samma sätt måste du, om du vill använda ett huvud namn för tjänsten för att skapa en EA-prenumeration, [ge tjänsten huvud kontot möjlighet att skapa prenumerationer](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Hitta konton som du har åtkomst till

När du har lagt till ett registrerings konto som är kopplat till en konto ägare använder Azure konto-till-registrerings-relationen för att avgöra var prenumerations avgifterna ska faktureras. Alla prenumerationer som skapas under kontot faktureras till den EA-registrering som kontot finns i. Om du vill skapa prenumerationer måste du skicka in värden för registrerings kontot och användarens huvud namn för att äga prenumerationen. 

Om du vill köra följande kommandon måste du vara inloggad på konto ägarens *Hem Katalog*, som är den katalog som prenumerationer skapas i som standard.

### <a name="resttabrest"></a>[REST](#tab/rest)

Begäran om att lista alla registrerings konton som du har åtkomst till:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

API-svaret visar alla registrerings konton som du har åtkomst till:

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

Använd egenskapen `principalName` för att identifiera det konto som du vill debitera prenumerationer på. Kopiera `name` för det kontot. Om du till exempel vill skapa prenumerationer under SignUpEngineering@contoso.com registrerings kontot kopierar du ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Den här identifieraren är objekt-ID: t för registrerings kontot. Klistra in det här värdet någonstans så att du kan använda det i nästa steg som `enrollmentAccountObjectId`.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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
Använd egenskapen `principalName` för att identifiera det konto som du vill debitera prenumerationer på. Kopiera `ObjectId` för det kontot. Om du till exempel vill skapa prenumerationer under SignUpEngineering@contoso.com registrerings kontot kopierar du ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Klistra in detta objekt-ID någonstans så att du kan använda det i nästa steg som `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

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

Använd egenskapen `principalName` för att identifiera det konto som du vill debitera prenumerationer på. Kopiera `name` för det kontot. Om du till exempel vill skapa prenumerationer under SignUpEngineering@contoso.com registrerings kontot kopierar du ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Den här identifieraren är objekt-ID: t för registrerings kontot. Klistra in det här värdet någonstans så att du kan använda det i nästa steg som `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Skapa prenumerationer under ett angivet registrerings konto

I följande exempel skapas en prenumeration med namnet *dev team-prenumeration* i det registrerings konto som valts i föregående steg. Prenumerations erbjudandet är *MS-AZR-0017P* (vanliga Microsoft Enterprise-avtal). Du kan också lägga till två användare som RBAC-ägare för prenumerationen.

### <a name="resttabrest"></a>[REST](#tab/rest)

Kör följande förfrågan och ersätt `<enrollmentAccountObjectId>` med värdet för `name` som du kopierade i första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Om du vill ange ägare kan du läsa mer om [hur du hämtar användar objekt-ID: n](grant-access-to-create-subscription.md#userObjectId).

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

| Element namn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nej      | Sträng | Visnings namnet för prenumerationen. Om inget annat anges anges namnet på erbjudandet, t. ex. "Microsoft Azure Enterprise".                                 |
| `offerType`   | Ja      | Sträng | Prenumerationens erbjudande. De två alternativen för EA är [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktions användning) och [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, måste [aktive ras med hjälp av EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nej       | Sträng | Objekt-ID för en användare som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas.  |

I svaret får du tillbaka ett `subscriptionOperation`-objekt för övervakning. När prenumerationen har skapats returnerar `subscriptionOperation`-objektet ett `subscriptionLink`-objekt, som har prenumerations-ID.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Installera först den här förhands gransknings modulen genom att köra `Install-Module Az.Subscription -AllowPrerelease`. För att se till att `-AllowPrerelease` fungerar installerar du en ny version av PowerShellGet från [Hämta PowerShellGet-modulen](/powershell/scripting/gallery/installing-psget).

Kör kommandot [New-AzSubscription](/powershell/module/az.subscription) nedan och ersätt `<enrollmentAccountObjectId>` med `ObjectId` som samlas in i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Om du vill ange ägare kan du läsa mer om [hur du hämtar användar objekt-ID: n](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Element namn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nej      | Sträng | Visnings namnet för prenumerationen. Om inget annat anges anges namnet på erbjudandet, t. ex. "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Ja      | Sträng | Prenumerationens erbjudande. De två alternativen för EA är [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktions användning) och [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, måste [aktive ras med hjälp av EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ja       | Sträng | Objekt-ID för det registrerings konto som prenumerationen skapas under och faktureras till. Det här värdet är ett GUID som du får från `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Nej       | Sträng | Objekt-ID för en användare som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas.  |
| `OwnerSignInName`    | Nej       | Sträng | E-postadressen till en användare som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas. Du kan använda den här parametern i stället för `OwnerObjectId`.|
| `OwnerApplicationId` | Nej       | Sträng | Program-ID för alla tjänst objekt som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas. Du kan använda den här parametern i stället för `OwnerObjectId`. När du använder den här parametern måste tjänstens huvud namn ha [Läs behörighet till katalogen](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Om du vill se en fullständig lista över alla parametrar, se [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Installera först detta förhands gransknings tillägg genom att köra `az extension add --name subscription`.

Kör kommandot [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) nedan och ersätt `<enrollmentAccountObjectId>` med `name` som du kopierade i det första steget (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Om du vill ange ägare kan du läsa mer om [hur du hämtar användar objekt-ID: n](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Element namn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nej      | Sträng | Visnings namnet för prenumerationen. Om inget annat anges anges namnet på erbjudandet, t. ex. "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Ja      | Sträng | Prenumerationens erbjudande. De två alternativen för EA är [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produktions användning) och [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test, måste [aktive ras med hjälp av EA-portalen](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ja       | Sträng | Objekt-ID för det registrerings konto som prenumerationen skapas under och faktureras till. Det här värdet är ett GUID som du får från `az billing enrollment-account list`. |
| `owner-object-id`      | Nej       | Sträng | Objekt-ID för en användare som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas.  |
| `owner-upn`    | Nej       | Sträng | E-postadressen till en användare som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas. Du kan använda den här parametern i stället för `owner-object-id`.|
| `owner-spn` | Nej       | Sträng | Program-ID för alla tjänst objekt som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas. Du kan använda den här parametern i stället för `owner-object-id`. När du använder den här parametern måste tjänstens huvud namn ha [Läs behörighet till katalogen](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Om du vill se en fullständig lista över alla parametrar, se [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Begränsningar för Azures API för att skapa prenumerationer

- Det går bara att skapa Azure Enterprise-prenumerationer med det här API: et.
- Det finns en gräns på 200 prenumerationer per registrerings konto. Därefter kan du bara skapa fler prenumerationer för kontot i Azure Portal. Skapa ett annat registrerings konto om du vill skapa fler prenumerationer via API: et.
- Användare som inte är konto ägare, men som har lagts till i ett registrerings konto via RBAC, kan inte skapa prenumerationer i Azure Portal.
- Du kan inte välja klienten för den prenumeration som ska skapas i. Prenumerationen skapas alltid i konto ägarens hem klient. Information om hur du flyttar prenumerationen till en annan klient finns i [ändra prenumerations klient](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Skapa prenumerationer för ett MCA-konto

### <a name="prerequisites"></a>Krav

Du måste ha rollen ägare, deltagare eller Azure-prenumerations skapare i avsnittet faktura eller ägare eller deltagare i en fakturerings profil eller ett fakturerings konto för att skapa prenumerationer. Mer information finns i [Roller och uppgifter för prenumerationsfakturering](../billing/billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).

I exemplet nedan används REST-API: er. PowerShell och Azure CLI stöds inte för närvarande.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Sök efter fakturerings konton som du har åtkomst till 

Gör begäran nedan om du vill lista alla fakturerings konton.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API-svaret visar de fakturerings konton som du har åtkomst till.

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
Använd egenskapen `displayName` för att identifiera det fakturerings konto som du vill skapa prenumerationer för. Se till att agreeementType för kontot är *MicrosoftCustomerAgreement*. Kopiera `name` för kontot.  Om du till exempel vill skapa en prenumeration för `Contoso` fakturerings kontot kopierar du `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Klistra in det här värdet någonstans så att du kan använda det i nästa steg.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Hitta faktura avsnitt för att skapa prenumerationer

Avgifterna för din prenumeration visas i ett avsnitt i fakturerings profilens faktura. Använd följande API för att hämta listan över faktura avsnitt och fakturerings profiler som du har behörighet att skapa Azure-prenumerationer på.

Kör följande förfrågan och ersätt `<billingAccountName>` med värdet för `name` som du kopierade i första steget (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
API-svaret visar alla faktura avsnitt och de fakturerings profiler som du har åtkomst till för att skapa prenumerationer:

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

Använd egenskapen `invoiceSectionDisplayName` för att identifiera faktura avsnittet som du vill skapa prenumerationer för. Kopiera `invoiceSectionId``billingProfileId` och ett av `skuId` för avsnittet faktura. Om du till exempel vill skapa en prenumeration av typen `Microsoft Azure plan` för `Development` faktura kopierar du `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` och `0001`. Klistra in dessa värden någonstans så att du kan använda dem i nästa steg.

### <a name="create-a-subscription-for-an-invoice-section"></a>Avsnittet Skapa en prenumeration för en faktura

I följande exempel skapas en prenumeration med namnet *dev team-prenumeration* av typen *Microsoft Azure plan* för avsnittet *utvecklings* faktura. Prenumerationen debiteras för *Contosos ekonomi* fakturerings profil och visas i *utvecklings* avsnittet för fakturan. 

Gör följande begäran och ersätt `<invoiceSectionId>` med `invoiceSectionId` som kopieras från det andra steget (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Du måste skicka `billingProfileId` och `skuId` kopieras från det andra steget i parametrarna för begäran i API: et. Om du vill ange ägare kan du läsa mer om [hur du hämtar användar objekt-ID: n](grant-access-to-create-subscription.md#userObjectId).

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

| Element namn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ja      | Sträng | Visnings namnet för prenumerationen.|
| `billingProfileId`   | Ja      | Sträng | ID för den fakturerings profil som ska faktureras för prenumerationens kostnader.  |
| `skuId` | Ja      | Sträng | Det SKU-ID som avgör typen av Azure-plan. |
| `owners`      | Nej       | Sträng | Objekt-ID för användare eller tjänstens huvud namn som du vill lägga till som en RBAC-ägare för prenumerationen när den skapas.  |
| `costCenter` | Nej      | Sträng | Det kostnads ställe som är associerat med prenumerationen. Den visas i CSV-filen för användning. |
| `managementGroupId` | Nej      | Sträng | ID för den hanterings grupp som prenumerationen ska läggas till i. Information om hur du hämtar listan över hanterings grupper finns i [hanteringsgrupper-List-API](https://docs.microsoft.com/rest/api/resources/managementgroups/list). Använd ID för en hanterings grupp från API: et. |

I svaret får du tillbaka ett `subscriptionCreationResult`-objekt för övervakning. När prenumerationen har skapats returnerar `subscriptionCreationResult`-objektet ett `subscriptionLink`-objekt, som har prenumerations-ID.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Skapa prenumerationer för ett produkt aktiverings fakturerings konto

### <a name="prerequisites"></a>Krav

Du måste ha en rollen global administratör eller administratör i din organisations konto för moln lösnings leverantör för att skapa en prenumeration för ditt fakturerings konto. Mer information finns i [partner Center – tilldela användare roller och behörigheter](https://docs.microsoft.com/partner-center/permissions-overview).

I exemplet nedan används REST-API: er. PowerShell och Azure CLI stöds inte för närvarande.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Hitta de fakturerings konton som du har åtkomst till 

Gör begäran nedan för att ange alla fakturerings konton som du har åtkomst till.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API-svar listar fakturerings kontona.

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
Använd egenskapen `displayName` för att identifiera det fakturerings konto som du vill skapa prenumerationer för. Se till att agreeementType för kontot är *MicrosoftPartnerAgreement*. Kopiera `name` för kontot. Om du till exempel vill skapa en prenumeration för `Contoso` fakturerings kontot kopierar du `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Klistra in det här värdet någonstans så att du kan använda det i nästa steg.

### <a name="find-customers-that-have-azure-plans"></a>Hitta kunder som har Azure-planer

Gör följande begäran och ersätt `<billingAccountName>` med `name` kopieras från det första steget (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) för att visa en lista över alla kunder i fakturerings kontot som du kan skapa Azure-prenumerationer för. 

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
API-svaret visar en lista över kunder i fakturerings kontot med Azure-planer. Du kan skapa prenumerationer för de här kunderna.

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

Använd egenskapen `displayName` för att identifiera den kund som du vill skapa prenumerationer för. Kopiera `id` för kunden. Om du till exempel vill skapa en prenumeration för `Fabrikam toys`kopierar du `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Klistra in det här värdet någonstans för att använda det i följande steg.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Valfritt för indirekta leverantörer: få åter försäljarna för en kund

Om du är en indirekt leverantör i CSP-modellen på två nivåer kan du ange en åter försäljare när du skapar prenumerationer för kunder. 

Gör följande begäran och ersätt `<customerId>` med `id` kopieras från det andra steget (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) för att visa en lista över alla åter försäljare som är tillgängliga för en kund.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
API-svaret visar åter försäljarna för kunden:

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
Använd egenskapen `description` för att identifiera den åter försäljare som ska associeras med prenumerationen. Kopiera `resellerId` för åter försäljaren. Om du till exempel vill associera `Wingtip`kopierar du `3xxxxx`. Klistra in det här värdet någonstans så att du kan använda det i nästa steg.

### <a name="create-a-subscription-for-a-customer"></a>Skapa en prenumeration för en kund

I följande exempel skapas en prenumeration med namnet *dev team-prenumeration* för *Fabrikam Toys* och associerar *Wingtip* -återförsäljare till prenumerationen. T

Gör följande begäran och ersätt `<customerId>` med `id` som kopieras från det andra steget (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Skicka de valfria *resellerId* som kopierats från det andra steget i parametrarna för begäran i API: et. 

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Element namn  | Krävs | Typ   | Beskrivning                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ja      | Sträng | Visnings namnet för prenumerationen.|
| `skuId` | Ja      | Sträng | SKU-ID för Azure-planen. Använd *0001* för prenumerationer av typen Microsoft Azure plan |
| `resellerId`      | Nej       | Sträng | MPN-ID för den åter försäljare som ska associeras med prenumerationen.  |

I svaret får du tillbaka ett `subscriptionCreationResult`-objekt för övervakning. När prenumerationen har skapats returnerar `subscriptionCreationResult`-objektet ett `subscriptionLink`-objekt, som har prenumerations-ID.

## <a name="next-steps"></a>Nästa steg

* Ett exempel på hur du skapar en Enterprise-avtal-prenumeration (EA) med hjälp av .NET finns i [exempel kod på GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Nu när du har skapat en prenumeration kan du bevilja den möjligheten till andra användare och tjänstens huvud namn. Mer information finns i [bevilja åtkomst till skapa Azure Enterprise-prenumerationer (för hands version)](grant-access-to-create-subscription.md).
* Mer information om hur du hanterar ett stort antal prenumerationer med hjälp av hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](management-groups-overview.md)
