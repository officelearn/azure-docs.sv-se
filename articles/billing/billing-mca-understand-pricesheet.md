---
title: Förstå villkoren i ditt prisdokument för ett Microsoft-kundavtal - Azure
description: Lär dig mer om att läsa och förstå din användningen och fakturan för en Microsoft-kundavtal.
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
ms.openlocfilehash: 4d83228fbec395d604e5ce3f988d2a6157f21eed
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490652"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Villkoren i ditt Microsoft-kundavtal prisdokument

Den här artikeln gäller för en Azure faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

Om du är en fakturering profil ägare, deltagare, läsare eller faktura Manager kan du hämta din organisations prisdokument från Azure-portalen. Se [visa och ladda ned din organisation priser](billing-ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Termer och beskrivningar i din prisdokument

I följande avsnitt beskrivs viktiga termer som visas i din Microsoft kundavtal prisdokument.

| **Fältnamn**   | **Beskrivning**   |
| --- | --- |
| billingAccountId  | Unik identifierare för faktureringskontot.   |
| billingAccountName  | Namnet på faktureringskontot.  |
| billingProfileId  | Unik identifierare för fakturerings-profilen.   |
| billingProfileName  | Namnet på den fakturering profil som är konfigurerade att ta emot fakturor. Priserna i prisdokumentet har associerats med den här fakturering profilen. |
| productOrderName  | Namnet på den köpta produkt-planen. |
| serviceFamily  | Typ av Azure-tjänsten. Exempel: Compute, Analytics, Security |
| Product  | Namnet på den produkt som betalar avgifterna. Exempel: Grundläggande SQL DB mot Standard SQL DB  |
| productId  | Unik identifierare för den produkt som förbrukas vars mätaren. |
| unitOfMeasure  | Identifierar enheterna för fakturering för tjänsten. Beräkningstjänster, till exempel faktureras per timme. |
| meterId  | Unik identifierare för mätaren. |
| meterName  | Namnet på mätaren. Mätaren representerar den distribuerbar resursen för en Azure-tjänst. |
| meterCategory  | Namnet på klassificeringskategori för mätaren. Till exempel _molntjänster_, _nätverk_osv. |
| meterType  |  Namnet på mätaren. |
| meterSubCategory  | Namnet på underkategori för mätning klassificering.  |
| meterRegion  | Namnet på den region där mätaren för tjänsten är tillgänglig. Anger datacenterplats för vissa tjänster som prissätts beroende på var datacentret ligger.    |
| tierId  | Identifierar prisnivån när så är tillämpligt. Detta fungerar tillsammans med tierMinimumUnits för att ställa in nivåindelade priser när priser varierar beroende på hur många enheter som används.    |
| tierMinimumUnits  | Definierar den undre gränsen för nivån intervallet priser har definierats. Om intervallet är 0 till 100, skulle tierMinimumUnits vara 0.  |
| effectiveStartDate  | Startdatum när priset träder i kraft. |
| effectiveEndDate  | Slutdatum för gällande pris. |
| unitPrice  | Pris per enhet vid tidpunkten för fakturering (inte det effektiva blandat priset) som är specifika för en mätare och produkten beställningens namn.  Obs! Enhetspriset är inte densamma som pris i användningsinformation hämtar händelse av tjänster som har differentiell priser på nivåerna.  Vid tjänster med flera nivåer priser gällande pris har en blandad avgift på nivåerna och visar inte en nivå-specifika enhetspriset. Blandat pris eller pris är nettopriset för Använd kvantitet utsträckning över flera nivåer (där varje nivå har ett specifikt a-pris). |
| basePrice  | Marknad priset då kunden loggar in eller marknad priset på gång mätaren tjänsten startar om den efter inloggning.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Visa och hämta din organisations priser](billing-ea-pricing.md)
