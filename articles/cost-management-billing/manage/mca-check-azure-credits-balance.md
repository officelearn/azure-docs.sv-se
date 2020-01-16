---
title: Spåra Azure-kreditbalansen för ett Microsoft-kundavtal
description: Lär dig hur du spårar Azure-kreditbalansen för ett Microsoft-kundavtal.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c8ea083e216331904c9d5741b97f69b7f5a8249a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991339"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Spåra Azure-kreditbalansen för Microsoft-kundavtal

Du kan kontrol lera Azures kredit balans för ditt fakturerings konto för ett Microsofts kund avtal i Azure Portal eller via REST API: er.

Krediter tilldelas till en faktureringsprofil på faktureringskontot för ett Microsoft-kundavtal. Varje fakturerings profil har sina egna krediter som automatiskt tillämpas på kostnaderna på fakturan. Du måste ha rollen ägare, deltagare, läsare eller fakturaansvarig för faktureringsprofilen eller rollen ägare, deltagare eller läsare för faktureringskontot för att kunna visa Azure-kreditsaldot för en faktureringsprofil. Du kan läsa mer om rollerna i [Förstå administrativa roller för Microsoft-kundavtal i Azure](understand-mca-roles.md).

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Kontrollera ditt kreditsaldo

### <a name="azure-portaltabportal"></a>[Azure-portalen](#tab/portal)

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

    ![Skärmbild som visar en sökning i portalen efter kostnadshantering och fakturering](./media/mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. På sidan fakturerings omfattningar väljer du det fakturerings konto som du vill spåra kredit balansen för. Fakturerings kontot bör vara av typen **Microsofts kund avtal**.

    ![Skärmbild som visar en sökning i portalen efter kostnadshantering och fakturering](./media/mca-check-azure-credits-balance/list-of-scopes.png)

    > [!NOTE]
    >
    > Azure Portal ommedlemmars den senaste fakturerings omfattningen som du kommer åt och visar omfånget nästa gång du kommer till Cost Management + fakturerings sida. Sidan fakturerings omfattningar visas inte om du har besökt Cost Management + fakturering tidigare. I så fall, kontrol lera att du är i [rätt omfång](#check-access-to-a-microsoft-customer-agreement). Om inte, [byter du omfattning](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) för att välja fakturerings konto för ett Microsoft-kundavtal.

3. Välj **betalnings metoder** från vänster sida och välj sedan **Azure-krediter**.

   ![Skärm bild av kredit balans för en fakturerings profil](./media/mca-check-azure-credits-balance/mca-payment-methods.png)

4. Sidan Azure-krediter innehåller följande avsnitt:

   #### <a name="balance"></a>Saldo

   I avsnittet saldo visas en översikt över ditt Azure-saldo.

   ![Skärm bild av kredit balans för en fakturerings profil](./media/mca-check-azure-credits-balance/mca-credit-balance.png)

   | Period               | Definition                           |
   |--------------------|--------------------------------------------------------|
   | Uppskattat saldo  | Uppskattat belopp med krediter som du har när alla fakturerade och väntande transaktioner har tagits med i beräkningen |
   | Aktuellt saldo    | Summan av krediterna från din senaste faktura. Inga väntande transaktioner ingår |

   När ditt beräknade saldo sjunker till 0 debiteras du för all användning, inklusive för produkter som omfattas av krediter.

   #### <a name="credits-list"></a>Kredit lista

   I avsnittet kredit lista visas en lista över Azure-krediter.

   ![Skärmbild av kreditlistor för en faktureringsprofil](./media/mca-check-azure-credits-balance/mca-credits-list.png)

   | Period | Definition |
   |---|---|
   | Källa | Kreditens förvärvskälla |
   | Startdatum | Det datum då du skaffade krediten |
   | Förfallodatum | Det datum då krediten upphör att gälla |
   | Aktuellt saldo | Saldot från din senaste faktura |
   | Ursprungligt belopp | Den ursprungliga mängden kredit |
   | Status | Aktuell kreditstatus. Statusen kan vara aktiv, används, upphörd eller håller på att upphöra |

   #### <a name="transactions"></a>Transaktioner

   I avsnittet om transaktioner visas alla transaktioner som påverkat kredit balansen.

   ![Skärm bild av kredit transaktioner för en fakturerings profil](./media/mca-check-azure-credits-balance/mca-credits-transactions.png)

   | Period | Definition |
   |---|---|
   | Transaktions datum | Datumet då transaktionen skedde |
   | Beskrivning | En beskrivning av transaktionen |
   | Mängd| Transaktionsbeloppet |
   | Saldo | Saldot efter transaktionen |

    > [!NOTE]
    >
    > Om du inte ser Azure-krediter på sidan betalnings metoder kan du antingen inte ha krediter eller så har du inte valt rätt omfång. Välj det fakturerings konto som har krediter eller någon av dess fakturerings profiler. Information om hur du ändrar omfång finns i ändra [fakturerings omfång i Azure Portal](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Om du visar Azure-krediter i fakturerings konto omfånget och fakturerings kontot har fler än en fakturerings profil, visar sidan Azure-krediter en tabell med en sammanfattning av Azure-krediterna för varje fakturerings profil. Välj en fakturerings profil i listan, Välj betalnings metoder och sedan Azure-krediter för att visa information om en fakturerings profil.

    ![Skärm bild av kredit lista för ett fakturerings konto](./media/mca-check-azure-credits-balance/mca-account-credit-list.png)

### <a name="rest-apitabrest"></a>[REST API](#tab/rest)

Du kan använda API:erna [Azure Billing](https://docs.microsoft.com/rest/api/billing/) och [Consumption](https://docs.microsoft.com/rest/api/consumption/) till att kontrollera kreditsaldot för ditt faktureringskonto programmatiskt.

I exemplen nedan används REST-API:er. PowerShell och Azure CLI stöds inte för närvarande.

### <a name="find-billing-profiles-you-have-access-to"></a>Kontrollera vilka faktureringsprofiler du har åtkomst till

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
I API-svaret ser du en lista med faktureringskonton och tillhörande faktureringsprofiler.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Använd egenskapen `displayName` för fakturerings profilen för att identifiera den fakturerings profil som du vill kontrol lera kredit balansen för. Kopiera egenskapen `id` för faktureringsprofilen. Om du till exempel vill kontrollera kreditsaldot för faktureringsprofilen **Development** kopierar du ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Klistra in det här värdet någonstans så att du kan använda det i nästa steg.

### <a name="get-azure-credit-balance"></a>Hämta kreditsaldot för Azure

Gör följande begäran och ersätt `<billingProfileId>` med `id` kopieras i det första steget (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

Du ser uppskattat och aktuellt saldo för faktureringsprofilen i API-svaret.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Elementnamn  | Beskrivning                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | Uppskattat kreditsaldo när alla fakturerade och väntande transaktioner har tagits med i beräkningen. |
| `currentBalance`   | Summan av krediterna från din senaste faktura. Här ingår inga väntande transaktioner.    |
| `pendingCreditAdjustments`      | justeringar, som återbetalningar som ännu inte har fakturerats.  |
| `expiredCredit`      |  Den kredit som har upphört att gälla sedan din senaste faktura.  |
| `pendingEligibleCharges`  | De debiteringar som omfattas av krediter som ännu inte har fakturerats.   |

### <a name="get-list-of-credits"></a>Hämta en lista med krediter

Gör följande begäran och ersätt `<billingProfileId>` med `id` kopieras i det första steget (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
Du ser en lista med Azure-krediter för en faktureringsprofil i API-svaret.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Elementnamn  | Beskrivning                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | Det ursprungliga kreditbeloppet. |
| `closedBalance`   | Saldot sedan din senaste faktura.    |
| `source`      | Källan som definierar vem som har köpt krediten. |
| `startDate`      |  Datumet då krediten började gälla.  |
| `expirationDate`  | Datumet när krediten upphör att gälla.   |
| `poNumber`  | Inköpsordernumret på fakturan som krediten räknades av mot.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Hämta transaktioner som påverkat kreditsaldot

Gör följande begäran och ersätt `<billingProfileId>` med `id` kopieras i det första steget (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Du måste skicka ett värde för **startDate** och **endDate** för att få transaktioner för din begärda tidsperiod.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
Du ser alla transaktioner som påverkat kreditsaldot för din faktureringsprofil i API-svaret.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Elementnamn  | Beskrivning                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | Datumet när transaktionen ägde rum. |
| `description` | En beskrivning av transaktionen. |
| `adjustments`   | Kreditjusteringen för transaktionen.    |
| `creditExpired`      | Kreditbeloppet som har upphört att gälla. |
| `charges`      |  Avgifterna för transaktionen.  |
| `closedBalance`  | Saldot efter transaktionen.   |
| `eventType`  | Typen av transaktion.   |
| `invoiceNumber`  | Fakturanumret för fakturan som transaktionen satts upp på. Det här är tomt för väntande transaktioner.   |

---

## <a name="how-credits-are-used"></a>Så används krediter

I ett faktureringskonto för ett Microsoft-kundavtal använder du faktureringsprofiler för att hantera fakturor och betalningsmetoder. En månadsfaktura skapas för varje faktureringsprofil, och du använder betalningsmetoderna för att betala fakturan.

Du tilldelar dina krediter till en faktureringsprofil. När en faktura genereras för faktureringsprofilen räknas krediterna automatiskt av från de totala avgifterna så att du ser vilket belopp du behöver betala. Du betalar återstående belopp med vald betalningsmetod, som check/banköverföring eller kreditkort.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produkter som inte omfattas av Azure-krediter

 Följande produkter omfattas inte av dina Azure-krediter. Du debiteras för användning av dessa produkter oavsett ditt kreditsaldo:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registrerad användare
- Openlogic
- Registrerad användare för Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (månatligen)
- Visual Studio Enterprise (årsvis)
- Visual Studio Professional (månatligen)
- Visual Studio Professional (årsvis)
- Azure Marketplace-produkter
- Supportavtal för Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Förstå faktureringskonton för Microsofts-kundavtal](../understand/mca-overview.md)
- [Förstå termerna på fakturan för ditt Microsoft-kundavtal](../understand/mca-understand-your-invoice.md)
