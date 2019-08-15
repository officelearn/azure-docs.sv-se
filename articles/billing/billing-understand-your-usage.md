---
title: Förstå din detaljerade användning och dina kostnader | Microsoft Docs
description: Lär dig hur du läser och förstår din detaljerade användning och dina kostnader
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
ms.openlocfilehash: 1842d32a838470d9b2af3a778c44c37464d32294
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954329"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Förstå villkoren i din Azure-användning och avgifts fil

Den detaljerade användnings-och avgifts filen innehåller daglig Beräknad användning baserat på förhandlade priser, inköp (till exempel reservationer, marknads avgifter) och åter betalningar för den angivna perioden.
Avgifter omfattar inte krediter, skatter eller andra avgifter eller rabatter.
Följande tabell beskriver vilka avgifter som ingår för varje kontotyp.

Kontotyp | Azure-användning | Marketplace-användning | Inköp | Åter betalningar
--- | --- | --- | --- | ---
Enterprise-avtal (EA) | Ja | Ja | Ja | Nej
Microsofts kundavtal | Ja | Ja | Ja | Ja
Betala per användning (PAYG) | Ja | Ja | Nej | Nej

Mer information om Marketplace-beställningar (även kallade externa tjänster) finns i [förstå dina avgifter för Azures externa tjänster](billing-understand-your-azure-marketplace-charges.md).

Hämtnings anvisningar finns i [så här hämtar du din fakturerings faktura för Azure och dagliga användnings data](billing-download-azure-invoice-daily-usage-date.md).
Du kan öppna CSV-filen för användning och debitering i Microsoft Excel eller något annat kalkyl blads program.

## <a name="list-of-terms-and-descriptions"></a>Lista över termer och beskrivningar

I följande tabell beskrivs de viktiga termer som används i den senaste versionen av Azures fil för användning och debitering.
Listan täcker PAYG (betala per användning), Enterprise-avtal (EA) och Microsoft Customer Agreement-konton (MCA).

