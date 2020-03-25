---
title: Termer i prisdokumentet för ett Microsoft-kundavtal – Azure
description: Lär dig att läsa och förstå din användning och fakturering för ett Microsoft-kundavtal.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 1e776e351fe723f0fbc5b1d6a8f5593a9fc975b7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199627"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Termer i prisdokumentet för ett Microsoft-kundavtal

Den här artikeln gäller ett Azure-faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

Om du har rollen ägare, deltagare, läsare eller fakturaansvarig för en faktureringsprofil kan du ladda ned organisationens prisdokument från Azure-portalen. Läs mer i [Visa och ladda ned din organisations priser](ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Termer och beskrivningar för prisdokumentet

I följande avsnitt beskrivs de viktigaste termerna i prisdokumentet för ett Microsoft-kundavtal.

| **Fältnamn**   | **Beskrivning**   |
| --- | --- |
| basePrice  | Marknadspriset vid den tidpunkt då kunden loggar in eller marknadspriset när mätaren för tjänsten startar om den här tidpunkten infaller efter inloggningen.   |
| billingAccountId  | Unik identifierare för faktureringskontot.   |
| billingAccountName  | Namnet på faktureringskontot.  |
| billingCurrency | Valutan som avgifterna faktureras i |
| billingProfileId  | Unik identifierare för faktureringsprofilen.   |
| billingProfileName  | Namnet på faktureringsprofilen som har konfigurerats för att ta emot fakturor. Priserna i prisdokumentet är kopplade till den här faktureringsprofilen. |
| currency | Valutan som alla priser återspeglas i. |
| discount | Prisrabatten som erbjuds för examensnivå, kostnadsfri nivå, inkluderad mängd eller förhandlade rabatter i tillämpliga fall. Representeras som en procentandel. |
| effectiveEndDate  | Slutdatumet för det gällande priset. |
| effectiveStartDate  | Startdatumet när priset börjar gälla. |
| includedQuantity | De kvantiteter av en specifik tjänst som en kund har rätt att använda utan ytterligare avgifter. |
| marketPrice | Aktuellt, gällande marknadspris för en specifik tjänst. |
| meterId  | Unik identifierare för mätaren. |
| meterCategory  | Namnet på mätarens klassificeringskategori. Det här kan till exempel vara _Molntjänster_ eller _Nätverk_. |
| meterName  | Namnet på mätaren. Mätaren representerar den distribuerbara resursen för en Azure-tjänst. |
| meterSubCategory  | Namnet på mätarens underkategori.  |
| meterType  |  Namnet på mätartypen. |
| meterRegion  | Namnet på regionen där mätaren för tjänsten är tillgänglig. Anger datacenterplats för vissa tjänster som prissätts beroende på var datacentret ligger.    |
| Produkt  | Namnet på produkten som avgifterna gäller, till exempel Basic SQL DB eller Standard SQL DB  |
| productId  | Unik identifierare för produkten vars mätare förbrukas. |
| productOrderName  | Namnet på den köpta produktplanen. |
| serviceFamily  | Typ av Azure-tjänst, till exempel Compute, Analytics, Security |
| tierMinimumUnits  | Definierar den nedre gränsen för nivåintervallet som priserna gäller. Om intervallet till exempel är 0 till 100 skulle tierMinimumUnits vara 0.  |
| unitOfMeasure  | Anger måttenheten för fakturering av tjänsten. Till exempel faktureras beräkningstjänster per timme. |
| unitPrice  | Pris per enhet vid faktureringstillfället (inte det effektiva blandade priset) som är specifikt för en mätare och ett produktbeställningsnamn.  Obs! Enhetspriset är inte detsamma som det effektiva priset i användningsinformationen när det gäller tjänster som har olika priser för olika nivåer.  När det gäller tjänster med olika priser för olika nivåer så är det effektiva priset en blandning av de olika prisnivåerna och då visas inget nivåspecifikt enhetspris. Det blandade priset eller det effektiva priset är nettopriset för den förbrukade kvantiteten och sträcker sig över flera nivåer (där varje nivå har ett visst enhetspris). |


## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Visa och ladda ned din organisations priser](ea-pricing.md)
