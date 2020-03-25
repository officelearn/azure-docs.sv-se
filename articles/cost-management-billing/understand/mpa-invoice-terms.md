---
title: Förstå fakturan för ditt Microsoft-partneravtal i Azure
description: Lär dig att läsa och förstå fakturan för ditt Microsoft-partneravtal i Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: banders
ms.openlocfilehash: 2aca67b5e73bf7f02ac6bca27569265339b0eae6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200171"
---
# <a name="terms-in-your-microsoft-partner-agreement-invoice"></a>Termer på fakturan för ditt Microsoft-partneravtal

Den här artikeln gäller Azure-faktureringskontot för ett Microsoft-partneravtal. [Kontrollera om du har åtkomst till ett Microsoft-partneravtal](#check-access-to-a-microsoft-partner-agreement).

Din faktura innehåller en sammanfattning av dina avgifter och instruktioner för betalning. Du kan ladda ned den i PDF-format (.pdf) från [Azure-portalen](https://portal.azure.com/) eller få den skickad via e-post. Mer information finns i [Visa och ladda ned din Microsoft Azure-faktura](download-azure-invoice.md).

I följande avsnitt anges viktiga termer som står på fakturan samt beskrivningar för respektive term.

## <a name="billing-period"></a>Faktureringsperiod

Du faktureras per månad. Kostnader som inträffar mellan slutet av faktureringsperioden och fakturadatumet inkluderas i nästa månads faktura, eftersom de tillhör nästa faktureringsperiod. Faktureringsperiodens startdatum och slutdatum för varje anges ovanför **Faktureringssammanfattning** i PDF-fakturan.

## <a name="invoice-summary"></a>Sammanfattning av faktura

**Fakturasammanfattningen** finns längst upp på den första sidan och visar information om din faktureringsprofil samt hur du betalar.

<!-- add screenshot -->

| Period | Beskrivning |
| --- | --- |
| Såldes till |Adress för din juridiska person, som finns i egenskaperna för faktureringskonto|
| Fakturera till |Faktureringsadressen för den faktureringsprofil som tar emot fakturan, som finns i egenskaperna för faktureringsprofil|
| Faktureringsprofil |Namnet på den faktureringsprofil som tar emot fakturan |
| IO- nummer |Ett valfritt inköpsordernummer som du tilldelar i spårningssyfte |
| Fakturanummer |Ett unikt fakturanummer som Microsoft genererar i spårningssyfte |
| Fakturadatum |Det datum då fakturan genereras, vanligtvis fem till tolv dagar efter faktureringsperiodens slut. Du kan kontrollera fakturadatumet i egenskaperna för faktureringsprofil.|
| Betalningsvillkor |Hur du betalar för din Microsoft-faktura. *60 dagar netto* innebär att du betalar inom 60 dagar från fakturadatumet. |

## <a name="billing-summary"></a>Faktureringssammanfattning

I **faktureringssammanfattningen** visas kostnaderna för faktureringsprofilen sedan den föregående faktureringsperioden, eventuella krediter som tillämpades, skatt samt det totala restbeloppet.

<!-- add screenshot -->

| Period | Beskrivning |
| --- | --- |
| Avgifter|Totalt antal Microsoft-avgifter för den här faktureringsprofilen sedan den senaste faktureringsperioden |
| Krediter |Krediter som du fått från returer |
| Tillämpade Azure-krediter | Azure-krediter som automatiskt tillämpas på Azure-avgifter varje faktureringsperiod |
| Delsumma |Restbelopp före skatt |
| Skatt |Typ av och belopp för skatt som du betalar, beroende på land/region för din faktureringsprofil. Om du inte behöver betala skatt står ingen skatt på din faktura. |
| Uppskattade totalbesparingar |Den uppskattade totalsumma som du har sparat från effektiva rabatter. Om effektiva rabattsatser är tillämpliga visas de nedanför inköpsradobjekten i Information efter fakturaavsnitt. |


## <a name="billing-details-by-product"></a>Faktureringsinformation per produkt

I avsnittet **Faktureringsinformation per produkt** ser du de totala kostnaderna för varje produkt som är kopplad till faktureringsprofilen. Du kan visa en uppdelning av kostnaden för varje produkt per dag i csv-filen med Azure-användning och kostnader. Om du vill lära dig att stämma av pdf-filen med fakturan mot csv-filen med Azure-användning och kostnader kan du läsa [Förstå fakturan för ditt Microsoft-partneravtal](review-partner-agreement-bill.md).

## <a name="how-to-pay"></a>Så betalar du

Längst ned på fakturan finns instruktioner för hur du betalar faktura. Du kan betala med check eller banköverföring inom 60 dagar från faktureringsdatumet.

## <a name="publisher-information"></a>Utgivarinformation

Om du har tjänster från tredje part på din faktura visas namn och adress för varje utgivare längst ned på fakturan.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Kontrollera åtkomsten till ett Microsoft-partneravtal
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Förstå avgifterna på faktureringsprofilens faktura](review-customer-agreement-bill.md)
- [Så här hämtar du information om fakturering och daglig användning från Azure](../manage/download-azure-invoice-daily-usage-date.md)
- [Visa organisationens Azure-priser](../manage/ea-pricing.md)
- [Visa skattedokument för din faktureringsprofil](mca-download-tax-document.md)
