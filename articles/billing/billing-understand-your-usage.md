---
title: Förstå din detaljerad användning och avgifter | Microsoft Docs
description: Lär dig hur du har läst och förstått detaljerad användning och kostnader
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 9ff9b6b5313026d2102b98659183fa97c6a5ef84
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683989"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Förstå villkoren i din Azure-användning och avgifter

Detaljerad användning och avgifter filen innehåller daglig graderad användning baserat på den förhandlade frekvens, inköp (till exempel reservationer, Marketplace-avgifter) och återbetalningar för den angivna perioden.
Avgifter omfattar inte krediter, skatter, eller andra avgifter eller rabatter.
Följande tabell ges ingår vilka avgifter för varje typ av.

Kontotyp | Azure-användning | Marketplace-användning | Köp | Återbetalningar
--- | --- | --- | --- | ---
Enterprise-avtal (EA) | Ja | Ja | Ja | Nej
Microsofts kundavtal | Ja | Ja | Ja | Ja
Betala per användning (PAYG) | Ja | Nej | Nej | Nej

Läs mer om Marketplace-order (även kallat externa tjänster) i [förstå din Azure externa tjänstavgifter](billing-understand-your-azure-marketplace-charges.md).

Se [så här hämtar du din Azure-fakturering och daglig användningsdata](billing-download-azure-invoice-daily-usage-date.md) anvisningar för hämtning.
Användning och avgifter-filen är tillgänglig i en fil med kommaavgränsade värden (.csv)-filformat som du kan öppna i ett kalkylbladsprogram.

## <a name="list-of-terms-and-descriptions"></a>Lista över villkor och beskrivningar

I följande tabell beskrivs viktiga termer som används i den senaste versionen av filen för Azure-användning och kostnader.
Listan innehåller betala per användning (PAYG), Enterprise Agreement (EA) och Microsoft Customer avtal MCA ()-konton.

