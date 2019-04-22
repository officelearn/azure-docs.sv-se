---
title: Förstå fakturan Microsoft kundavtal | Microsoft Docs
description: Lär dig att läsa och förstå fakturan MCA
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: ee6317f61f95b19effd64308b88f53c027582b63
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883017"
---
# <a name="understand-terms-on-your-microsoft-customer-agreement-invoice"></a>Förstå villkoren på din faktura för Microsoft kundavtal

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

Fakturan innehåller en översikt över dina kostnader och instruktioner för betalning. Den är tillgänglig för hämtning i PDF--Format (PDF) från den [Azure-portalen](https://portal.azure.com/) eller skickas via e-post. Mer information finns i [visa och ladda ned Microsoft Azure fakturerar](billing-download-azure-invoice.md).

<!-- ## When am I billed?

You are invoiced on a monthly basis. You can find out which day of the month you receive invoices by checking *invoice date* under billing profile properties in the [Azure portal](https://portal.azure.com/). Charges that occur between the end of the billing period and the invoice date are included in the next month's invoice, since they are in the next billing period. The billing period start and end dates for each invoice are listed in the invoice PDF above **Billing Summary**. -->

## <a name="invoice-terms-and-descriptions"></a>Faktura villkor och beskrivningar

Följande avsnitt listar viktiga termer som du ser på din faktura och beskrivningar av varje term.

### <a name="invoice-summary"></a>Sammanfattning av faktura

Den **fakturasammanfattning** är överst på första sidan som visar information om profilen fakturering och hur du betala.

![Sammanfattningen av fakturan](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Period | Beskrivning |
| --- | --- |
| Såldes till |Adressen för din juridisk person som finns i fakturering Kontoegenskaper|
| Fakturera till |Fakturering-adressen för fakturering profilen får fakturan, finns i fakturering profilegenskaper|
| Faktureringsprofil |Namnet på fakturering profilen får fakturan |
| P.O. nummer |Ett valfritt inköpsordernummer som tilldelats av dig för spårning |
| Fakturanummer |Ett unikt, Microsoft-genererade fakturanummer som används i spårningssyfte |
| Fakturadatum |Datum då fakturan skapas, vanligtvis fem till 12 dagar efter slutet av faktureringscykel. Du kan kontrollera din fakturadatum i fakturering profilegenskaper.|
| Betalningsvillkor |Hur betalar för din Microsoft-faktura. *30 dagar netto* innebär att du betalar inom 30 dagar efter fakturadatum. |

### <a name="billing-summary"></a>Faktureringssammanfattning

Den **fakturering sammanfattning** visar avgifter avseende fakturering profilen eftersom den föregående faktureringsperioden, eventuella krediter som tillämpats, skatt och det totala beloppet som förfaller.

![Fakturering sammanfattningsavsnittet](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Period | Beskrivning |
| --- | --- |
| Avgifter|Totalt antal Microsoft debiterar för fakturering profilen sedan den senaste faktureringsperioden |
| Eftertexter |Krediter som du har fått från returnerar |
| Azure-krediter tillämpas | Azure-krediter som automatiskt tillämpas på Azure-avgifter varje faktureringsperiod |
| Delsumma |Före skatt beloppet |
| Moms |Typ och skatt du betalar, beroende på landet där din faktureringsinformation profil. Om du inte betala skatt kan se du inte skatt på din faktura. |
| Uppskattade totala besparingar |Det uppskattade totala beloppet som du sparade från effektiva rabatter. Om så är tillämpligt, visas effektiva rabatterna nedanför köp posterna i informationen om faktura-avsnittet. |

### <a name="invoice-sections"></a>Fakturaavsnitt

För varje faktura avsnitt under din faktureringsinformation profil visas avgifterna, mängden Azure-krediter tillämpas, skatt och det totala beloppet.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Information om fakturan avsnittet

Informationen visas kostnaden för varje faktura avsnitt uppdelat efter produkt ordning. Inom varje i produktionsorder är kostnaden fördelade på typ av tjänst. Du hittar dagliga avgifterna för dina produkter och tjänster i Azure-portalen och Azure-användning och kostnader CSV. Läs mer i [förstå debiteringar på fakturan för en Microsoft-kundavtal](billing-mca-understand-your-bill.md).

Totalsumman för varje tjänst-familjen beräknas genom att subtrahera *Azure-krediter* från *krediter/avgifter* och lägga till *skatt*:

<!-- `Total = Charges/Credits - Azure Credit + Tax` -->

![Information om fakturan avsnittet](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Period |Beskrivning |
| --- | --- |
| A-pris | Effektiva enhetspriset för tjänsten (pris för valuta) som används för att frekvensen användningen. Detta är unik för en produkt, tjänst-familjen, mätare och erbjudandet. |
| Antal | Kvantitet som har köpt eller förbrukats under fakturaperioden |
| Avgifter/krediter | Summan av kostnader när krediter/återbetalningar tillämpas |
| Azure-kredit | Mängden Azure krediter som tillämpats på avgifter/krediter|
| Skattesats | Skattesatser beroende på land |
| Skattebelopp | Skatt tillämpas om du vill köpa utifrån skattesats |
| Totalt | Totalsumman för inköp |

### <a name="how-to-pay"></a>Så här betalar du

Det finns anvisningar för att betala fakturan längst ned på fakturan. Du kan betala med check, under överföring, eller online. Om du betalar online kan använda du ett kredit-eller betalkort eller Azure-krediter, om tillämpligt.

### <a name="publisher-information"></a>Utgivarinformation

Om du har tjänster från tredje part på fakturan, visas namn och adress för varje utgivare längst ned på fakturan.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Förstå debiteringar på din faktureringsinformation profil faktura](billing-mca-understand-your-bill.md)
- [Så här hämtar du information om fakturering och daglig användning från Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Visa din organisations Azure priser](billing-ea-pricing.md)
- [Visa skattedokument för din faktureringsinformation profil](billing-mca-download-tax-document.md)
