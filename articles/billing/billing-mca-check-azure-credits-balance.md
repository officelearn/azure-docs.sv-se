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
ms.openlocfilehash: bbd456f82e333ab8e096e5695a55be43c2084c6d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223794"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Spåra Azure-kreditbalansen för Microsoft-kundavtal

Du kan kontrollera Azure-kreditsaldot för ditt faktureringskonto för ett Microsoft-kundavtal i Azure-portalen. 

Du använder krediter till att betala för avgifter som omfattas av krediterna. Du debiteras när du använder produkter som inte omfattas av krediterna eller när din användning överskrider kreditbeloppet. Mer information finns i [Produkter som inte omfattas av Azure-krediter](#products-that-arent-covered-by-azure-credits).

Krediter tilldelas till en faktureringsprofil på faktureringskontot för ett Microsoft-kundavtal. Varje faktureringsprofil har egna krediter. Du måste ha rollen ägare, deltagare, läsare eller fakturaansvarig för faktureringsprofilen eller rollen ägare, deltagare eller läsare för faktureringskontot för att kunna visa Azure-kreditsaldot för en faktureringsprofil. Du kan läsa mer om rollerna i [Förstå administrativa roller för Microsoft-kundavtal i Azure](billing-understand-mca-roles.md).

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance-in-the-azure-portal"></a>Kontrollera kreditsaldot i Azure-portalen

1. Logga in på [Azure-portalen]( https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

    ![Skärmbild som visar en sökning i portalen efter kostnadshantering och fakturering](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Välj **Azure-krediter** på vänster sida. Beroende på din åtkomst kan du behöva välja ett faktureringskonto eller en faktureringsprofil och sedan välja **Azure-krediter**.

4. På sidan Azure-krediter visas följande information:

   ![Skärmbild av ett kreditsaldo och transaktioner för en faktureringsprofil](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Period               | Definition                           |
   |--------------------|--------------------------------------------------------|
   | Uppskattat saldo  | Uppskattat belopp med krediter som du har när alla fakturerade och väntande transaktioner har tagits med i beräkningen |
   | Aktuellt saldo    | Summan av krediterna från din senaste faktura. Inga väntande transaktioner ingår |
   | Transaktioner       | Faktureringstransaktioner som påverkat ditt Azure-kreditsaldo |

   När ditt beräknade saldo sjunker till 0 debiteras du för all användning, inklusive för produkter som omfattas av krediter.

6. Välj **Kreditlista** om du vill visa en lista med krediter för faktureringsprofilen. Kreditlistan innehåller följande information:

   ![Skärmbild av kreditlistor för en faktureringsprofil](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Period | Definition |
   |---|---|
   | Källa | Kreditens förvärvskälla |
   | Startdatum | Det datum då du skaffade krediten |
   | Förfallodatum | Det datum då krediten upphör att gälla |
   | Aktuellt saldo | Saldot från din senaste faktura |
   | Ursprungligt belopp | Den ursprungliga mängden kredit |
   | Status | Aktuell kreditstatus. Statusen kan vara aktiv, används, upphörd eller håller på att upphöra |

## <a name="check-your-credit-balance-programmatically"></a>Kontrollera ditt kreditsaldo programmatiskt

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

Använd egenskapen `displayName` för faktureringsprofilen till att identifiera den faktureringsprofil som du vill kontrollera kreditsaldot för. Kopiera egenskapen `id` för faktureringsprofilen. Om du till exempel vill kontrollera kreditsaldot för faktureringsprofilen **Development** kopierar du ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Klistra in det här värdet någonstans så att du kan använda det i nästa steg.

### <a name="get-azure-credit-balance"></a>Hämta kreditsaldot för Azure 

Kör följande förfrågan och ersätt `<billingProfileId>` med värdet för `id` som du kopierade i första steget (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

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

Kör följande förfrågan och ersätt `<billingProfileId>` med värdet för `id` som du kopierade i första steget (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

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

Kör följande förfrågan och ersätt `<billingProfileId>` med värdet för `id` som du kopierade i första steget (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Du måste skicka ett värde för **startDate** och **endDate** för att få transaktioner för din begärda tidsperiod.

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
- Azure-supportplaner

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Förstå faktureringskonton för Microsofts-kundavtal](billing-mca-overview.md)
- [Förstå termerna på fakturan för ditt Microsoft-kundavtal](billing-mca-understand-your-invoice.md)
