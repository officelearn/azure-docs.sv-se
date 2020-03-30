---
title: Termer relaterade till Azure-användning och -avgifter för Microsoft-kundavtal
description: Lär dig hur du läser och förstår avsnitten i CSV-filen för Azure-användning och -avgifter för din faktureringsprofil.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: banders
ms.openlocfilehash: c6883dfd0f4c3bc11afdd68ead8c8c84729b1880
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80065693"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Termer i filen för Azure-användning och -avgifter för ett Microsoft-kundavtal

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

CSV-filen för Azure-användning och -avgifter innehåller dagliga och mätarnivåbaserade användningsavgifter för den aktuella faktureringsperioden.

Information om hur du hämtar filen med Azure-användning och -avgifter finns i artikeln om hur du [visar och laddar ned Azure-användning och -avgifter för ditt Microsoft-kundavtal](download-azure-daily-usage.md). Den är tillgänglig i filformatet .csv (kommaavgränsade värden), som du kan öppna i ett kalkylbladsprogram.

Användningsavgifter är de totala **månatliga** avgifterna för en prenumeration. Användningsavgifterna tar inte hänsyn till krediter eller rabatter.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Ändringar från Azure EA-användning och -avgifter

Om du var EA-kund ser du att termerna i CSV-filen med användning av Azure-faktureringsprofil är annorlunda jämfört med termerna i CSV-filen för Azure EA-användning. Här är en mappning av termer för EA-användning till termer för faktureringsprofilsanvändning:

| CSV med Azure EA-användning | CSV med Azure-användning och -avgifter för Microsoft-kundavtal |
| --- | --- |
| Datum | date |
| Month| date |
| Dag | date |
| År | date |
| Produkt | produkt |
| MeterId | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Taggar | tags |
| StoreServiceIdentifier | Ej tillämpligt |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Detaljerade termer och beskrivningar

Följande termer visas i filen med Azure-användning och -avgifter.

Period | Beskrivning
--- | ---
invoiceId | Det unika dokument-ID som anges i PDF-filen för faktura
previousInvoiceId | Referens till en ursprunglig faktura om det här radobjektet är en återbetalning
billingAccountName | Namnet på faktureringskontot
billingAccountId | Unik identifierare för rotfaktureringskontot
billingProfileId | Namnet på den faktureringsprofil som de avgifter som faktureras gäller
billingProfileName | Unik identifierare för den faktureringsprofil som de avgifter som faktureras gäller
invoiceSectionId | Unik identifierare för fakturaavsnittet
invoiceSectionName | Namn på fakturaavsnittet
costCenter | Det kostnadsställe som definierats för prenumerationen för spårning av kostnader (endast tillgängligt i öppna faktureringsperioder)
billingPeriodStartDate | Startdatumet för den faktureringsperiod som fakturan genereras för
billingPeriodEndDate | Slutdatumet för den faktureringsperiod som fakturan genereras för
servicePeriodStartDate | Startdatumet för den klassificeringsperiod som har definierat och låst prissättning för den förbrukade eller köpta tjänsten
servicePeriodEndDate | Slutdatumet för den klassificeringsperiod som har definierat och låst prissättning för den förbrukade eller köpta tjänsten
date | För användningsbaserade Azure- och Marketplace-avgifter är detta klassificeringsdatumet. För engångsköp (reservationer, Marketplace) eller fasta återkommande avgifter (supporterbjudanden) är detta inköpsdatumet.
serviceFamily | Den tjänstfamilj som tjänsten tillhör
productOrderId | Unik identifierare för produktbeställningen
productOrderName | Unikt namn för produktbeställningen
consumedService | Namnet på den förbrukade tjänsten
meterId | Unik identifierare för mätaren
meterName | Namnet på mätaren
meterCategory | Namnet på mätarens klassificeringskategori. Det här kan till exempel vara *Molntjänster* eller *Nätverk*.
meterSubCategory | Namnet på mätarens underkategori
meterRegion | Namnet på den region där mätaren för tjänsten är tillgänglig. Anger datacentrets plats för vissa tjänster som prissätts beroende på var datacentret ligger.
offer | Namnet på det köpta erbjudandet
PayGPrice | Resursens butikspris.
PricingModel | Identifierare som anger hur mätarens priser bestäms (värden: på begäran, reservation, punkt)
productId | Unik identifierare för den produkt som avgifterna gäller
produkt | Namnet på den produkt som avgifterna gäller
subscription ID | Unik identifierare för den prenumeration som avgifterna gäller
subscriptionName | Namn på den prenumeration som avgifterna gäller
reservationId | Unik identifierare för den köpta reservationsinstansen
reservationName | Namn på den köpta reservationsinstansen
publisherType | Typ av utgivare (värden: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Utgivare för Marketplace-tjänster
resourceGroupId | Unik identifierare för den resursgrupp som är associerad med resursen
resourceGroupName | Namn på den resursgrupp som är associerad med resursen
resourceId | Unik identifierare för resursinstansen
resourceType | Typ av resursinstans
resourceLocation | Identifierar platsen för det datacenter där resursen körs.
location | Normaliserad plats för resursen om olika resursplatser konfigureras för samma regioner
quantity | Antalet köpta eller förbrukade enheter
unitOfMeasure | Måttenheten för fakturering av tjänsten. Till exempel faktureras beräkningstjänster per timme.
chargeType | Typen av avgift. Värden: <ul><li>AsCharged-Usage: Avgifter som ådras baserat på användning av en Azure-tjänst. Detta inkluderar användning mot virtuella datorer som inte debiteras på grund av reserverade instanser.</li><li>AsCharged-PurchaseMarketplace: Antingen engångsavgifter eller fasta återkommande avgifter från Marketplace-inköp</li><li>AsCharged-UsageMarketplace: Avgifter för Marketplace-tjänster som debiteras baserat på förbrukningsenheter</li></ul>
isAzureCreditEligible | Flagga som anger huruvida avgiften mot tjänsten är berättigad att betalas för med hjälp av Azure-krediter (värden: True, False)
serviceInfo1 | Tjänstspecifika metadata
serviceInfo2 | Ett äldre fält som registrerar valfria tjänstspecifika metadata
additionalInfo | Ytterligare tjänstspecifika metadata.
tags | Taggar som du tilldelar till resursen

### <a name="make-sure-that-charges-are-correct"></a>Kontrollera att avgifterna är korrekta

Om du vill säkerställa att avgifterna i den detaljerade användningsfilen är korrekta kan du verifiera dem. Se [Förstå avgifterna på faktureringsprofilens faktura](review-customer-agreement-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Visa och ladda ned din Microsoft Azure-faktura](download-azure-invoice.md)
- [Visa och ladda ned information om din användning och dina avgifter för Microsoft Azure](download-azure-daily-usage.md)
