---
title: Sammanfattning för utbetalning av handels Marketplace | Azure Marketplace
description: Sammanfattningen av utbetalningen visar information om de pengar som du har fått med erbjudandet. Du kan också se när du får betalningar och hur mycket du betalar.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 38717ae103ac72d35042ced9bf662d295ed8a29b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931807"
---
# <a name="payout-reporting"></a>Utbetalnings rapportering

[**Sammanfattningen av utbetalningen**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) visar information om de pengar som du har fått av Microsoft. Du kan också se när du får betalningar och hur mycket du betalar.

Om du säljer erbjudanden i Azure Marketplace ser du också information om lyckade utbetalningar i **sammanfattningen av utbetalningen**. Mer information om Azure Marketplace-betalning finns i [principer för Microsoft Azure Marketplace medverkan](https://go.microsoft.com/fwlink/p/?LinkId=722436) och [Microsoft Azure Marketplace utgivar avtal](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> För att bli berättigad till utbetalning måste du ha uppnått [betalnings tröskeln](payment-thresholds-methods-timeframes.md) på $50. Mer information om betalnings tröskeln finns på den här sidan och granska [Microsoft Azure Marketplace Publisher-avtalet](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Roller och behörighet för att få åtkomst till utbetalnings rapporten](#roles-and-permission-to-access-the-payout-report)
- [Utbetalnings rapport: skillnaden mellan Cloud Partner Portal och partner Center](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Kund typer](#customer-types)
- [Relation mellan utbetalning och användning](#corelation-between-payout-and-usage)
- [Hämtning av transaktions historik](#transaction-history-download-export)
- [Fakturerings frågor och support](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Roller och behörighet för att få åtkomst till utbetalnings rapporten

| Rapporter/sidor    | Kontoägare    | Manager  | Developer | Företags deltagare |  Ekonomi deltagare | Rika marknads föraren |
|------------------|------------------|----------|-----------|----|----|-----|
| Anskaffnings rapport (inklusive nära real tids data) | Kan visa | Kan visa | Ingen åtkomst | Ingen åtkomst | Kan visa | Ingen åtkomst |
| Feedback-rapport/svar | Kan visa och skicka feedback | Kan visa och skicka feedback | Kan visa och skicka feedback | Ingen åtkomst | Ingen åtkomst | Kan visa och skicka feedback |
| Hälso rapport (inklusive nära real tids data) | Kan visa | Kan visa | Kan visa | Kan visa | Ingen åtkomst | Ingen åtkomst |
| Användningsrapport | Kan visa | Kan visa | Kan visa | Kan visa | Ingen åtkomst | Ingen åtkomst |
| Konto utbetalning | Kan uppdatera | Ingen åtkomst | Ingen åtkomst | Ingen åtkomst | Kan uppdatera | Ingen åtkomst |
| Skatte profil | Kan uppdatera | Ingen åtkomst | Ingen åtkomst | Ingen åtkomst | Kan uppdatera | Ingen åtkomst |
| Utbetalningssammanfattning | Kan visa | Ingen åtkomst | Ingen åtkomst | Ingen åtkomst | Kan visa | Ingen åtkomst |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Utbetalnings rapport: skillnaden mellan Cloud Partner Portal och partner Center

| | Partnerportalen i molnet | Partnercenter |
|---------|---------|---------|
| Länkar | https://cloudpartner.azure.com/ | https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory och https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| Navigering | Utbetalnings rapportering som tillhandahålls i insikts utbetalning | Utbetalnings rapportering som tillhandahålls i Partner Center – utbetalnings ikon |
| Omfång | <ul> <li>Transaktion per rad objekt är synligt för insamling i arbete, samlas in och betalas </li> <li>Rapportering – visar alla rad artiklar när inköps ordern har skapats, inklusive pågående insamling och fakturering, samt insamlings status och rad artiklar som ännu inte är berättigade att betala. </li> </ul> | <ul> <li>Visar rad artiklarna när de bedöms som berättigade intäkter.</li> <li>Kunderna betalar till Microsoft först och sedan kan ISV: erna se utbetalnings rapporten som startar.</li> <li>Utbetalnings rapporten visar inte pågående insamling och fakturering pågår.  </li> </ul>  |
| Transaktionen är inte klar för utbetalning | Fakturering pågår | Nästa beräknade betalning: status för utbetalning är i tillståndet Unprocessed.  |
| Utbetalnings status |  | Obearbetade <br> Betalningen är berättigad till betalning. Den förblir i det här läget för en kylnings period som definieras i program guiden för stimulans programmet. <br> <br> Planer <br> Betalnings order-genererade interna granskningar innan betalningen bearbetas. <br> <br> Överföra <br> Betalningen har skickats till din bank. |

## <a name="customer-types"></a>Kund typer 

### <a name="enterprise-agreement"></a>Enterprise-avtal

Kunder utan en Enterprise-avtal på plats faktureras per månad för program varu licenser för Marketplace. Kunder med en Enterprise-avtal faktureras per månad via en faktura som presenteras kvartals vis.

### <a name="credit-cards-and-monthly-invoice"></a>Kredit kort och månads faktura

Kunder kan också betala med hjälp av ett kredit kort och en månads faktura. I det här fallet faktureras licens avgifter för program vara varje månad.

### <a name="csp-and-direct-pay-users"></a>CSP och direkta löne användare

Till exempel om kunden köper med ett kredit kort.

## <a name="corelation-between-payout-and-usage"></a>Relation mellan utbetalning och användning 

|Beskrivning    |    Datum  | Beställningar/användning  | Betalning |
|----------|----------|-----------|-------------|
|Order period   | Aug, 15, 2019-Aug 30, 2019 | **Ordning för korrelations attribut** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Användning** <br> <ul> <li>CustomerId </li> <li>Kund namn</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Uppskattad utökad avgift <br> Beräknad utbetalning (PC) </li> </ul> |  |
|Slut period (månad)   | 30 aug 2019 | | |
|Fakturerings datum | 1 september 2019 | | |
|Kund betalnings datum | 1 september 2019 | | |
|Depositions period (endast kredit kort, 30 dagar) | 1 september 2019 – – 30 september 2019 | | **Ordning för korrelations attribut:** <br> <ul><li>AssetId</li> <li>Kund-ID</li> <li> Kund namn</li> </ul> <br> **Användning** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Kund namn</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Utbetalnings status:** Obearbetade |
|Start för samlings period | 1 september 2019 | | |
|Samlings period slut (maximalt 30 dagar) | 30 september 2019 | | |
|Beräknings datum för utbetalning (månatlig på 15) | 1 oktober 2019 | | **Korrelations-attribut** <br> <ul><li>AssetId</li> <li>Kund-ID</li> <li>Kund namn</li> </ul> <br> **Användning** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Kund namn</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Utbetalnings status:** Planer |
|Utbetalnings datum | 15 oktober 2019 | | **Korrelations-attribut** <br> <ul><li>AssetId</li> <li>Kund-ID</li> <li> Kund namn</li> </ul> <br> **Användning** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Kund namn</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Utbetalnings status:** Betalning skickas |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Enterprise Agreement (kvartals Visa/månads kunder)

| Beskrivning |    Datum  | Användning | Betalning |
|----------|----------|---------|-----------|
|Order period | Aug, 15, 2019-Aug 30, 2019 | **Ordning för korrelations attribut** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Användnings rapport** <br> <ul> <li>CustomerId </li> <li>Kund namn</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Uppskattad utökad avgift <br> Beräknad utbetalning (PC) </li> </ul> | |
|Slut period (kvartal) | 30 september 2019 | | |
|Fakturerings datum | 15 oktober 2019 | | |
|Depositions period (endast kredit kort, 30 dagar) | Ej tillämpligt | | |
|Start för samlings period | 15 oktober 2019 | | |
|Endast kredit kort, 30 dagar | Nov 1, 2019 – 30 november 2019 | | |
|Samlings period slut (maximalt 90 dagar) | 15 Jan 2020 | | |
|Kund betalnings datum | 30 december 2019 | | |
|Beräkning av utbetalning | 15 Jan 2020 | | |
|Utbetalnings datum | Feb 15, 2020 | | **För kvartals beroende kunder** <br> <br> **Rapporten order** <br> <ul><li>AssetId</li> <li>Kund-ID</li> <li> Kund namn</li> </ul> <br> **Användning** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Kund namn</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Utbetalnings status:** skickat |

## <a name="transaction-history-download-export"></a>Hämta export av transaktions historik

Det här alternativet innehåller en nedladdning av varje rad objekt som du ser på sidan transaktions historik, typ, datum, associerat transaktions belopp, kund, produkt och annan transaktionell information som gäller för stimulans programmet. 

| Kolumnnamn     | Beskrivning    | 
|-------------|-------------------------------|
| earningId                      | Unikt ID för varje tilltjänande                                                                                                       |
| participantId                  | Den primära identiteten för partnern enligt programmet                                                                            | 
| participantIdType              | De flesta program-ID: n för stimulans program och näringsidkare om för Store-program och Azure Marketplace                                          | 
| participantName                | Partnerns namn                                                                                                              | 
| partnerCountryCode             | Partnerns plats/land                                                                                                  |
| programName                    | Namn på incitament/Store-program                                                                                                             | 
| transactionId                  | Unikt ID för transaktionen                                                                                                    | 
| transactionCurrency            | Valutan i vilken den ursprungliga kund transaktionen ägde rum (den är inte partner platsens valuta)                                     | 
| transactionDate                | Datum för transaktionen. Användbart för program där många transaktioner bidrar till en                                           | 
| transactionExchangeRate        | Växelkurs som används för att visa motsvarande transaktions USD-belopp                                                                 | 
| transactionAmount              | Transaktions belopp i den ursprungliga transaktions valutan baserat på vilken du genererar                                              | 
| transactionAmountUSD           | Transaktions belopp i USD                                                                                                                | 
| handtag                          | Indikerar affärs regel för att tjäna                                                                                                  | 
| earningRate                    | Incitaments grad som tillämpas på transaktions belopp för att generera en tjänande                                                                      | 
| kvantitet                       | Varierar beroende på program. Anger Fakturerat antal för transaktions program                                                            | 
| quantityType                   | Anger typ av kvantitet, till exempel: fakturerad kvantitet, MAU                                                                                     |
| earningType                    | Anger om det är avgift, rabatt, Coop, försäljning osv.                                                                                          | 
| earningAmount                  | Tjänande belopp i den ursprungliga transaktions valutan                                                                                      |
| earningAmountUSD               | Tjäna belopp i USD                                                                                                                    |
| earningDate                    | Datum för den tjänande                                                                                                                      |
| calculationDate                | Datum då intjänaren beräknades i systemet                                                                                            |
| earningExchangeRate            | Växelkurs som används för att visa motsvarande USD-belopp                                                                                  |
| exchangeRateDate               | Valutakurs datum som används för att beräkna EarningAmount USD                                                                                   | 
| paymentAmountWOTax             | Uppskattat belopp (utan skatt) i betala till valuta för "skickade" endast betalningar                                                                 |
| paymentCurrency                | Betala till den valuta som valts av partner i betalnings profilen. Visas endast för skickade betalningar                                                   |
| paymentExchangeRate            | Växelkurs som används för att beräkna paymentAmountWOTax i betalnings valuta med ExchangeRateDate                                            |
| paymentId            | Unikt ID för betalningen. Det här antalet visas i ditt bank utdrag                                            |
| paymentStatus            | Betalnings status                                            |
| paymentStatusDescription            | Egen beskrivning av betalnings status                                            |
| customerId                     | Är alltid tomt                                                                                                                     |
| customerName                   | Är alltid tomt                                                                                                                     |
| partNumber                     | Är alltid tomt                                                                                                                     |
| Namn                    | Produkt namn som är kopplat till transaktionen                                                                                                       |
| productId                      | Unikt produkt-ID                                                                                                                |
| parentProductId                | Unikt ID för överordnad produkt. Obs: om det inte finns någon överordnad produkt för transaktionen, så överordnad produkt-ID = produkt-ID. |
| parentProductName              | Namnet på den överordnade produkten. OBS! om det inte finns någon överordnad produkt för transaktionen, så överordnad produkt namn = produkt namn.   |
| productType                    | Typ av produkt (till exempel app, tillägg, spel osv.)                                                                                        |
| invoiceNumber                  | Faktura nummer (gäller endast EA)                                                                                                  |
| resellerId                     | Åter försäljarens ID                                                                                                                      |
| resellerName                   | Återförsäljarens namn                                                                                                                            |
| transactionType                | Transaktions typ (till exempel köp, åter betalning, återföring, åter betalning osv.)                                                               |
| localProviderSeller            | Lokal leverantör/säljare i post                                                                                                          |
| taxRemitted                    | Moms som remitterats (försäljning, användning eller moms/GST-skatter).                                                                                   |
| taxRemitModel                  | Part som ansvarar för att remittera moms (försäljning, användning eller moms/GST-skatter).                                                                    |
| storeFee                       | Det belopp som Microsoft har bevarat som en avgift för att göra appen eller tillägget tillgängligt i butiken.                                            |
| transactionPaymentMethod       | Kund betalnings instrument som används för transaktionen (till exempel kort, fakturering av mobil företag, PayPal osv.)                                |
| tpan                           | Anger AD-nätverk från tredje part                                                                                                     |
| customerCountry                | Kund land                                                                                                                         |
| customerCity                   | Kund ort                                                                                                                            |
| customerState                  | Kund tillstånd                                                                                                                           |
| customerZip                    | Post nummer för kund                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | Unikt ID för programmet                                                                                                        |
| externalReferenceIdLabel       | Etikett för unikt ID                                                                                                                  |
| transactionCountryCode       | Landskod som transaktionen skedde i                                                                                                                  |
| taxCountry       | Säljs till kund land                                                                                                                  |
| taxState       | Såld till kund delstat                                                                                                                  |
| taxCity       | Såld till kund ort                                                                                                                  |
| taxZipCode       | Säljs till kund post                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Program kod       | Sträng som ska mappas med program namnet                                                                                                                   |
| earningAmountInLastPaymentCurrency       | Värdet i den senaste betalnings valutan (fältet är tomt om inga tidigare betalningar har betalats)                                                                                                                   |
| lastPaymentCurrency       | Senaste betalnings valuta (fältet är tomt om ingen tidigare betalning har betalats)                                                                                                                   |
| AssetId       | Den unika identifieraren för kund order för din Marketplace-tjänst.  Den representerar överförda inköps rad artiklar. Det kan finnas flera till gångar.                                                                                                                   |
| OrderId       | avser en kund faktura                                                                                                                   |
| LineItemId       | enskild rad i en kunds faktura                                                                                                                   |
| Kund land       | Landets namn som tillhandahålls av kunden.  Detta kan skilja sig från landet i en kunds Azure-prenumeration.                                                                                                                   |
| Kund EmailAddress       | E-postadressen som tillhandahålls av slut kunden.  Detta kan skilja sig från e-postadressen i en kunds Azure-prenumeration.                                                                                                                   |
| SkuId       | SKU-ID som definieras under publiceringen. Ett erbjudande kan ha många SKU: er, men en SKU kan bara associeras med ett enda erbjudande.                                                                                                                   |

>[!Note]
>All rapportering och insikter för alternativet Transact Publishing är tillgängliga via avsnittet Insights i avsnittet Cloud Partner Portal eller Analytics i Partner Center.

## <a name="billing-questions-and-support"></a>Fakturerings frågor och support

Om du vill ha hjälp med fakturerings frågor kan du kontakta [supporten för marknads plats utgivare](https://aka.ms/marketplacepublishersupport).
