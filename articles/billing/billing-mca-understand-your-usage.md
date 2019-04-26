---
title: Förstå villkoren på Azure-användning och kostnader CSV för en Microsoft-kundavtal | Microsoft Docs
description: Lär dig hur du har läst och förstått avsnitt av Azure-användning och kostnader CSV för din faktureringsinformation profil
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 8f71f42386ce49d4d7178cb03d28d74edacd7e39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371316"
---
# <a name="understand-terms-on-your-azure-usage-and-charges-csv-for-a-microsoft-customer-agreement"></a>Förstå villkoren på Azure-användning och kostnader CSV för en Microsoft-kundavtal

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

Azure användning och avgifter CSV-filen innehåller avgifter för användning av varje dag och mätare för den aktuella faktureringsperioden.

För att få din Azure-användning och avgifter kan se [visa och ladda ned Azure-användning och kostnader för ditt Microsoft-kundavtal](billing-download-azure-daily-usage.md).
Det är tillgängligt i en fil med kommaavgränsade värden (.csv)-format som kan öppnas i ett kalkylbladsprogram.

Användningsdebitering är det totala antalet **månatliga** debiteringar i en prenumeration. Avgifter för användning beakta inte en kredit eller rabatt.

## <a name="changes-in-the-enterprise-agreement-azure-usage-and-charges-csv"></a>Ändringar i Enterprise Agreement Azure-användning och kostnader CSV

Om du har ett EA-kund, märker du som villkoren i fakturering profilen Azure-användning CSV-filen skiljer sig från villkoren i CSV-filen EA Azure-användning. Här är en mappning av villkoren för EA-användning för perioder Användarvillkor profil:

| EA Azure-användning CSV | Microsoft Customer Agreement Azure-användning och avgifter CSV |
| --- | --- |
| Date | date |
| Månad| date |
| Dag | date |
| År | date |
| Product | produkt |
| Mätar-ID | meterID |
| Mätarkategori | meterCategory |
| Underkategori för mätare | meterSubCategory |
| Mätarregion | meterRegion |
| Mätarnamn | meterName |
| Använd kvantitet | kvantitet |
| Resurskostnad | effectivePrice | <!-- this was highlighted -->
| Utökad kostnad | cost |
| Resursplats | resourceLocation |
| Använd tjänst | consumedService |
| Instans-ID | instanceId |
| Tjänstinformation 1 | serviceInfo1 |
| Tjänstinformation 2 | serviceInfo2 |
| Ytterligare information | additionalInfo |
| Taggar | tags |
| ID för lagringstjänst | Gäller inte |
| Avdelningsnamn | invoiceSection | <!-- this was highlighted -->
| Kostnadsställe | costCenter |
| Måttenhet | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible | <!-- this was highlighted -->

<!-- TO DO: Marketplace CSV? -->

## <a name="detailed-terms-and-descriptions-in-your-azure-usage-and-charges-file"></a>Detaljerade villkor och beskrivningar i din Azure-användning och avgifter

I följande avsnitt beskrivs viktiga termer som visas i Azure-användning och avgifter.

