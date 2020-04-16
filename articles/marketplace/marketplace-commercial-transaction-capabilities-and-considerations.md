---
title: Microsofts kommersiella marknadsplats transact kapacitet
description: I den här artikeln beskrivs pris-, fakturerings-, fakturerings- och utbetalningsöverväganden för Microsofts kommersiella marknadsplatstransactalternativ.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 768fa9ca2080fc9a58fb321e62d8d61a608f9564
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415264"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Kommersiella marknadsplats transact kapacitet

Den här artikeln innehåller följande handelsrelaterade ämnen för Microsofts kommersiella marknadsplats:

* Transaktionsansvar för olika publiceringsalternativ
* Transact allmän översikt
* Handla faktureringsmodeller
* Transact krav

## <a name="transactions-by-publishing-option"></a>Alternativet Transaktioner efter publicering

Antingen utgivaren eller Microsoft ansvarar för att hantera programvarulicenstransaktioner för erbjudanden på den kommersiella marknaden. Det publiceringsalternativ du väljer för ditt erbjudande avgör vem som hanterar transaktionen. Se [Bestämma vilket publiceringsalternativ](./determine-your-listing-type.md#choose-a-publishing-option) du vill ha för tillgänglighet och förklaringar av varje publiceringsalternativ.

### <a name="list-trial-and-byol-publishing-options"></a>Publiceringsalternativ för lista, utvärderingsversion och BYOL

Utgivare med befintliga handelsfunktioner kan välja publiceringsalternativ för lista, utvärderingsversion och medlaga licenser (BYOL) för marknadsförings- och användarförvärv. Med dessa alternativ deltar Microsoft inte direkt i utgivarens programvarulicenstransaktioner och det finns ingen associerad transaktionsavgift. Utgivare ansvarar för att stödja alla aspekter av programvarulicenstransaktionen, inklusive men inte begränsat till: order, uppfyllelse, mätning, fakturering, fakturering, betalning och insamling. Med alternativen för lista och provpublicering behåller utgivarena 100 % av licensavgifterna för utgivare av programvara som samlas in från kunden.

### <a name="transact-publishing-option"></a>Alternativ för transact-publicering

Alternativet för transact-publicering drar nytta av Microsofts handelsfunktioner och ger en heltäckande upplevelse från identifiering och utvärdering till inköp och implementering. Transact-erbjudanden faktureras mot en befintlig Microsoft-prenumeration eller ett kreditkort, vilket gör att Microsoft kan vara värd för molnmarknadsplatstransaktioner för utgivarens räkning.

Du väljer alternativet verkställ när du skapar ett nytt erbjudande i Partnercenter. På **inställningssidan för Erbjudande** under **Inställningsinformation**väljer du "Ja, jag vill sälja via Microsoft och ha Microsoft-värdtransaktioner för min räkning". Det här alternativet visas bara om transact är tillgängligt för din erbjudandetyp.

## <a name="transact-general-overview"></a>Transact allmän översikt

När du använder alternativet för transactpublicering möjliggör Microsoft försäljning av programvara från tredje part och distribution av vissa erbjudandetyper till kundens Azure-prenumeration. Utgivaren måste ta hänsyn till fakturering av infrastrukturavgifter och utgivarens egna licensavgifter för programvara när du väljer en faktureringsmodell och erbjudandetyp.

Alternativet Transact-publicering stöds för närvarande för följande erbjudandetyper: virtuella datorer, Azure-program och SaaS-appar.

### <a name="billing-infrastructure-costs"></a>Kostnader för faktureringsinfrastruktur

**För virtuella datorer och Azure-program**

För virtuella datorer och Azure-program debiteras azure-infrastrukturanvändningsavgifterna till kundens Azure-prenumeration. Infrastrukturanvändningsavgifter prissätts och presenteras separat från programvaruleverantörens licensavgifter på kundens faktura.

**För SaaS-appar**

För SaaS-appar måste utgivaren ta hänsyn till azure-infrastrukturanvändningsavgifter och programvarulicensavgifter som en enda kostnadspost.  Det representeras som en fast avgift för kunden. Azure-infrastrukturanvändningen hanteras och faktureras direkt till partnern. Faktiska infrastrukturanvändningsavgifter ses inte av kunden. Utgivare väljer vanligtvis att paketera Azure-infrastrukturanvändningsavgifter i sina priser för programvarulicenser. Licensavgifter för programvara mäts inte eller baseras på förbrukning.

## <a name="transact-billing-models"></a>Handla faktureringsmodeller

Beroende på vilket transaktionsalternativ som används kan utgivarens licensavgifter för programvara visas på följande sätt:  

* Gratis: Ingen avgift för programvarulicenser.

* Ta med egen licens (BYOL): Eventuella tillämpliga avgifter för programvarulicenser hanteras direkt mellan utgivaren och kunden. Microsoft skickar endast igenom användningsavgifter för Azure-infrastruktur. Detta gäller endast virtuella datorer och Azure-program.

* Betala per användning: Programvarulicensavgifter presenteras som en pris för per timme, per kärna (vCPU) baserat på den Azure-infrastruktur som används. Detta gäller endast virtuella datorer och Azure-program.

* Prenumerationspriser: Licensavgifter för programvara presenteras som en månatlig eller årlig, återkommande avgift som faktureras som ett schablonbelopp eller per säte. Detta gäller endast SaaS-appar och Azure-program – hanterade appar.

* Testversion av fri programvara: Ingen avgift för programvarulicenser för 30 dagar eller 90 dagar.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Gratis och bring-your-own-license (BYOL) prissättning

När Microsoft publicerar ett kostnadsfritt eller erbjudande om att ta med dig en egen licens spelar microsoft ingen roll när det gäller att underlätta försäljningstransaktionen för dina licensavgifter för programvara. Precis som list- och utvärderingspubliceringsalternativen behåller utgivaren 100 % av programvarulicensavgifterna.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Priser för användningsbaserad betalning och prenumeration (webbplatsbaserad)

När Microsoft publicerar ett erbjudande om betalnings- eller prenumerationstransaktioner tillhandahåller microsoft teknik och tjänster för att behandla inköp, returer och återbetalningar av programvarulicenser. I det här fallet tillåter utgivaren Microsoft att fungera som en agent för dessa ändamål. Utgivaren tillåter Microsoft att underlätta licenstransaktionen för programvara, samtidigt som de behåller sin beteckning som säljare, leverantör, distributör och licensgivare.

Microsoft gör det möjligt för kunder att beställa, licensiera och använda utgivarprogramvara, med förbehåll för villkoren för både Microsofts kommersiella Marketplace och utgivarens licensavtal för slutanvändare. Utgivare måste tillhandahålla sitt licensavtal för slutanvändare eller välja [standardkontraktet](./standard-contract.md) när de skapar erbjudandet.

### <a name="free-software-trials"></a>Testversioner av fri programvara

För transact publiceringsscenarier kan utgivaren göra en programvarulicens tillgänglig gratis i 30 dagar eller 90 dagar. Den här diskonteringsfunktionen inkluderar inte kostnaden för Azure-infrastrukturanvändning som drivs av användning av partnerlösningen.

### <a name="private-offers"></a>Privata erbjudanden

Förutom att använda erbjudandetyper och faktureringsmodeller för att tjäna pengar på ett erbjudande kan utgivare utföra ett privat erbjudande, komplett med förhandlade, avtalsspecifika priser eller anpassade konfigurationer. Privata erbjudanden stöds av alla 3 transact publiceringsalternativ.

Det här alternativet tillåter högre eller lägre priser än det allmänt tillgängliga erbjudandet. Privata erbjudanden kan användas för att rabatt, eller lägga till en premie för ett erbjudande. Privata erbjudanden kan göras tillgängliga för en eller flera kunder genom att vit lista deras Azure-prenumeration på erbjudandenivå.


### <a name="examples"></a>Exempel

**Betala per användning** 

* Om du aktiverar alternativet Betala per er har du följande kostnadsstruktur.

|Din licenskostnad  | $1.00 per timme  |
|---------|---------|
|Kostnaden för Azure-användning (D1/1-Core)    |   $0.14 per timme     |
|*Kunden faktureras av Microsoft*    |  *$1.14 per timme*       |

* I det här fallet fakturerar Microsoft $ 1,14 per timme för användning av din publicerade VM-avbildning.

|Microsoft-räkningar  | $1.14 per timme  |
|---------|---------|
|Microsoft betalar dig 80 % av licenskostnaden|   $0.80 per timme     |
|Microsoft behåller 20 % av licenskostnaden  |  $0.20 per timme       |
|Microsoft behåller 100 % av kostnaden för Azure-användning | $0.14 per timme |

**Ta med din egen licens (BYOL)**

* Om du aktiverar alternativet BYOL har du följande kostnadsstruktur.

|Din licenskostnad  | Licensavgift som förhandlats fram och fakturerats av dig  |
|---------|---------|
|Kostnaden för Azure-användning (D1/1-Core)    |   $0.14 per timme     |
|*Kunden faktureras av Microsoft*    |  *$0.14 per timme*       |

* I det här fallet fakturerar Microsoft $ 0,14 per timme för användning av din publicerade VM-avbildning.

|Microsoft-räkningar  | $0.14 per timme  |
|---------|---------|
|Microsoft behåller kostnaden för Azure-användning    |   $0.14 per timme     |
|Microsoft behåller 0 % av licenskostnaden   |  $0.00 per timme       |

**SaaS App-prenumeration**

Det här alternativet måste konfigureras för att sälja via Microsoft och kan prissättas till ett schablonbelopp eller per användare månadsvis eller år.
*    Om du aktiverar alternativet Sälj via Microsoft för ett SaaS-erbjudande har du följande kostnadsstruktur.

|Din licenskostnad       | $100.00 per månad  |
|--------------|---------|
|Kostnaden för Azure-användning (D1/1-Core)    | Faktureras direkt till utgivaren, inte kunden |
|*Kunden faktureras av Microsoft*    |  *$100.00 per månad (obs: utgivare måste redovisa eventuella uppkomna eller genomströmningsinfrastrukturkostnader i licensavgiften)*  |

* I det här fallet fakturerar Microsoft $ 100,00 för din programvarulicens och betalar ut $ 80,00 till utgivaren.
* Partner som har kvalificerat sig för serviceavgiften för reducerad marknadsplats ser en reducerad transaktionsavgift på SaaS-erbjudandena från maj 2019 till juni 2020. I det här fallet fakturerar Microsoft $ 100,00 för din programvarulicens och betalar ut $ 90,00 till utgivaren.

|Microsoft-räkningar  | $100.00 per månad  |
|---------|---------|
|Microsoft betalar dig 80 % av licenskostnaden <br> \*Microsoft betalar dig 90 % av licenskostnaden för alla kvalificerade SaaS-appar   |   $80.00 per månad <br> \*$90.00 per månad    |
|Microsoft behåller 20 % av licenskostnaden <br> \*Microsoft behåller 10 % av licenskostnaden för alla kvalificerade SaaS-appar.  |  $20.00 per månad <br> \*$10.00     |

* **Reducerad marknadsplatsserviceavgift:** För vissa SaaS-produkter som du publicerar på vår kommersiella marknadsplats minskar Microsoft sin Marketplace-serviceavgift från 20 % (enligt beskrivningen i Microsofts utgivaravtal) till 10 %.  För att din produkt ska kvalificera måste minst en av dina produkter anges av Microsoft som antingen IP-samförsäljning klar eller IP-samförsäljning prioriteras. För att få den reducerade Marketplace-serviceavgiften för månaden måste berättigandet uppfyllas minst fem (5) arbetsdagar före slutet av föregående kalendermånad. Reducerad Marketplace-serviceavgift gäller inte för virtuella datorer, hanterade appar eller andra produkter som görs tillgängliga via vår kommersiella marknadsplats.  Den här reducerade marketplace-serviceavgiften kommer att vara tillgänglig för kvalificerade erbjudanden, med licensavgifter som samlas in av Microsoft mellan den 1 maj 2019 och den 30 juni 2020.  Efter den tiden återgår Marketplace Service Fee till sitt normala belopp.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Kundfakturering, betalning, fakturering och inkasso

**Fakturering och betalning**

Publisher kan använda kundens föredragna faktureringsmetod för att leverera licensavgifter för prenumerations- eller PAYGO-programvara.

**Företagsavtal** 

Om kundens föredragna faktureringsmetod är Microsoft Enterprise-avtalet debiteras dina licensavgifter för programvara med den här faktureringsmetoden som en specificerad kostnad, separat från eventuella Azure-specifika användningskostnader.

**Kreditkort och månadsfaktura** 

Kunder kan också betala med kreditkort och månadsfaktura. I det här fallet debiteras dina licensavgifter för programvara precis som enterprise agreement-scenariot, som en specificerad kostnad, separat från eventuella Azure-specifika användningskostnader.

Om kunden till exempel köper med kreditkort:

|Beskrivning    |    Date  |
|----------|----------|
|Orderperiod   | 15 augusti 2018 - 30 augusti 2018 |
|Terminsslut (månad)   | 30 augusti 2018 |
|Faktureringsdatum | den 1 september 2018 |
|Betalningsdatum för kund | den 1 september 2018 |
|Escrow Period (endast kreditkort, 30 dagar) | 1 september 2018 - 30 september 2018 |
|Start av insamlingsperiod | den 1 september 2018 |
|Insamlingsperiodslut (max, 30 dagar) | den 30 september 2018 |
|Utbetalningsberäkningsdatum (månadsvis den 15:e) | den 1 oktober 2018 |
|Utbetalningsdatum | den 15 oktober 2018 |

Om kunden köper med ett enterprise-avtal:

| Beskrivning |    Date  |
|----------|----------|
|Orderperiod | 15 augusti 2018 - 30 augusti 2018 |
|Löptidsslut (kvartal) | den 30 september 2018 |
|Faktureringsdatum | den 15 oktober 2018 |
|Escrow Period (endast kreditkort, 30 dagar) | Saknas |
|Start av insamlingsperiod | den 15 oktober 2018 |
|Insamlingsperiodslut (max, 90 dagar) | den 15 januari 2019 |
|Betalningsdatum för kund | den 30 december 2018 |
|Utbetalningsberäkningsdatum (månadsvis den 15:e) | den 15 januari 2019 |
|Utbetalningsdatum | den 15 februari 2019 |

**Gratis krediter och monetärt åtagande** 

Vissa kunder väljer att betala Azure i förskott med ett monetärt åtagande i Enterprise-avtalet eller har fått kostnadsfria krediter för användning med Azure. Även om dessa krediter kan användas för att betala för Azure-användning, kan de inte användas för att betala för licensavgifter för utgivaremjukvara.

**Fakturering och inkasso** 

Fakturering av publisher-programvarulicenser visas med den kundvalda faktureringsmetoden och följer faktureringstidslinjen. Kunder utan ett företagsavtal faktureras månadsvis för marknadsplatsprogramlicenser. Kunder med ett företagsavtal faktureras månadsvis via en faktura som presenteras kvartalsvis.

När prissättningsmodeller för prenumerationer eller användningsbaserad betalning väljs fungerar Microsoft som utgivarens agent och ansvarar för alla aspekter av fakturering, betalning och insamling.

### <a name="publisher-payout-and-reporting"></a>Utbetalning och rapportering av utgivare

* Alla licensavgifter för programvara som tas ut av Microsoft som ombud är föremål för en transaktionsavgift på 20 % om inte annat anges och dras av vid tidpunkten för utgivarens utbetalning.

* Kunder köper vanligtvis med enterprise-avtalet eller ett kreditkort aktiverat pay-as-you-go-avtal. Avtalstypen bestämmer fakturering, fakturering, insamling och utbetalningstid.

>[!NOTE]
>All rapportering och insikter för alternativet för transactpublicering är tillgängliga via avsnittet Insikter i avsnittet Cloud Partner Portal eller Analytics i Partner center.

#### <a name="billing-questions-and-support"></a>Faktureringsfrågor och support

Mer information och juridiska policyer finns i [Publisher-avtalet](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (tillgängligt i Molnpartnerportalen).

Om du vill ha hjälp med faktureringsfrågor kontaktar du [supporten för marknadsplatsutgivare.](https://aka.ms/marketplacepublishersupport)

## <a name="transact-requirements"></a>Transact krav

De transactkrav för olika erbjudandetyper behandlas i detta avsnitt.

### <a name="requirements-for-all-offer-types"></a>Krav för alla erbjudandetyper

* Ett Microsoft-konto och ekonomisk information krävs för alternativet för transact-publicering, oavsett erbjudandets prismodell.
* Obligatorisk finansiell information inkluderar utbetalningskonto och skatteprofil.

Mer information om hur du konfigurerar dessa konton finns [i Hantera ditt kommersiella marketplace-konto i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)).

### <a name="requirements-for-specific-offer-types"></a>Krav för specifika erbjudandetyper

Alternativet för transact-publicering är endast tillgängligt för användning med följande typer av marketplace-erbjudanden:

**Virtuell dator** 

Välj mellan kostnadsfria, bring-your-own-licens- eller pay-as-you-go-prissättningsmodeller och presentera som SKU:er definierade på erbjudandenivå. På kundens Azure-faktura presenterar Microsoft licensavgifterna för utgivaren separat från de underliggande Azure-infrastrukturavgifterna. Azure-infrastrukturavgifter drivs av användning av utgivaren.

**Azure-program: Lösningsmall eller hanterad app** 

Måste etablera en eller flera virtuella datorer och drar igenom summan av den virtuella datorn prissättning. För hanterade appar på en enda plan kan en fast månadsprenumeration väljas som prismodell i stället för den virtuella datorns prissättning. I vissa fall skickas Azure-infrastrukturanvändningsavgifter till kunden separat från programvarulicensavgifter, men på samma faktureringsutdrag. Men om du konfigurerar ett hanterat apperbjudande för ISV-infrastrukturavgifter faktureras Azure-resurserna till utgivaren och kunden får en fast avgift som inkluderar kostnaden för infrastruktur, programvarulicenser och hanteringstjänster.

## <a name="next-steps"></a>Nästa steg

* Granska behörighetskraven i publiceringsalternativen efter avsnitt om erbjudandetyp för att slutföra valet och konfigurationen av erbjudandet.
* Läs publiceringsmönstren efter skyltfönster för exempel på hur lösningen mappar till en erbjudandetyp och konfiguration.