Period | Kontotyp | Beskrivning
--- | --- | ---
Kontonamn | EA | Visningsnamn för registreringskontot.
ID för kontoägare | EA | Unik identifierare för registreringskontot.
Ytterligare information | Alla | Tjänstspecifik metadata. Till exempel detta en Avbildningstyp för en virtuell dator.
BillingAccountId | EA, MCA | Unik identifierare för roten fakturering konto.
BillingAccountName | EA, MCA | Namnet på faktureringskontot.
BillingCurrency | EA, MCA | Valuta som är associerade med faktureringskontot.
BillingPeriod | EA | Avgiften för faktureringsperioden.
BillingPeriodEndDate | EA, MCA | Slutdatum för faktureringsperioden.
BillingPeriodStartDate | EA, MCA | Startdatum för faktureringsperioden.
BillingProfileId | EA, MCA | Unik identifierare för EA-avtal eller MCA fakturering profil.
BillingProfileName | EA, MCA | Namnet på EA-avtal eller MCA fakturering profil.
chargeType | EA, MCA | Anger om tillägget representerar användning (**användning**), ett köp (**köpa**), eller en återbetalning (**återbetalning**).
Använd kvantitet | PAYG | Se kvantitet.
Använd tjänst | Alla | Namnet på tjänsten avgiften är kopplad till.
Kostnad | EA | Se CostInBillingCurrency.
Kostnadsställe | EA, MCA | Det kostnadsställe som definierats för prenumerationen för att spåra kostnader (endast tillgängligt i öppna faktureringsperioder för MCA konton).
CostInBillingCurrency | MCA | Kostnaden för tillägget i faktureringsvalutan innan krediter eller skatter.
CostInPricingCurrency | MCA | Kostnaden för tillägget i pris valuta innan krediter eller skatter.
Valuta | PAYG | Se BillingCurrency.
Date | EA, MCA | Användnings- eller köp datum för tillägget.
ExchangeRateDate | MCA | Datum som en valutakurs har upprättats.
ExchangeRatePricingToBilling | MCA | Exchange-hastighet som används för att konvertera kostnaden i pris valuta till valutan.
Frekvens | EA, MCA | Anger om en avgift förväntas ska upprepas. Avgifter kan antingen göras en gång (**OneTime**) Upprepa på basis av per månad eller år (**återkommande**), eller baseras på användning (**UsageBased**).
IncludedQuantity | PAYG | Mängden mätaren som ingår utan kostnad i den aktuella faktureringsperioden.
Instans-ID | PAGY | Se ResourceId.
InvoiceId | EA, MCA | Det unika dokument-ID som visas på fakturan PDF.
invoiceSection | MCA | Se InvoiceSectionName.
InvoiceSectionId | EA, MCA | Unik identifierare för EA-avdelningen eller MCA faktura avsnittet.
InvoiceSectionName | EA, MCA | Namnet på EA-avdelningen eller MCA faktura avsnittet.
IsAzureCreditEligible | EA, MCA | Anger om tillägget är berättigade att betalas för användning av Azure-krediter (värden: True, False).
Location | EA, MCA | Plats för datacenter där resursen körs.
Mätarkategori | Alla | Namnet på klassificeringskategori för mätaren. Till exempel *molntjänster* och *nätverk*.
Mätar-ID | Alla | Den unika identifieraren för mätaren.
Mätarnamn | Alla | Namnet på mätaren.
Mätarregion | Alla | Namnet på datacenterplatsen för tjänster som prissätts baserat på plats. Visa plats.
Underkategori för mätare | Alla | Namnet på underkategori för etablering.
OfferId | EA, MCA | Namnet på erbjudandet har köpt.
PartNumber | EA | Identifierare som används för att få prisinformation om specifika mätaren.
PlanName | EA | Marketplace-planens namn.
PreviousInvoiceId | MCA | Referens till en ursprungliga faktura om den här radartikeln är en återbetalning.
pricingCurrency | MCA | Valutan som används när klassificering utifrån förhandlade priser.
Product | MCA | Se ProductName.
Produkt-ID | EA, MCA | Unik identifierare för produkten.
ProductName | EA | Namnet på produkten.
ProductOrderId | EA, MCA | Unik identifierare för produkten ordningen.
productOrderName | EA, MCA | Unikt namn för den produkt ordningen.
PublisherName | EA, MCA | Utgivare för Marketplace-tjänster.
PublisherType | EA, MCA | Typ av utgivare (värden: firstParty, thirdPartyReseller, thirdPartyAgency).
Kvantitet | EA, MCA | Antalet enheter som har köpt eller förbrukat.
Pris | PAYG | Se Enhetspris.
ReservationId | EA, MCA | Unik identifierare för den köpta reservation-instansen.
ReservationName | EA, MCA | Namnet på den köpta reservation-instansen.
ResourceGroupId | EA, MCA | Unik identifierare för den [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) resursen finns i.
ResourceGroupName | EA, MCA | Namnet på den [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) resursen finns i.
ResourceId | EA, MCA | Unik identifierare för den [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) resurs.
Resursplats | EA, MCA | Plats för datacenter där resursen körs. Visa plats.
ResourceName | EA | Resursens namn.
ResourceType | MCA | Typ av resursinstans.
ServiceFamily | EA, MCA | Tjänsten familj som tillhör tjänsten.
Tjänstinformation 1 | Alla | Tjänstspecifik metadata.
Tjänstinformation 2 | Alla | Äldre fält med valfria tjänstspecifika metadata.
ServicePeriodEndDate | MCA | Slutdatum för klassificering perioden som definieras och låst priser för förbrukade eller köpta-tjänsten.
ServicePeriodStartDate | MCA | Startdatumet för den klassificering perioden som definieras och låst priser för förbrukade eller köpta-tjänsten.
SubscriptionId | Alla | Unikt ID för prenumerationen.
Prenumerationsnamn | Alla | Namnet på prenumerationen.
Taggar | Alla | Taggar som tilldelats till resursen. Omfattar inte gruppen resurstaggar. Kan användas för att gruppera eller fördela kostnader för interna återbetalning. Mer information finns i [ordna dina Azure-resurser med taggar](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Enhet | PAYG | Se UnitOfMeasure.
Måttenhet | Alla | Måttenhet för fakturering för tjänsten. Beräkningstjänster, till exempel faktureras per timme.
UnitPrice | EA | Pris per enhet för tillägget.
UsageDate | PAYG | Visa datum.

Observera att vissa fält kan skiljer sig åt i skiftläge och avstånd mellan kontotyper.
Äldre versioner av användningsfiler för betala per har separata avsnitt för instruktionen och daglig användning.

## <a name="ensure-that-your-charges-are-correct"></a>Kontrollera att dina kostnader är korrekta

Om du vill veta mer om detaljerad användning och avgifter kan läsa om hur du förstår din [användningsbaserad](./billing-understand-your-bill.md) eller [Microsoft kundavtal](billing-mca-understand-your-bill.md) faktura.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Visa och ladda ned Microsoft Azure-faktura](billing-download-azure-invoice.md)
- [Visa och hämta dina Microsoft Azure-användning och kostnader](billing-download-azure-daily-usage.md)
