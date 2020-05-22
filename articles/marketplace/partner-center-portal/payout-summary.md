---
title: Utbetalnings rapporter – Microsoft Commercial Marketplace
description: Utbetalnings rapporterna visar information om de pengar du har fått för ditt erbjudande, inklusive betalnings belopp och när du betalar.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6639026d071b496027996036a81f6bc66e0185e3
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744813"
---
# <a name="payout-summaries"></a>Utbetalningssammanfattningar

Sammanfattningen av utbetalningen visar information om de pengar som du har fått av Microsoft. Du kan också se när du får betalningar och hur mycket du betalar.

Om du säljer produkter på Azure Marketplace kan du också se information om lyckade utbetalningar i sammanfattningen av utbetalningen. Mer information finns i [principer för Microsoft Azure Marketplace medverkan](https://docs.microsoft.com/legal/marketplace/participation-policy) och [Microsoft Azure Marketplace utgivar avtal](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

> [!NOTE]
> För att bli berättigad till utbetalning måste du ha uppnått [betalnings tröskeln](payment-thresholds-methods-timeframes.md) på $50. Om du vill ha mer information om betalnings tröskeln ser du den här sidan och granskar appens utvecklings avtal.

> [!NOTE]
> Om du letar efter stöd för utbetalningar, inklusive hur du konfigurerar utbetalnings konton, saknade inbetalningar, påträffar undantag eller annat, kontaktar du support [här](https://developer.microsoft.com/windows/support).

## <a name="access-the-payout-summary-pages"></a>Få åtkomst till sammanfattnings sidorna för utbetalning

Öppna en av sammanfattnings sidorna för utbetalning:

1. Välj utbetalnings ikonen i det övre högra hörnet.
2. Välj transaktions historik, betalningar eller exportera data.

## <a name="transaction-history-page"></a>Sidan transaktions historik

På den här sidan visas alla dina enskilda intäkter, inklusive datum, typ och för var och en. Du kan välja en tids period att visa och du kan också filtrera efter registrerings-ID, program, betalnings-ID, typ, Laffont och status. Data är tillgängliga för innevarande räkenskapsår (1 juli – 30 juni) och de föregående två räkenskapsåren.

Om du vill se mer information om ett som du väljer väljer du nedåtpilen till höger på sidan. Detta visar spaken, intäkts belopp och produkt. Om någon av dessa data inte är tillgänglig men du behöver åtkomst till den, kan du kontakta [supporten](https://developer.microsoft.com/windows/support). Om resultatet är resultatet av en justering och inte en transaktion, kommer produkt fälten inte att visas.

Om du vill exportera någon av transaktions data på den här sidan använder du sidan **Exportera data** .

## <a name="payments-page"></a>Sidan betalningar

Summorna på den här sidan representerar alla de program som du deltar i. Du kan filtrera efter deltagar-ID, program, betalnings-ID och typ av betalning. Belopp anges i amerikanska dollar. Det betalda värdet visas också i betala till valuta.

| Område                   | Beskrivning                                                                                |
|------------------------|---------------------------------------------------------------------------------------------|
| Totalt betalat i år   | Den sammanlagda summan betalas ut till dig detta år, i amerikanska dollar, för alla dina program       |
| Nästa beräknade betalning | Den enda nästa betalningen kommer till dig (även om andra kommer snart) i amerikanska dollar |
| Senaste betalning           | Mängden (i USD), program namn och program för din senaste betalning           |
| Betalningar per källa     | Belopp för betalningar (i USD) som representeras av programmet under de senaste 12 månaderna           |
| Betalningar               | Välj **betald** eller **väntande** och sortera sedan efter behov. Om du vill ha mer information om en speciell betalning väljer du **Visa**. Om du vill ladda ned en kopia av betalnings remitterings instruktionen väljer du **Hämta**. Eftersom transaktions historik data kan ta upp till 24 timmar innan de visas, kan det hända att du inte ser de associerade intäkterna direkt. |
|||

Om du vill exportera någon av data på den här sidan väljer du **Exportera** och följer anvisningarna på sidan Exportera data.

## <a name="transaction-history-page"></a>Sidan transaktions historik

På den här sidan visas alla dina enskilda intäkter, inklusive datum, typ och för var och en. Du kan välja en tids period att visa och du kan filtrera efter registrerings-ID, program, betalnings-ID, typ, spaken och status. Data är tillgängliga för innevarande räkenskapsår (1 juli – 30 juni) och de föregående två räkenskapsåren.

Om du vill se mer information om ett som du väljer väljer du nedåtpilen till höger på sidan. Detta visar spaken, intäkts belopp och produkt. Om någon av dessa data inte är tillgänglig men du behöver åtkomst till den, kontaktar du [supporten](https://developer.microsoft.com/windows/support)]. Om resultatet är resultatet av en justering och inte en transaktion, kommer produkt fälten inte att visas.

Om du vill exportera någon av transaktions data på den här sidan väljer du **Exportera** och följer anvisningarna på sidan Exportera data. Filer som exporteras från sidan transaktions historik visar data i transaktions valuta, intäkter i både transaktions valuta och US-dollar och det betalda värdet i betala till valuta.

## <a name="payment-status"></a>Betalningstatus

| Status för att tjäna           | Anledning                                                                                                                                      | Krävs partner åtgärd?                                   |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| Obearbetade              | Betalningen är berättigad till betalning. Den förblir i det här läget för en kylnings period som definieras i program guiden för stimulans programmet. | Nej                                                         |
| Planer                 | Betalnings order som genererats som väntande interna granskningar innan betalningen bearbetas                                                               | Nej                                                         |
| Väntande moms faktura      | Din moms faktura är ofullständig eller ogiltig                                                                                                  | Du måste uppdatera din moms faktura innan du kan betala |
| Avvisad under granskning   | Betalningen avvisades under granskningen                                                                                                     | Kontakta [Microsoft Support](https://developer.microsoft.com/windows/support) om du vill ha mer information                      |
| Misslyckades                   | Betalningen misslyckades på grund av ett fel i Microsoft-systemet                                                                                         | Kontakta [Microsoft Support](https://developer.microsoft.com/windows/support) om du vill ha mer information                      |
| Pågår              | Betalningen pågår                                                                                                                 | Nej                                                         |
| Felaktig betalning        | Betalnings återkoppling pågår                                                                                                       | Nej                                                         |
| Skickat                     | Betalningen har skickats till din bank                                                                                                     | Nej                                                         |
| Ombearbetning             | Ett Microsoft-systemfel påträffades under betalningen och ombearbetas                                                                  | Nej                                                         |
| Reversed                 | Betalningen återfördes av banken och skickas igen vid nästa betalnings cykel                                                     | Nej                                                         |
| Avvisad moms faktura     | Din moms faktura avvisades under granskningen. Alla väntande betalningar stoppas tills moms granskningen är klar.                 | Kontakta [Microsoft Support](https://developer.microsoft.com/windows/support) om du vill ha mer information                      |
| Moms faktura under granskning | Din moms faktura granskas. Din betalning frigörs när moms fakturan har godkänts.                                   | Nej                                                         |
| Avvisad                 | Betalningen avvisades av din bank                                                                                                      | Kontakta din bank om du vill ha mer information.                             |
|||

## <a name="export-data-page"></a>Sidan exportera data

Följ dessa anvisningar för att exportera data.

Sidan exportera data uppdateras inte på egen hand. Du kan behöva uppdatera sidan manuellt för att se de senaste data.

Filtret kan resultera i att det **inte finns några tillgängliga data** fel. Detta innebär förmodligen att du har lämnat standard tids perioden som valts vid tre månader och sedan valt ett betalnings-ID från ett som ligger utanför den perioden. Expandera din tids period och försök igen.

## <a name="payments"></a>Betalningar

![Exportera betalningar](./media/pc-export-payments.png)

Med det här alternativet får du en hämtning av de betalningar som du har fått i din bank för ett visst program, tillhör ande skatt och aggregerat belopp. Den här rapporten används för många Partner Center-program, så vissa kolumner kan vara inaktuella för rapporten. Dessa kolumner markeras nedan.

| Kolumnnamn              | Beskrivning                                                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------------  |
| participantID            | Den primära identiteten för partnern enligt programmet                                                                             |
| participantIDType        | Vanligt vis program-ID för stimulans program och säljar-ID för Store-program                                                                |
| participantName          | Partnerns namn                                                                                                               |
| programName              | Namn på incitament/Store-program                                                                                                              |
| tjänade                   | Belopp som erhållits i betala till valuta för programmet/participantID                                                                       |
| earnedUSD                | Belopp som erhållits för program-/deltagar-ID i USD                                                                                      |
| withheldTax              | Skatte belopp i betalningen till valuta för programmet/participantID                                                               |
| Moms                 | Total moms belopp i betalningen till valuta för programmet/participantID (gäller endast incitaments program)                   |
| serviceFeeTax            | Total mängd serviceFeeTax i betalning till valuta för programmet/participantID (gäller endast för Store-program och Azure Marketplace) |
| totalPayment             | Total betalning i lokal valuta exklusive käll skatt och inklusive moms (om tillämpligt) för programmet/participantID   |
| currencyCode             | Betala till valuta kod                                                                                                                      |
| paymentMethod            | Den metod som används för att betala partnern, till exempel elektronisk bank överföring, kredit anteckning                                                     |
| paymentID                | Unikt ID för betalningen. Det här numret visas vanligt vis i ditt konto utdrag (gäller endast för SAP-betalningar).              |
| paymentStatus            | Betalningstatus                                                                                                                            |
| paymentStatusDescription | Egen beskrivning av betalnings status                                                                                                    |
| paymentDate              | Datum betalning skickades från Microsoft                                                                                                      |
|||

## <a name="transaction-history"></a>Transaktions historik

![Exportera transaktions historik](./media/pc-export-transaction.png)

Det här alternativet innehåller en nedladdning av varje rad objekt som du ser på sidan transaktions historik, typ, datum, associerat transaktions belopp, kund, produkt och annan transaktionell information som gäller för dina program.

| Kolumnnamn                    | Beskrivning                                                                                                                              | Tillämplighet för incitament/Store/Azure Marketplace           |
|--------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| earningId                      | Unikt ID för varje tilltjänande                                                                                                       | Alla                                                            |
| participantId                  | Den primära identiteten för partnern enligt programmet                                                                            | Alla                                                            |
| participantIdType              | De flesta program-ID: n för stimulans program och näringsidkare om för Store-program och Azure Marketplace                                          | Alla                                                            |
| participantName                | Partnerns namn                                                                                                              | Alla                                                            |
| partnerCountryCode             | Plats/land/region för partnern                                                                                                  | Alla                                                            |
| programName                    | Namn på incitament/Store-program                                                                                                             | Alla                                                            |
| transactionId                  | Unikt ID för transaktionen                                                                                                    | Alla                                                            |
| transactionCurrency            | Valutan i vilken den ursprungliga kund transaktionen inträffade (detta är inte en partner plats valuta)                                     | Alla                                                            |
| transactionDate                | Datum för transaktionen. Användbart för program där många transaktioner bidrar till en                                           | Alla                                                            |
| transactionExchangeRate        | Valutakurs datum som används för att visa motsvarande transaktions USD-belopp                                                                 | Alla                                                            |
| transactionAmount              | Transaktions belopp i den ursprungliga transaktions valutan baserat på vilken du genererar                                              | Alla                                                            |
| transactionAmountUSD           | Transaktions belopp i USD                                                                                                                | Alla                                                            |
| handtag                          | Indikerar affärs regel för att tjäna                                                                                                  | Alla                                                            |
| earningRate                    | Incitaments grad som tillämpas på transaktions belopp för att generera en tjänande                                                                      | Alla                                                            |
| quantity                       | Varierar beroende på program. Anger Fakturerat antal för transaktions program                                                            | Alla                                                            |
| quantityType                   | Anger typ av kvantitet, till exempel fakturerad kvantitet, MAU                                                                             | Alla                                                            |
| earningType                    | Anger om det är avgift, rabatt, samop, Sälj, och så vidare                                                                                          | Alla                                                            |
| earningAmount                  | Tjänande belopp i den ursprungliga transaktions valutan                                                                                      | Alla                                                            |
| earningAmountUSD               | Tjäna belopp i USD                                                                                                                    | Alla                                                            |
| earningDate                    | Datum för den tjänande                                                                                                                      | Alla                                                            |
| calculationDate                | Datum då intjänaren beräknades i systemet                                                                                            | Alla                                                            |
| earningExchangeRate            | Växelkurs som används för att visa motsvarande USD-belopp                                                                                  | Alla                                                            |
| exchangeRateDate               | Valutakurs datum som används för att beräkna EarningAmount USD                                                                                   | Alla                                                            |
| paymentAmountWOTax             | Uppskattat belopp (utan skatt) i betala till valuta för "skickade" endast betalningar                                                                 | Alla                                                            |
| paymentCurrency                | Betala till den valuta som valts av partner i betalnings profilen. Visas endast för skickade betalningar                                                   | Alla                                                            |
| paymentExchangeRate            | Växelkurs som används för att beräkna paymentAmountWOTax i betalnings valuta med ExchangeRateDate                                            | Alla                                                            |
| claimId                        | Unikt ID för anspråk                                                                                                              | Incitament – endast vissa program                                |
| planId                         | Unikt ID för plan                                                                                                               | Incitament – endast vissa program                                |
| paymentId                      | Unikt ID för betalningen. Det här numret visas vanligt vis i ditt konto utdrag                                                 | Endast SAP-betalningar                                              |
| paymentStatus                  | Betalningstatus                                                                                                                           | Alla                                                            |
| paymentStatusDescription       | Egen beskrivning av betalnings status                                                                                                   | Alla                                                            |
| customerId                     | Är alltid tomt                                                                                                                     | Incitaments program (undantag: OEM) och Azure Marketplace |
| customerName                   | Är alltid tomt                                                                                                                     | Incitaments program (undantag: OEM) och Azure Marketplace |
| partNumber                     | Är alltid tomt                                                                                                                     | Vissa stimulans-och butiks program och Azure Marketplace        |
| Namn                    | Produkt namn som är kopplat till transaktionen                                                                                                       | Alla                                                            |
| productId                      | Unikt produkt-ID                                                                                                                | Store och Azure Marketplace                                    |
| parentProductId                | Unikt ID för överordnad produkt. Om det inte finns någon överordnad produkt för transaktionen, så överordnad produkt-ID = produkt-ID. | Store och Azure Marketplace                                    |
| parentProductName              | Namnet på den överordnade produkten. Om det inte finns någon överordnad produkt för transaktionen får du ett överordnat produkt namn = produkt namn.   | Store och Azure Marketplace                                    |
| productType                    | Typ av produkt, till exempel app, tillägg eller spel                                                                                        | Store och Azure Marketplace                                    |
| invoiceNumber                  | Faktura nummer (gäller endast EA)                                                                                                  | Stimulans och Azure Marketplace – endast vissa program           |
| subscriptionId                 | Prenumerations-ID som är associerad med kunden                                                                                         | Stimulans – endast vissa program                                 |
| subscriptionStartDate          | Start datum för prenumeration                                                                                                                  | Stimulans – endast vissa program                                 |
| subscriptionEndDate            | Slutdatum för prenumeration                                                                                                                    | Stimulans – endast vissa program                                 |
| resellerId                     | Åter försäljarens ID                                                                                                                      | Stimulans – endast vissa program                                 |
| resellerName                   | Återförsäljarens namn                                                                                                                            | Stimulans – endast vissa program                                 |
| distributorId                  | Distributions-ID                                                                                                                   | Stimulans – endast vissa program                                 |
| distributorName                | Namn på distributör                                                                                                                         | Stimulans – endast vissa program                                 |
| agreementNumber                | Avtals nummer                                                                                                                         | Stimulans – endast vissa program                                 |
| agreementStartDate             | Avtalets start datum                                                                                                                     | Stimulans – endast vissa program                                 |
| agreementEndDate               | Datum för avtals slutdatum                                                                                                                       | Stimulans – endast vissa program                                 |
| workload                       | Arbetsbelastning                                                                                                                                 | Stimulans – endast vissa program                                 |
| transactionType                | Transaktions typ, till exempel köp, åter betalning, återföring eller åter betalning                                                               | Store och Azure Marketplace                                    |
| localProviderSeller            | Lokal leverantör/säljare i post                                                                                                          | Endast butik                                                     |
| taxRemitted                    | Moms omremitterad (försäljning, användning eller moms/GST-moms)                                                                                   | Store och Azure Marketplace                                    |
| taxRemitModel                  | Part som ansvarar för att remittera skatter (försäljning, användning eller moms/GST-skatter)                                                                    | Endast butik                                                     |
| storeFee                       | Det belopp som Microsoft har bevarat som en avgift för att göra appen eller tillägget tillgängligt i butiken                                           | Endast butik                                                     |
| transactionPaymentMethod       | Kund betalnings instrument som används för transaktionen, till exempel kort, fakturering av mobil företag eller PayPal                                | Store och Azure Marketplace                                    |
| tpan                           | Anger AD-nätverk från tredje part                                                                                                     | Lagra endast annonser                                               |
| customerCountry                | Kund land/-region                                                                                                                         | Store och Azure Marketplace                                    |
| customerCity                   | Kund ort                                                                                                                            | Store och Azure Marketplace                                    |
| customerState                  | Kund tillstånd                                                                                                                           | Store och Azure Marketplace                                    |
| customerZip                    | Post nummer för kund                                                                                                                 | Store och Azure Marketplace                                    |
| purchaseTypeCode               | Är alltid tomt                                                                                                                     | Incitaments program – CRI                                        |
| purchaseOrderType              | Är alltid tomt                                                                                                                     | Incitaments program – CRI                                        |
| purchaseOrderCoverageStartDate | Är alltid tomt                                                                                                                     | Incitaments program – CRI                                        |
| purchaseOrderCoverageEndDate   | Är alltid tomt                                                                                                                     | Incitaments program – CRI                                        |
| programOfferingLevel           |                                                                                                                                          | Incitaments program – CRI                                        |
| TenantID                       |                                                                                                                                          | Incitaments program                                             |
| externalReferenceId            | Unikt ID för programmet                                                                                                        | Direkta löne program (incitament och butik)                      |
| externalReferenceIdLabel       | Etikett för unikt ID                                                                                                                  | Direkta löne program (incitament och butik)                      |
|||

## <a name="historical-statements"></a>Historiska instruktioner

![Exportera historiska rapporter](./media/pc-export-statements.png)

Transaktions historiken från före juli 1 2019 hanteras separat. Instruktioner kommer att använda följande fält i stället för de aktuella.

> [!NOTE]
> Tidigare transaktions historik har en kolumn med namnet "reserverad" som motsvarar kolumnen "intäkter" i den moderna historiken, förutom att den utesluter alla intäkter med status = "betalning har skickats".

> [!NOTE]
> Filter som 3M, 6 M eller 12M kommer inte att gälla för avsnittet **historiska instruktioner** .

| Fältnamn              | Beskrivning                                                                                                                                                             |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Intäkts källa          | Källan till intäkterna baserat på var transaktionen ägde rum, till exempel Microsoft Store, Windows Phone butik, Windows Store 8 eller annonsering                  |
| Order-ID                | Unikt order-ID. Med det här ID: t kan du identifiera inköps transaktioner med deras respektive icke-inköps transaktioner, till exempel åter betalningar eller åter betalningar. Båda kommer att ha samma order-ID. Om flera betalnings metoder används för ett enda köp kan du också länka inköps transaktionerna. det gäller även för en aktie avgift där flera betalnings metoder användes. |
| Transaktions-ID          | Unikt transaktions-ID.                                                                                                                                          |
| Transaktions datum tid   | Datum och tid då transaktionen inträffade (UTC).                                                                                                                       |
| ID för överordnad produkt       | Unikt ID för överordnad produkt. Om det inte finns någon överordnad produkt för transaktionen, så överordnad produkt-ID = produkt-ID.                                |
| Produkt-ID              | Unikt produkt-ID.                                                                                                                                              |
| Namn på överordnad produkt     | Namnet på den överordnade produkten. Om det inte finns någon överordnad produkt för transaktionen får du ett överordnat produkt namn = produkt namn.                                  |
| Produktnamn            | Produktens namn                                                                                                                                                    |
| Produkttyp            | Typ av produkt, till exempel app, tillägg eller spel                                                                                                                       |
| Kvantitet                | När intäkts källan är Microsoft Store för företag, representerar kvantiteten antalet licenser som har köpts. För alla andra intäkts källor är antalet alltid 1. Även om en enskild transaktion delas upp i två rad objekt eftersom två olika betalnings metoder användes, visar varje rad objekt en kvantitet på 1. |
| Transaktionstyp        | Transaktions typ, till exempel köp, åter betalning, återföring eller åter betalning                                                                                              |
| Betalnings metod          | Kund betalnings instrument som används för transaktionen, till exempel kort, fakturering av mobil företag eller PayPal                                                               |
| Land/region        | Land/region där transaktionen ägde rum                                                                                                                          |
| Lokal leverantör/säljare | Lokal leverantör/säljare i post                                                                                                                                        |
| Transaktions valuta    | Transaktions valuta                                                                                                                                            |
| Transaktions belopp      | Transaktions belopp                                                                                                                                              |
| Skatte mottagare            | Moms omremitterad (försäljning, användning eller moms/GST-moms)                                                                                                                  |
| Netto kvitton            | Transaktions belopp exklusive skatte mottagare                                                                                                                                   |
| Butiks avgift               | Procent andelen av netto inleveranser som har behållits av Microsoft som en avgift för att göra appen eller tillägget tillgängligt i butiken                                                      |
| Appen fortsätter            | Netto inleveranser minus butiks avgiften                                                                                                                                       |
| Kvarhållna skatter          | **Förbehållen** inkomst skatt (del ingår i den reserverade CSV-filen)                                                                                                |
| Payment                 | Appen fortsätter minus eventuell tillämplig inkomst skatts katt (belopp som visas i transaktions valutan). Ingår inte i **reserverad** CSV-fil.                               |
| FX-pris                 | Utländsk växelkurs som används för att omvandla transaktions valutan till betalnings valutan                                                                                         |
| Betalningsvaluta        | Valutan som din betalning görs i                                                                                                                                       |
| Konverterad betalning       | Betalnings belopp konverterat till betalnings valuta med hjälp av FX-pris                                                                                                         |
| Moms remitterad modell         | Part som ansvarar för att remittera skatter (försäljning, användning eller moms/GST-skatter)                                                                                                   |
| Datum och tid för berättigande   | Datum och tid när transaktions fortsätter blir berättigade till utbetalning (UTC). När en utbetalning skapas, inkluderar den transaktions Fortsätt med en giltighets tid innan datumet för utbetalning skapas (endast inkluderad i den **reserverade** CSV-filen). |
| Avgifter                 | Visar en analys av all avgifts information som sammanställs i kolumnen transaktions belopp (ingår endast för Azure Marketplace, ingår inte i den **reserverade** CSV-filen). |
|||

## <a name="next-step"></a>Nästa steg

- [Information om utbetalningsprincip](./payout-policy-details.md)