Term | Kontotyp | Beskrivning
--- | --- | ---
Kontonamn | EA, PAYG | Visnings namn för det EA-registrerings kontot eller PAYG fakturerings konto.
AccountOwnerId<sup>1</sup> | EA, PAYG | Unik identifierare för det EA-registrerings kontot eller PAYG fakturerings konto.
Ytterligare information | Alla | Tjänstspecifika metadata. Till exempel en avbildnings typ för en virtuell dator.
BillingAccountId<sup>1</sup> | Alla | Unik identifierare för rot fakturerings kontot.
BillingAccountName | Alla | Namnet på fakturerings kontot.
BillingCurrency | Alla | Valuta som är associerad med fakturerings kontot.
BillingPeriod | EA, PAYG | Debiterings perioden för avgiften.
BillingPeriodEndDate | Alla | Slutdatum för fakturerings perioden.
BillingPeriodStartDate | Alla | Start datumet för fakturerings perioden.
BillingProfileId<sup>1</sup> | Alla | Unikt ID för EA-registrering, PAYG-prenumeration, MCA-fakturerings profil eller AWS-konsoliderat konto.
BillingProfileName | Alla | Namnet på EA-registreringen, PAYG-prenumerationen, MCA-fakturerings profilen eller det konsoliderade AWS-kontot.
ChargeType | Alla | Anger om avgiften representerar användning (**användning**), ett inköp (**inköp**) eller en åter betalning (**åter**betalning).
Använd tjänst | Alla | Namnet på den tjänst som avgiften är kopplad till.
CostCenter<sup>1</sup> | EA, MCA | Det kostnads ställe som har definierats för prenumerationen för spårning av kostnader (endast tillgängligt i öppna fakturerings perioder för MCA-konton).
Kostnad | EA, PAYG | Se CostInBillingCurrency.
CostInBillingCurrency | MCA | Kostnaden för avgiften i fakturerings valutan innan krediter eller skatter.
CostInPricingCurrency | MCA | Kostnaden för avgiften i pris valutan före kredit eller skatt.
Currency | EA, PAYG | Se BillingCurrency.
Datum<sup>1</sup> | Alla | Avgiftens användnings-eller inköps datum.
EffectivePrice | Alla | Blandat enhets pris för perioden. Blandade priser tar ut genomsnitt av eventuella fluktuationer i enhets priset, t. ex. graderad nivå, vilket sänker priset när antalet ökar med tiden.
ExchangeRateDate | MCA | Datum då valutakursen upprättades.
ExchangeRatePricingToBilling | MCA | Växelkurs som används för att konvertera kostnaden i pris valutan till fakturerings valutan.
Frekvens | Alla | Anger om en avgift förväntas upprepas. Avgifter kan antingen ske en gång (**Databasmigrering**), upprepas per månad eller per år (**återkommande**) eller baseras på användning (**UsageBased**).
InvoiceId | PAYG, MCA | Det unika dokument-ID som visas på PDF-filen för fakturor.
InvoiceSection | MCA | Se InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | Unik identifierare för avsnittet EA-avdelning eller MCA-faktura.
InvoiceSectionName | EA, MCA | Namnet på avsnittet EA-avdelning eller MCA-faktura.
IsAzureCreditEligible | Alla | Anger om avgiften är berättigad att betalas för användning av Azure-krediter (värden: Sant, falskt).
Location | MCA | Data Center plats där resursen körs.
Mätarkategori | Alla | Namn på klassificerings kategori för mätaren. Till exempel *moln tjänster* och *nätverk*.
MeterId<sup>1</sup> | Alla | Den unika identifieraren för mätaren.
Mätarnamn | Alla | Namnet på mätaren.
MeterRegion | Alla | Namnet på Data Center platsen för tjänsternas pris beroende på plats. Se plats.
MeterSubCategory | Alla | Namn på under klassificerings kategori för mätare.
OfferId<sup>1</sup> | Alla | Namnet på erbjudandet som köpts.
PartNumber<sup>1</sup> | EA, PAYG | Identifierare som används för att hämta en speciell mätnings prissättning.
PlanName | EA, PAYG | Namn på Marketplace-prenumeration.
PreviousInvoiceId | MCA | Referens till en ursprunglig faktura om det här rad objektet är en åter betalning.
PricingCurrency | MCA | Valuta som används vid klassificering baserat på förhandlade priser.
Produkt | Alla | Produktens namn.
ProductId<sup>1</sup> | MCA | Unikt ID för produkten.
ProductOrderId | Alla | Unikt ID för produkt ordern.
ProductOrderName | Alla | Unikt namn för produkt ordern.
PublisherName | Alla | Publisher för Marketplace-tjänster.
PublisherType | Alla | Typ av utgivare (värden: **Azure**, **AWS**, **Marketplace**).
Antal | Alla | Antalet köpta eller förbrukade enheter.
ReservationId | EA, MCA | Unik identifierare för den köpta reservations instansen.
ReservationName | EA, MCA | Namnet på den köpta reservations instansen.
ResourceGroup | Alla | Namnet på [resurs gruppen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) som resursen finns i.
ResourceId<sup>1</sup> | Alla | Unikt ID för den [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) resursen.
Resursplats | Alla | Data Center plats där resursen körs. Se plats.
ResourceName | EA, PAYG | Namnet på resursen.
Resurstyp | MCA | Typ av resurs instans.
ServiceFamily | MCA | Tjänst familj som tjänsten tillhör.
Tjänstinformation 1 | Alla | Tjänstspecifika metadata.
ServiceInfo2 | Alla | Äldre fält med valfria service-/regionsspecifika metadata.
ServicePeriodEndDate | MCA | Slutdatumet för den klassificerings period som har definierats och låst prissättning för den förbrukade eller köpta tjänsten.
ServicePeriodStartDate | MCA | Start datumet för den klassificerings period som har definierats och låst prissättning för den förbrukade eller köpta tjänsten.
SubscriptionId<sup>1</sup> | Alla | Unikt ID för Azure-prenumerationen.
Prenumerationsnamn | Alla | Namnet på Azure-prenumerationen.
Taggar<sup>1</sup> | Alla | Taggar som tilldelats resursen. Innehåller inte taggar för resurs grupper. Kan användas för att gruppera eller distribuera kostnader för intern åter betalning. Mer information finns i [ordna dina Azure-resurser med Taggar](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Term | Alla | Visar giltighets tiden för erbjudandet. Exempel: I händelse av reserverade instanser visas 12 månader som villkor. För ett engångs köp eller återkommande inköp är termen 1 månad (SaaS, Marketplace-support). Detta gäller inte för Azure-förbrukning.
Måttenhet | Alla | Mått enheten för fakturering av tjänsten. Till exempel debiteras beräknings tjänster per timme.
UnitPrice | EA, PAYG | Priset per enhet för avgiften.

_<sup>**1**</sup> fält som används för att bygga ett unikt ID för en enskild kostnads post._

Observera att vissa fält kan skilja sig åt i skift läge och avstånd mellan konto typer.
Äldre versioner av användnings filerna enligt principen betala per användning innehåller separata avsnitt för utdraget och den dagliga användningen.

### <a name="list-of-terms-from-older-apis"></a>Lista över villkor från äldre API: er
Följande tabell mappar termer som används i äldre API: er till de nya villkoren. Se tabellen ovan för dessa beskrivningar.

Gammal term | Ny term
--- | ---
Använd kvantitet | Antal
IncludedQuantity | Gäller inte
Instans-ID | ResourceId
Avgift | EffectivePrice
Enhet | Måttenhet
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>Se till att avgifterna är korrekta

Om du vill veta mer om detaljerad användning och avgifter kan du läsa om hur du kan förstå din kund avtals faktura [enligt principen betala per](./billing-understand-your-bill.md) användning eller [kund avtal](billing-mca-understand-your-bill.md) .

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Visa och ladda ned din Microsoft Azure faktura](billing-download-azure-invoice.md)
- [Visa och ladda ned Microsoft Azure användning och avgifter](billing-download-azure-daily-usage.md)
