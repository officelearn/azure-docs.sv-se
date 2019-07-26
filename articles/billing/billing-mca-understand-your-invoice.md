---
title: Förstå din Microsoft-kund avtals faktura i Azure
description: Lär dig att läsa och förstå din Microsoft-kund avtals faktura i Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: a5f77120c1d4e8a6721f3bc207132bee19a7772f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383535"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Villkor i din Microsoft kund avtals faktura

Den här artikeln gäller ett Azure-fakturerings konto för ett Microsofts kund avtal. [Kontrol lera om du har åtkomst till ett Microsofts kund avtal](#check-access-to-a-microsoft-customer-agreement).

Din faktura innehåller en sammanfattning av dina avgifter och betalnings anvisningar. Den finns tillgänglig för hämtning i Portable Document Format (. pdf) från [Azure Portal](https://portal.azure.com/) eller kan skickas via e-post. Mer information finns i [Visa och ladda ned din Microsoft Azure faktura](billing-download-azure-invoice.md).

## <a name="billing-period"></a>Faktureringsperiod

Du faktureras per månad. Du kan ta reda på vilken dag i månaden du får fakturor genom att kontrol lera *faktura datum* under egenskaper för fakturerings profil i [Azure Portal](https://portal.azure.com/). Kostnader som inträffar mellan slutet av fakturerings perioden och faktura datumet ingår i nästa månads faktura, eftersom de är i nästa fakturerings period. Start-och slutdatumen för fakturerings perioden för varje faktura visas i faktura-PDF -filen ovanför fakturerings sammanfattningen.

## <a name="invoice-terms-and-descriptions"></a>Faktura villkor och beskrivningar

I följande avsnitt listas viktiga termer som du ser på din faktura och ger beskrivningar för varje term.

### <a name="invoice-summary"></a>Faktura sammanfattning

**Faktura sammanfattningen** visas överst på den första sidan och visar information om din fakturerings profil och hur du betalar.

![Avsnittet Sammanfattning av faktura](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Term | Beskrivning |
| --- | --- |
| Såldes till |Adress till din juridiska person, som finns i egenskaper för fakturerings konto|
| Fakturera till |Fakturerings adressen för den fakturerings profil som tar emot fakturan, som finns i egenskaper för fakturerings profil|
| Faktureringsprofil |Namnet på den fakturerings profil som tar emot fakturan |
| P.O. nummer |Ett valfritt inköps order nummer som du har tilldelat för spårning |
| Faktura nummer |Ett unikt, Microsoft-genererat faktura nummer som används i spårnings syfte |
| Fakturadatum |Datum då fakturan genereras, vanligt vis fem till 12 dagar efter fakturerings cykelns slut. Du kan kontrol lera faktura datumet i egenskaper för fakturerings profil.|
| Betalnings villkor |Hur du betalar för din Microsoft-faktura. *Netto 30 dagar* innebär att du betalar inom 30 dagar från faktura datumet. |

### <a name="billing-summary"></a>Fakturerings Sammanfattning

I **fakturerings sammanfattningen** visas kostnaderna för fakturerings profilen sedan den föregående fakturerings perioden, eventuella krediter som tillämpats, skatt och det totala beloppet som förfallit.

![Avsnittet fakturerings Sammanfattning](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Term | Beskrivning |
| --- | --- |
| Avgifter|Totalt antal Microsoft-avgifter för den här fakturerings profilen sedan den senaste fakturerings perioden |
| Eftertexter |Krediter som du fått från returer |
| Azure-krediter tillämpas | Azure-krediter som automatiskt tillämpas på Azure debiterar varje fakturerings period |
| Delsumma |Förfallet belopp för förskotts skatt |
| Moms |Typ av och belopp för skatt som du betalar, beroende på land/region för din fakturerings profil. Om du inte behöver betala skatt kan du inte se skatt på din faktura. |
| Beräknad total besparing |Den uppskattade total summan som du har sparat från effektiva rabatter. Om så är tillämpligt visas effektiva rabatt satser under inköps rad artiklarna i avsnittet information per faktura. |

### <a name="invoice-sections"></a>Fakturaavsnitt

För varje faktura avsnitt under din fakturerings profil ser du avgifterna, hur många Azure-krediter som används, skatt och det totala beloppet som förfaller.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Avsnittet information efter faktura

Informationen visar kostnaden för varje faktura avsnitt uppdelat efter produkt order. Inom varje produkt ordning delas kostnaden upp av tjänst typen. Du hittar dagliga avgifter för dina produkter och tjänster i Azure Portal och Azures användning och avgifter CSV. Mer information finns i [förstå avgifterna på din faktura för ett Microsofts kund avtal](billing-mca-understand-your-bill.md).

Det totala beloppet som förfaller för varje service familj beräknas genom att subtrahera *Azure-krediter* från *krediter/avgifter* och *moms*:


![Avsnittet information efter faktura](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Term |Beskrivning |
| --- | --- |
| A-pris | Det effektiva enhets priset för tjänsten (i pris valutan) som används för att bedöma användningen. Detta är unikt för en produkt, tjänst familj, mätare och erbjudande. |
| Kvantitet | Köpt eller förbrukad kvantitet under fakturerings perioden |
| Avgifter/krediter | Netto beloppet för avgifterna per kredit och åter betalning gäller |
| Azure-kredit | Mängden Azure-krediter som används för avgifterna/krediterna|
| Skatte sats | Skatte sats (er) beroende på land/region |
| Skatte belopp | Moms belopp som tillämpas på inköp baserat på skatte sats |
| Totalt | Det totala beloppet som ska betalas för köpet |

### <a name="how-to-pay"></a>Så här betalar du

Längst ned på fakturan finns det instruktioner för att betala fakturan. Du kan betala per kontroll, tråd eller online. Om du betalar online kan du använda ett kredit kort eller en Azure-kredit, om så är tillämpligt.

### <a name="publisher-information"></a>Utgivar information

Om du har tjänster från tredje part på din faktura visas namnet och adressen för varje utgivare längst ned på fakturan.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrol lera åtkomsten till ett Microsofts kund avtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Förstå avgifterna på din fakturerings profils faktura](billing-mca-understand-your-bill.md)
- [Så här hämtar du information om fakturering och daglig användning från Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Visa din organisations priser för Azure](billing-ea-pricing.md)
- [Visa moms dokument för din fakturerings profil](billing-mca-download-tax-document.md)
