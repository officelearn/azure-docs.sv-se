---
title: Förstå fakturan Microsoft kundavtal i Azure
description: Lär dig att läsa och förstå din faktura för Microsoft kundavtal i Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fed658d3f672d6116d7c2b0f3e2e9ad989dd67c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490643"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Villkoren i ditt Microsoft-kundavtal faktura

Den här artikeln gäller för en Azure faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

Fakturan innehåller en översikt över dina kostnader och instruktioner för betalning. Den är tillgänglig för hämtning i PDF--Format (PDF) från den [Azure-portalen](https://portal.azure.com/) eller skickas via e-post. Mer information finns i [visa och ladda ned Microsoft Azure fakturerar](billing-download-azure-invoice.md).

## <a name="billing-period"></a>Faktureringsperiod

Du faktureras per månad. Du kan se vilken dag i månaden som du tar emot fakturor genom att kontrollera *fakturadatum* under fakturering profilegenskaper i den [Azure-portalen](https://portal.azure.com/). Avgifter som sker mellan slutet av faktureringsperioden och fakturadatum ingår i nästa månads faktura, eftersom de finns i nästa faktureringsperiod. Fakturering start- och datumen för perioden för respektive faktura visas på fakturan PDF ovan **fakturering sammanfattning**.

## <a name="invoice-terms-and-descriptions"></a>Faktura villkor och beskrivningar

I de följande avsnitten lista viktiga termer som du ser på din faktura och ange beskrivningar av varje term.

### <a name="invoice-summary"></a>Sammanfattning av faktura

Den **fakturasammanfattning** är högst upp på den första sidan som visar information om profilen fakturering och hur du betala.

![Sammanfattningen av fakturan](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Term | Beskrivning |
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

| Term | Beskrivning |
| --- | --- |
| Avgifter|Totalt antal Microsoft debiterar för fakturering profilen sedan den senaste faktureringsperioden |
| Eftertexter |Krediter som du har fått från returnerar |
| Azure-krediter tillämpas | Azure-krediter som automatiskt tillämpas på Azure-avgifter varje faktureringsperiod |
| Delsumma |Före skatt beloppet |
| Skatt |Typ och skatt du betalar, beroende på land/region för fakturering-profilen. Om du inte betala skatt kan se du inte skatt på din faktura. |
| Uppskattade totala besparingar |Det uppskattade totala beloppet som du sparade från effektiva rabatter. Om så är tillämpligt, visas effektiva rabatterna nedanför köp posterna i informationen om faktura-avsnittet. |

### <a name="invoice-sections"></a>Faktura-avsnitt

För varje faktura avsnitt under din faktureringsinformation profil visas avgifterna, mängden Azure-krediter tillämpas, skatt och det totala beloppet.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Information om fakturan avsnittet

Informationen visas kostnaden för varje faktura avsnitt uppdelat efter produkt ordning. Inom varje i produktionsorder är kostnaden fördelade på typ av tjänst. Du hittar dagliga avgifterna för dina produkter och tjänster i Azure-portalen och Azure-användning och kostnader CSV. Läs mer i [förstå debiteringar på fakturan för en Microsoft-kundavtal](billing-mca-understand-your-bill.md).

Totalsumman för varje tjänst-familjen beräknas genom att subtrahera *Azure-krediter* från *krediter/avgifter* och lägga till *skatt*:


![Information om fakturan avsnittet](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Term |Beskrivning |
| --- | --- |
| A-pris | Effektiva enhetspriset för tjänsten (pris för valuta) som används för att frekvensen användningen. Detta är unik för en produkt, tjänst-familjen, mätare och erbjudandet. |
| Antal | Kvantitet som har köpt eller förbrukats under fakturaperioden |
| Avgifter/krediter | Summan av kostnader när krediter/återbetalningar tillämpas |
| Azure-kredit | Mängden Azure krediter som tillämpats på avgifter/krediter|
| Skattesats | Skattesatser beroende på land/region |
| Skattebelopp | Skatt tillämpas om du vill köpa utifrån skattesats |
| Totalt | Totalsumman för inköp |

### <a name="how-to-pay"></a>Så här betalar du

Det finns anvisningar för att betala fakturan längst ned på fakturan. Du kan betala med check, under överföring, eller online. Om du betalar online kan använda du ett kredit-eller betalkort eller Azure-krediter, om tillämpligt.

### <a name="publisher-information"></a>Utgivarinformation

Om du har tjänster från tredje part på fakturan, visas namn och adress för varje utgivare längst ned på fakturan.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Förstå debiteringar på din faktureringsinformation profil faktura](billing-mca-understand-your-bill.md)
- [Så här hämtar du information om fakturering och daglig användning från Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Visa din organisations Azure priser](billing-ea-pricing.md)
- [Visa skattedokument för din faktureringsinformation profil](billing-mca-download-tax-document.md)
