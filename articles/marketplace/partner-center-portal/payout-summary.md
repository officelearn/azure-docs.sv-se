---
title: Sammanfattning av utbetalning på marknadsplats | Azure Marketplace
description: Utbetalningssammanfattningen visar information om de pengar du har tjänat med ditt erbjudande. Det låter dig också veta när du får betalningar och hur mycket du kommer att betalas.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 678dc8b058d0ae0694dafeb4222b2fc9f10ecda7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288707"
---
# <a name="payout-reporting"></a>Rapportering av utbetalning

Sammanfattningen [**av utbetalningen**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) visar information om de pengar du har tjänat med Microsoft. Det låter dig också veta när du får betalningar och hur mycket du kommer att betalas.

Om du säljer erbjudanden på Azure Marketplace ser du även information om lyckade utbetalningar i **utbetalningssammanfattningen**. Mer information om Azure Marketplace-betalning finns i [Microsoft Azure Marketplace Participation Policies](https://go.microsoft.com/fwlink/p/?LinkId=722436) och Microsoft Azure Marketplace Publisher [Agreement](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> För att vara berättigad till utbetalning måste dina intäkter nå [betalningströskeln på](payment-thresholds-methods-timeframes.md) $50. Mer information om betalningströskeln finns på den här sidan och läser [Microsoft Azure Marketplace Publisher Agreement](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Roller och behörighet att komma åt utbetalningsrapporten](#roles-and-permission-to-access-the-payout-report)
- [Utbetalningsrapport: skillnaden mellan Cloud Partner Portal och Partner Center](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Kundtyper](#customer-types)
- [Samförhållande mellan utbetalning och användning](#corelation-between-payout-and-usage)
- [Hämtning av transaktionshistorik](#transaction-history-download-export)
- [Faktureringsfrågor och support](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Roller och behörighet att komma åt utbetalningsrapporten

| Rapporter/sidor    | Kontoägare    | Ansvarig  | Developer | Företag bidragsgivare |  Finansbidragsgivare | Marknadsförare |
|------------------|------------------|----------|-----------|----|----|-----|
| Förvärvsrapport (inklusive data i nära realtid) | Kan visa | Kan visa | Ingen åtkomst | Ingen åtkomst | Kan visa | Ingen åtkomst |
| Feedbackrapport/svar | Kan visa och skicka feedback | Kan visa och skicka feedback | Kan visa och skicka feedback | Ingen åtkomst | Ingen åtkomst | Kan visa och skicka feedback |
| Hälsorapport (inklusive data i nära realtid) | Kan visa | Kan visa | Kan visa | Kan visa | Ingen åtkomst | Ingen åtkomst |
| Användningsrapport | Kan visa | Kan visa | Kan visa | Kan visa | Ingen åtkomst | Ingen åtkomst |
| Utbetalningskonto | Kan uppdatera | Ingen åtkomst | Ingen åtkomst | Ingen åtkomst | Kan uppdatera | Ingen åtkomst |
| Skatteprofil | Kan uppdatera | Ingen åtkomst | Ingen åtkomst | Ingen åtkomst | Kan uppdatera | Ingen åtkomst |
| Utbetalningssammanfattning | Kan visa | Ingen åtkomst | Ingen åtkomst | Ingen åtkomst | Kan visa | Ingen åtkomst |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Utbetalningsrapport: skillnaden mellan Cloud Partner Portal och Partner Center

| | Partnerportalen i molnet | Partnercenter |
|---------|---------|---------|
| Länkar | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory)Och[https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Navigering | Utbetalningsrapportering som tillhandahålls i Insights utbetalning | Utbetalningsrapportering i Partner Center – utbetalningsikon |
| Omfång | <ul> <li>Transaktion per radartikel är synlig, för pågående insamling, insamlad och betald </li> <li>Rapportering – visar alla radartiklar när inköpsordern har skapats, inklusive pågående samling och fakturering pågår, och insamlingsstatus och radartiklar som ännu inte är berättigade att betalas. </li> </ul> | <ul> <li>Visar radposterna när de har bedömts som berättigade intäkter.</li> <li>Kunderna betalar först till Microsoft och sedan kan ISV:er se utbetalningsrapporten starta.</li> <li>Utbetalningsrapporten visar inte pågående insamling och fakturering pågår.  </li> </ul>  |
| Transaktionen är inte klar för utbetalning | Fakturering pågår | Nästa beräknade betalning: Utbetalningsstatusen är i obearbetat tillstånd.  |
| Utbetalningsstatus |  | Obearbetade: <br> Intjäningen är berättigad till betalning. Den förblir i det här tillståndet under en kylningsperiod enligt definitionen i programguiden för incitamentsprogrammet. <br> <br> Kommande: <br> Betalningsordergenererad väntande interna granskningar innan betalningen bearbetas. <br> <br> Skickas: <br> Betalningen har skickats till din bank. |

## <a name="customer-types"></a>Kundtyper

### <a name="enterprise-agreement"></a>Företagsavtal

Kunder utan ett företagsavtal faktureras månadsvis för marknadsplatsprogramlicenser. Kunder med ett företagsavtal faktureras månadsvis via en faktura som presenteras kvartalsvis.

### <a name="credit-cards-and-monthly-invoice"></a>Kreditkort och månadsfaktura

Kunder kan också betala med kreditkort och månadsfaktura. I det här fallet debiteras dina licensavgifter för programvara månadsvis.

### <a name="csp-and-direct-pay-users"></a>CSP- och Direct Pay-användare

Om kunden till exempel köper med kreditkort.

## <a name="corelation-between-payout-and-usage"></a>Samförhållande mellan utbetalning och användning

|Beskrivning    |    Datum  | Order/användning  | Utbetalning |
|----------|----------|-----------|-------------|
|Orderperiod   | 15 augusti 2019 - 30 augusti 2019 | **Order för korrelationsattribut** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Användning** <br> <ul> <li>CustomerId </li> <li>Kundens namn</li> <li>(UsageReference) InköpRecordId/LineItemId</li> <li> Uppskattad utökad laddning <br> Beräknad utbetalning (PC) </li> </ul> |  |
|Terminsslut (månad)   | 30 augusti 2019 | | |
|Faktureringsdatum | den 1 september 2019 | | |
|Betalningsdatum för kund | den 1 september 2019 | | |
|Escrow Period (endast kreditkort, 30 dagar) | 1 september 2019 - 30 september 2019 | | **Order för korrelationsattribut:** <br> <ul><li>AssetId (olika)</li> <li>Kund-ID</li> <li> Kundens namn</li> </ul> <br> **Användning** <br> <ul> <li>AssetId (olika)</li> <li>CustomerId</li> <li>Kundens namn</li> <li>OrderId</li> <li>LineItemId (På Andra plats)</li> <li>transaktionAmount</li> <li>TjänaAmountInLastPaymentCurrency</li> </ul> <br> **Utbetalningsstatus:** Obearbetade |
|Start av insamlingsperiod | den 1 september 2019 | | |
|Insamlingsperiodslut (max, 30 dagar) | den 30 september 2019 | | |
|Utbetalningsberäkningsdatum (månadsvis den 15:e) | den 1 oktober 2019 | | **Korrelationsattribut** <br> <ul><li>AssetId (olika)</li> <li>Kund-ID</li> <li>Kundens namn</li> </ul> <br> **Användning** <br> <ul> <li>AssetId (olika)</li> <li>CustomerId</li> <li>Kundens namn</li> <li>OrderId</li> <li>LineItemId (På Andra plats)</li> <li>transaktionAmount</li> <li>TjänaAmountInLastPaymentCurrency</li> </ul> <br> **Utbetalningsstatus:** Kommande |
|Utbetalningsdatum | den 15 oktober 2019 | | **Korrelationsattribut** <br> <ul><li>AssetId (olika)</li> <li>Kund-ID</li> <li> Kundens namn</li> </ul> <br> **Användning** <br> <ul> <li>AssetId (olika)</li> <li>CustomerId</li> <li>Kundens namn</li> <li>OrderId</li> <li>LineItemId (På Andra plats)</li> <li>transaktionAmount</li> <li>TjänaAmountInLastPaymentCurrency</li> </ul> <br> **Utbetalningsstatus:** Betalning skickad |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Enterprise-avtal (kvartals-/månadskunder)

| Beskrivning |    Datum  | Användning | Utbetalning |
|----------|----------|---------|-----------|
|Orderperiod | 15 augusti 2019 - 30 augusti 2019 | **Order för korrelationsattribut** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Användningsrapport** <br> <ul> <li>CustomerId </li> <li>Kundens namn</li> <li>(UsageReference) InköpRecordId/LineItemId</li> <li> Uppskattad utökad laddning <br> Beräknad utbetalning (PC) </li> </ul> | |
|Löptidsslut (kvartal) | den 30 september 2019 | | |
|Faktureringsdatum | den 15 oktober 2019 | | |
|Escrow Period (endast kreditkort, 30 dagar) | Saknas | | |
|Start av insamlingsperiod | den 15 oktober 2019 | | |
|Endast kreditkort, 30 dagar | 1 november 2019 - 30 november 2019 | | |
|Insamlingsperiodslut (max, 90 dagar) | den 15 januari 2020 | | |
|Betalningsdatum för kund | den 30 december 2019 | | |
|Beräkning av utbetalning | den 15 januari 2020 | | |
|Utbetalningsdatum | Den 15 februari 2020 | | **För kvartalsbaserade kunder** <br> <br> **Orderrapport** <br> <ul><li>AssetId (olika)</li> <li>Kund-ID</li> <li> Kundens namn</li> </ul> <br> **Användning** <br> <ul> <li>AssetId (olika)</li> <li>CustomerId</li> <li>Kundens namn</li> <li>OrderId</li> <li>LineItemId (På Andra plats)</li> <li>transaktionAmount</li> <li>TjänaAmountInLastPaymentCurrency</li> </ul> <br> **Utbetalningsstatus:** skickas |

## <a name="transaction-history-download-export"></a>Nedladdning av transaktionshistorik

Det här alternativet ger en hämtning av varje intjäningsradsartikel som visas på sidan Transaktionshistorik, intjäningstyp, datum, associerat transaktionsbelopp, kund, produkt och annan transaktionsinformation som gäller för incitamentsprogrammet.

| Kolumnnamn     | Beskrivning    |
|-------------|-------------------------------|
| tjänarId                      | Unik identifierare för varje intjäning                                                                                                       |
| deltagareD                  | Den primära identiteten för den partner som tjänar inom ramen för programmet                                                                            |
| deltagareIdType              | Mestadels program-ID för incitamentsprogram och säljare IF för Store-program och Azure Marketplace                                          |
| deltagareNamn                | Den intjänande partnerns namn                                                                                                              |
| partnerCountryCode             | Den intjänstpartnerns läge/land                                                                                                  |
| programNamn                    | Namn på incitament/butiksprogram                                                                                                             |
| transaktionId                  | Unik identifierare för transaktionen                                                                                                    |
| transaktionValuta            | Valuta där den ursprungliga kundtransaktionen inträffade (det är inte partnerplatsvalutan)                                     |
| transaktionDate                | Datum för transaktionen. Användbart för program där många transaktioner bidrar till en intjäning                                           |
| transaktionsbyte        | Valutakurs som används för att visa motsvarande transaktion USD belopp                                                                 |
| transaktionAmount              | Transaktionsbelopp i den ursprungliga transaktionsvalutan baserat på vilken intjäning genereras                                              |
| transaktionAmountUSD           | Transaktionsbelopp i USD                                                                                                                |
| Spaken                          | Anger affärsregel för intjänings-                                                                                                  |
| tjänaNdeStra                    | Incitamentsränta som tillämpas på transaktionsbelopp för att generera en intjäning                                                                      |
| quantity                       | Varierar beroende på program. Anger fakturerad kvantitet för transaktionsprogram                                                            |
| kvantitetstyp                   | Anger typ av kvantitet, till exempel: Fakturerad kvantitet, MAU                                                                                     |
| inkomstTyp                    | Anger om det är avgift, rabatt, coop, sälja etc.                                                                                          |
| tjänaAmount                  | Intjäningsbelopp i den ursprungliga transaktionsvalutan                                                                                      |
| tjänaAmountUSD               | Intjäningsbelopp i USD                                                                                                                    |
| tjänaDate                    | Datum för intjäning                                                                                                                      |
| beräkningDate                | Datum då intjäningen beräknades i systemet                                                                                            |
| tjänaExchangeRate            | Valutakurs som används för att visa motsvarande USD-belopp                                                                                  |
| exchangeRateDate               | Valutakursdatum som används för att beräkna IntjäningSgenomstj.S                                                                                   |
| betalningAmountWOTax             | Intjäningsbelopp (utan skatt) i betala till valuta för "Skickade" betalningar endast                                                                 |
| betalningValuta                | Betala till valuta som valts av partner i betalningsprofilen. Visas endast för skickade betalningar                                                   |
| betalningVäxlar            | Valutakurs som används för att beräkna betalningAmountWOTax i betalningsvaluta med ExchangeRateDate                                            |
| paymentId (på)            | Unik identifierare för betalningen. Detta nummer visas i ditt kontoutdrag                                            |
| betalningStatus            | Betalningsstatus                                            |
| betalningStatusBeskrivning            | Vänskaplig beskrivning av betalningsstatus                                            |
| customerId                     | Kommer alltid att vara tom                                                                                                                     |
| kundNamn                   | Kommer alltid att vara tom                                                                                                                     |
| partNumber                     | Kommer alltid att vara tom                                                                                                                     |
| Productname                    | Produktnamn kopplat till transaktionen                                                                                                       |
| productId                      | Unik produktidentifierare                                                                                                                |
| parentProductId                | Unik överordnad produktidentifierare. Om det inte finns någon överordnad produkt för transaktionen kan du ange ett produkt-ID = Produkt-ID. |
| parentProductName              | Namnet på den överordnade produkten. Om det inte finns någon överordnad produkt för transaktionen kan du ange det överordnade produktnamnet = produktnamnet.   |
| productType                    | Typ av produkt (t.ex. app, tillägg, spel, etc.)                                                                                        |
| fakturaNummer                  | Fakturanummer (gäller endast för EA)                                                                                                  |
| återförsäljare                     | Identifierare för återförsäljare                                                                                                                      |
| återförsäljareNamn                   | Återförsäljarens namn                                                                                                                            |
| transaktionstyp                | Typ av transaktion (t.ex. inköp, återbetalning, återföring, återbetalning osv.)                                                               |
| lokalaProviderSeller            | Lokal leverantör/säljare av posten                                                                                                          |
| skattRemitterad                    | Momsbelopp som överförs (moms-, användnings- eller moms-/momsskatter).                                                                                   |
| skattRemitModel                  | Part som ansvarar för att försänka skatter (moms, användning eller moms/moms).                                                                    |
| storeFee                       | Det belopp som Microsoft behåller som en avgift för att göra appen eller tillägget tillgängligt i Store.                                            |
| transaktionBetalningMehod       | Kundens betalningsinstrument som används för transaktionen (t.ex. kort, mobiloperatörsfakturering, PayPal osv.)                                |
| tpan (tpan)                           | Anger annonsnätverket från tredje part                                                                                                     |
| kundLand                | Kundland                                                                                                                         |
| customerCity (kundCity)                   | Kundstad                                                                                                                            |
| customerState (kundState)                  | Kundens tillstånd                                                                                                                           |
| kundZip                    | Kundpostnummer                                                                                                                 |
| TenantID (Klient-ID)                       |                                                                                                                                          |
| externreferensId            | Unik identifierare för programmet                                                                                                        |
| externreReferensIdLabel       | Unik identifierare etikett                                                                                                                  |
| transaktionCountryCode       | Landskod där transaktionen inträffade                                                                                                                  |
| skattLand       | Säljs till kundland                                                                                                                  |
| taxState (skattestat)       | Såld till kundtillstånd                                                                                                                  |
| taxCity (skatteStad)       | Såld till Customer City                                                                                                                  |
| skattZipCode       | Säljs till kund zip                                                                                                                  |
| LicensprogramNamn       |                                                                                                                   |
| Programkod       | Sträng för att mappa med programmets namn                                                                                                                   |
| tjänaAmountInLastPaymentCurrency       | Intjäningsbelopp i den senaste betalningsvalutan (fältet kommer att vara tomt, om inga tidigare betalningar har betalats)                                                                                                                   |
| senaste betalningValuta       | Senaste betalningsvalutan (fältet kommer att vara tomt, om ingen tidigare betalning har betalats)                                                                                                                   |
| AssetId (olika)       | Den unika identifieraren för kundorder för din marknadsplatstjänst.  Den representerar de transagerade inköpsradartiklarna. Det kan finnas flera tillgångar.                                                                                                                   |
| OrderId       | avser en kunds faktura                                                                                                                   |
| LineItemId (På Andra plats)       | enskild rad i en kunds faktura                                                                                                                   |
| Kundland       | Det landsnamn som kunden tillhandahåller.  Detta kan vara annorlunda än landet i en kunds Azure-prenumeration.                                                                                                                   |
| Kundens e-postadress       | Den e-postadress som slutkunden har angett.  Detta kan skilja sig från e-postadressen i en kunds Azure-prenumeration.                                                                                                                   |
| SkuId (på)       | SKU-ID enligt definitionen under publicering. Ett erbjudande kan ha många SKU: er, men en SKU kan bara associeras med ett enda erbjudande.                                                                                                                   |

>[!Note]
>All rapportering och insikter för alternativet för transactpublicering är tillgängliga via avsnittet Insikter i avsnittet Cloud Partner Portal eller Analytics i Partner center.

## <a name="billing-questions-and-support"></a>Faktureringsfrågor och support

Om du vill ha hjälp med faktureringsfrågor kontaktar du [supporten för marknadsplatsutgivare.](https://aka.ms/marketplacepublishersupport)