Period | Beskrivning
--- | ---
invoiceId | Det unika dokument-ID som visas på fakturan PDF
previousInvoiceId | Referens till en ursprungliga faktura om den här radartikeln är en återbetalning
billingAccountName | Namnet på kontot fakturering
billingAccountId | Unik identifierare för roten fakturering konto
billingProfileId | Namnet på den fakturering profil som betalar avgifter som faktureras
billingProfileName | Unik identifierare för fakturering profilen som betalar avgifter som faktureras
InvoiceSectionId | Unik identifierare för fakturan
InvoiceSectionName | Namnet på avsnittet faktura
costCenter | Det kostnadsställe som definierats i prenumerationen för att spåra kostnader (endast tillgängligt i öppen faktureringsperioder)
billingPeriodStartDate | Startdatumet för den faktureringsperioden som fakturan skapas
billingPeriodEndDate | Slutdatum för den faktureringsperioden som fakturan skapas
servicePeriodStartDate | Startdatumet för den klassificering perioden som har definierat och låst priser för förbrukade eller köpta-tjänsten
servicePeriodEndDate | Slutdatum för klassificering perioden som har definierat och låst priser för förbrukade eller köpta-tjänsten
date | För Azure Marketplace användningsbaserade avgifter och är detta datumet för klassificering. Detta är inköpsdatum för enstaka inköp (reservationer, Marketplace) eller fasta återkommande avgifter (support erbjuder).
serviceFamily | Service-familj som tillhör tjänsten
productOrderId | Unik identifierare för ordningen som produkt
productOrderName | Unikt namn för ordningen som produkt
consumedService | Namnet på förbrukad tjänst
meterId | Den unika identifieraren för mätaren
meterName | Namnet på mätaren
meterCategory | Namnet på klassificeringskategori för mätaren. Till exempel *molntjänster*, *nätverk*osv.
meterSubCategory | Namnet på underkategori för mätning klassificering
meterRegion | Namnet på den region där mätaren för tjänsten är tillgänglig. Identifierar platsen för datacenter för vissa tjänster som prissätts baserat på datacenterplats.
erbjudande | Namnet på erbjudandet har köpt
productId | Unik identifierare för den produkt som betalar avgifterna
produkt | Namnet på den produkt som betalar avgifterna
prenumerations-ID | Unik identifierare för den prenumeration betalar avgifterna
subscriptionName | Namnet på den prenumeration betalar avgifterna
reservationId | Unik identifierare för den köpta reservation-instansen
reservationName | Namnet på den köpta reservation-instansen
publisherType | Typ av utgivare (värden: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Utgivare för Marketplace-tjänster
resourceGroupId | Unik identifierare för resursgruppen som hör till resursen
resourceGroupName | Namnet på resursgruppen som hör till resursen
resourceId | Unik identifierare för resursinstansen
ResourceType | Typ av resursinstans
resourceLocation | Identifierar platsen för det datacenter där resursen körs.
location | Normaliserade platsen för resursen om olika platser är konfigurerade för samma regioner
kvantitet | Antalet enheter som har köpt eller förbrukat
unitOfMeasure | Måttenhet för fakturering för tjänsten. Beräkningstjänster, till exempel faktureras per timme.
chargeType | Typ av avgift. Värden: <ul><li>AsCharged-Usage: Avgifter som ackumuleras baserat på användning av en Azure-tjänst. Detta inkluderar användning mot virtuella datorer som inte debiteras på grund av reserverade instanser.</li><li>AsCharged-PurchaseMarketplace: Enstaka eller fasta återkommande debiteringar från Marketplace-köp</li><li>AsCharged-UsageMarketplace: Avgifter för Marketplace-tjänster som debiteras baserat på enheter med användning</li></ul>
isAzureCreditEligible | Flagga som anger om tillägget mot tjänsten är berättigade att betalas för användning av Azure-krediter (värden: True, False)
serviceInfo1 | Tjänstspecifik metadata
serviceInfo2 | Äldre fält som samlar in valfria tjänstspecifika metadata
additionalInfo | Ytterligare tjänstspecifika metadata.
tags | Taggar som du tilldelar till resursen

### <a name="how-do-i-make-sure-that-the-charges-in-my-azure-usage-and-charges-file-are-correct"></a>Hur gör jag till att kostnader på min Azure användning och avgifter fil är korrekta?

Om det finns en avgift på din detaljerad användning-fil som du vill ha mer information på, se [förstå debiteringar på din faktureringsinformation profil faktura](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Visa och ladda ned Microsoft Azure-faktura](billing-download-azure-invoice.md)
- [Visa och hämta dina Microsoft Azure-användning och kostnader](billing-download-azure-daily-usage.md)
