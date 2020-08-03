---
title: Översikt över utbetalnings översikt – Azure Marketplace
description: Sammanfattningen av utbetalningen visar information om de pengar som du har fått med erbjudandet. Du kan också se när du får betalningar och hur mycket du betalar.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 2e7658e965931dd78a6db0e2c9fee36edffff98b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479820"
---
# <a name="payout-summary-overview"></a>Översikt över utbetalningssammanfattning

[Sammanfattningen av utbetalningen](./payout-summary.md) visar information om de pengar som du har fått av Microsoft. Du kan också se när du får betalningar och hur mycket du betalar.

Om du säljer erbjudanden i Azure Marketplace ser du också information om lyckade utbetalningar i sammanfattningen av utbetalningen. Mer information om Azure Marketplace-betalning finns i [principer för att delta i Azure Marketplace](https://docs.microsoft.com/legal/marketplace/participation-policy) och [Microsoft Azure Marketplace Publisher-avtal](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

> [!NOTE]
> För att bli berättigad till utbetalning måste du ha uppnått [betalnings tröskeln](./payment-thresholds-methods-timeframes.md) på $50. Mer information om betalnings tröskeln finns i [Microsoft Azure Marketplace Publisher-avtalet](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

Om du vill visa utbetalnings information loggar du in på [partner Center](https://partner.microsoft.com/dashboard/home) och väljer utbetalnings ikonen i det övre högra hörnet på skärmen:

![Visar utbetalnings ikonen i det övre högra hörnet på Partner Center-portalen.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Roller och behörigheter

Detta är roller och behörigheter för att få åtkomst till utbetalnings rapporten:

| Rapporter/sidor | Kontoinnehavare | Ansvarig | Utvecklare | Företags deltagare | Ekonomi deltagare | Rika marknads föraren |
| --- | --- | --- | --- | --- | --- | --- |
| Anskaffnings rapport (inklusive data i nära real tid) | Kan visa | Kan visa | Ingen åtkomst | Ingen åtkomst | Kan visa | Ingen åtkomst |
| Feedback-rapport/svar | Kan visa och skicka feedback | Kan visa och skicka feedback | Kan visa och skicka feedback | Ingen åtkomst | Ingen åtkomst | Kan visa och skicka feedback |
| --- | --- | --- | --- | --- | --- | --- |
| Hälso rapport (inklusive data i nära real tid) | Kan visa | Kan visa | Kan visa | Kan visa | Ingen åtkomst | Ingen åtkomst |
| Användnings rapport | Kan visa | Kan visa | Kan visa | Kan visa | Ingen åtkomst | Ingen åtkomst |
| Konto utbetalning | Kan uppdatera | Ingen åtkomst | Ingen åtkomst | Ingen åtkomst | Kan uppdatera | Ingen åtkomst |
| Skatte profil | Kan uppdatera | Ingen åtkomst | Ingen åtkomst | Ingen åtkomst | Kan uppdatera | Ingen åtkomst |
| Utbetalningssammanfattning | Kan visa | Ingen åtkomst | Ingen åtkomst | Ingen åtkomst | Kan visa | Ingen åtkomst  |
| | | | | | | |

## <a name="payment-schedules"></a>Betalnings scheman

För en diskussion om betalnings scheman, inklusive anläggnings perioder, partner synlighet och när kunden använder ett kredit kort eller en faktura, se avsnittet [betalnings planer](./payout-policy-details.md#payment-schedules) i avsnittet **utbetalnings information** .

## <a name="transaction-history-download-export"></a>Hämta export av transaktions historik

Det här alternativet ger en nedladdning av varje rad objekt som visas på sidan transaktions historik. Detta inkluderar typ, datum, tillhör ande transaktions belopp, kund, produkt och annan transaktionell information som är relaterad till stimulans programmet.

| Kolumnnamn | Description |
| --- | --- |
| earningId | Unikt ID för varje tilltjänande |
| participantId | Den primära identiteten för partnern enligt programmet |
| participantIdType | Program-ID för stimulans program och näringsidkare om programmet är för Store-program och Azure Marketplace |
| participantName | Partnerns namn |
| partnerCountryCode | Plats/land/region för partnern |
| programName | Namn på incitament/Store-program |
| transactionId | Unikt ID för transaktionen |
| transactionCurrency | Valutan i vilken den ursprungliga kund transaktionen ägde rum (den är inte partner platsens valuta) |
| transactionDate | Datum för transaktionen. Användbart för program där många transaktioner bidrar till en |
| transactionExchangeRate | Växelkurs som används för att visa motsvarande transaktions USD-belopp |
| transactionAmount | Transaktions belopp i den ursprungliga transaktions valutan baserat på vilken du genererar |
| transactionAmountUSD | Transaktions belopp i USD (USD) |
| handtag | Affärs regel för att tjäna |
| earningRate | Incitaments grad som tillämpas på transaktions belopp för att generera en tjänande |
| quantity | Fakturerad kvantitet för transaktions program. Varierar beroende på program |
| quantityType | Typ av kvantitet, till exempel: fakturerad kvantitet, månatliga aktiva användare (MAU) |
| earningType | Anger om det är avgift, rabatt, Coop, Sälj, och så vidare |
| earningAmount | Tjänande belopp i den ursprungliga transaktions valutan |
| earningAmountUSD | Tjäna belopp i USD |
| earningDate | Datum för den tjänande |
| calculationDate | Datum då intjänaren beräknades i systemet |
| earningExchangeRate | Växelkurs som används för att visa motsvarande USD-belopp |
| exchangeRateDate | Valutakurs datum som används för att beräkna EarningAmount USD |
| paymentAmountWOTax | Tjänande belopp (utan skatt) i betala till valuta för &quot; skickade &quot; enbart betalningar |
| paymentCurrency | Betala till den valuta som valts av partner i betalnings profilen. Visas endast för skickade betalningar |
| paymentExchangeRate | Växelkurs som används för att beräkna paymentAmountWOTax i betalnings valuta med ExchangeRateDate |
| paymentId | Unikt ID för betalningen. Det här antalet visas i ditt bank utdrag |
| paymentStatus | Betalningstatus |
| paymentStatusDescription | Beskrivning av betalnings status |
| customerId | Är alltid tomt |
| customerName | Är alltid tomt |
| partNumber | Är alltid tomt |
| Namn | Produkt namn som är kopplat till transaktionen |
| productId | Unikt produkt-ID |
| parentProductId | Unikt ID för överordnad produkt. Om det inte finns någon överordnad produkt för transaktionen, så överordnad produkt-ID = produkt-ID. |
| parentProductName | Namnet på den överordnade produkten. Om det inte finns någon överordnad produkt för transaktionen får du ett överordnat produkt namn = produkt namn. |
| productType | Typ av produkt (till exempel app, tillägg och spel) |
| invoiceNumber | Faktura nummer (endast för företags avtal) |
| resellerId | Åter försäljarens ID |
| resellerName | Återförsäljarens namn |
| transactionType | Transaktions typ (till exempel köp, åter betalning, återföring och åter betalning) |
| localProviderSeller | Lokal leverantör/säljare i post |
| taxRemitted | Moms som remitterats (försäljning, användning eller moms/GST-skatter). |
| taxRemitModel | Part som ansvarar för att remittera moms (försäljning, användning eller moms/GST-skatter). |
| storeFee | Det belopp som Microsoft har bevarat som en avgift för att göra appen eller tillägget tillgängligt på den kommersiella marknaden. |
| transactionPaymentMethod | Kund betalnings instrument som används för transaktionen (till exempel kort, fakturering av mobil företag och PayPal) |
| tpan | AD-nätverk från tredje part |
| customerCountry | Kund land/-region |
| customerCity | Kund ort |
| customerState | Kund tillstånd |
| customerZip | Post nummer för kund |
| TenantID | ID för klienten |
| externalReferenceId | Unikt ID för programmet |
| externalReferenceIdLabel | Etikett för unikt ID |
| transactionCountryCode | Lands nummer/region kod som transaktionen skedde i |
| taxCountry | Kundens land/region |
| taxState | Kundens tillstånd |
| taxCity | Kundens ort |
| taxZipCode | Kundens post nummer |
| LicensingProgramName | Namn på licensierings programmet |
| Program kod | Sträng som ska mappas med program namnet |
| earningAmountInLastPaymentCurrency | Detta belopp i den senaste betalnings valutan (fältet är tomt om inga tidigare betalningar har betalats) |
| lastPaymentCurrency | Senaste betalnings valuta (fältet är tomt om ingen tidigare betalning har betalats) |
| AssetId | Den unika identifieraren för kund order för din Marketplace-tjänst. Den representerar inköps rad artiklarna. Det kan finnas flera till gångar. |
| OrderId | Avser en kund faktura |
| LineItemId | Enskild rad i en kunds faktura |
| Kund land/-region | Landets/regionens namn som tillhandahålls av kunden. Detta kan skilja sig från landet/regionen i en kunds Azure-prenumeration. |
| Kund EmailAddress | E-postadressen som tillhandahålls av kunden. Detta kan skilja sig från e-postadressen i en kunds Azure-prenumeration. |
| SkuId | SKU-ID som definieras under publiceringen. Ett erbjudande kan ha många SKU: er, men en SKU kan bara associeras med ett enda erbjudande. |

> [!NOTE]
> Du hittar all rapportering och insikter för alternativet Transact Publishing i avsnittet Analytics i Partner Center.

## <a name="billing-questions-and-support"></a>Fakturerings frågor och support
Kontakta [supporten för stöd för Microsoft](https://partner.microsoft.com/support/v2/?stage=1)Marketplace för fakturerings support.

## <a name="next-step"></a>Nästa steg

- [Utbetalningssammanfattningar](./payout-summary.md)
