---
title: Fakturering på kommersiella marknadsplatser | Azure Marketplace
description: Den här artikeln täcker handelsrelaterade ämnen för kommersiell marknadsplats.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: a0912e1dca63fabfe6bc546305824a1c582b9a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279750"
---
# <a name="commercial-marketplace-billing"></a>Fakturering på kommersiella marknadsplatser

Denna artikel täcker handelsrelaterade ämnen för den kommersiella marknaden:

- [Alternativ för marketplace-publicering](#marketplace-publishing-options)
- [Transact allmän översikt](#transact-general-overview)
- [Handla faktureringsmodeller](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Alternativ för marketplace-publicering

Kommersiell marknadsplats erbjuder flera publiceringsalternativ för utgivare.

### <a name="list-and-trial-publishing-options"></a>Alternativ för publicering av list- och utvärderingsversion

Utgivare kan utnyttja listan, utvärderingsversionen och ta med egna licenspubliceringsalternativ (BYOL) för marknadsförings- och användarförvärv. Med dessa alternativ deltar Microsoft inte direkt i utgivarens programvarulicenstransaktioner och det finns ingen associerad transaktionsavgift. Utgivare ansvarar för att stödja alla aspekter av programvarulicenstransaktionen, inklusive men inte begränsat till: order, uppfyllelse, mätning, fakturering, fakturering, betalning och insamling. Med alternativen för lista och provpublicering behåller utgivarena 100 % av licensavgifterna för utgivare av programvara som samlas in från kunden.

### <a name="transact-publishing-option"></a>Alternativ för transact-publicering

Förutom alternativen för lista och utvärderingspublicering är alternativet för visning av transaktering tillgängligt för utgivare. Det här alternativet utnyttjar Microsofts globalt tillgängliga handelsfunktioner och gör det möjligt för Microsoft att vara värd för molnmarknadsplatstransaktioner på utgivarens vägnar.

## <a name="transact-general-overview"></a>Transact allmän översikt

När du använder alternativet för transactpublicering möjliggör Microsoft försäljning av programvara från tredje part och distribution av vissa erbjudandetyper till kundens Azure-prenumeration. Utgivaren måste ta hänsyn till fakturering av infrastrukturavgifter och utgivarens egna licensavgifter för programvara när du väljer en faktureringsmodell och erbjudandetyp.

Alternativet Transact-publicering stöds för närvarande för följande erbjudandetyper: virtuella datorer, Azure-program och SaaS-program.

![Handla på Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Kostnader för faktureringsinfrastruktur

#### <a name="for-virtual-machines-and-azure-applications"></a>För virtuella datorer och Azure-program

För virtuella datorer och Azure-program debiteras azure-infrastrukturanvändningsavgifterna till kundens Azure-prenumeration. Infrastrukturanvändningsavgifter prissätts och presenteras separat från programvaruleverantörens licensavgifter på kundens faktura.

#### <a name="for-saas-apps"></a>För SaaS-appar

För SaaS-appar måste utgivaren ta hänsyn till azure-infrastrukturanvändningsavgifter och programvarulicensavgifter som en enda kostnadspost. Det representeras som en fast avgift för kunden. Azure-infrastrukturanvändningen hanteras och faktureras direkt till partnern. Faktiska infrastrukturanvändningsavgifter ses inte av kunden. Utgivare väljer vanligtvis att paketera Azure-infrastrukturanvändningsavgifter i sina priser för programvarulicenser. Licensavgifter för programvara mäts inte eller baseras på förbrukning.

## <a name="transact-billing-models"></a>Handla faktureringsmodeller

Beroende på vilket transaktionsalternativ som används kan utgivarens licensavgifter för programvara visas på följande sätt:

- *Gratis:* Ingen avgift för programvarulicenser.
- *Ta med egen licens (BYOL):* Eventuella tillämpliga avgifter för programvarulicenser hanteras direkt mellan utgivaren och kunden. Microsoft skickar endast igenom användningsavgifter för Azure-infrastruktur. (Endast virtuella datorer och Azure-program.)
- *Betala per användning:* Programvarulicensavgifter presenteras som en pris för per timme, per kärna (vCPU) baserat på den Azure-infrastruktur som används. Den här modellen gäller endast virtuella datorer och Azure-program.
- *Prenumerationspriser*: Programvarulicensavgifter presenteras som en månatlig eller årlig, återkommande avgift som faktureras som ett fast pris eller per säte. Den här modellen gäller endast SaaS-appar och Azure-program – hanterade appar.
- *Testversion av fri programvara*: Ingen avgift för programvarulicenser för 30 dagar eller 90 dagar.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Gratis och bring-your-own-license (BYOL) prissättning

När Microsoft publicerar ett kostnadsfritt eller erbjudande om att ta med dig en egen licens spelar microsoft ingen roll när det gäller att underlätta försäljningstransaktionen för dina licensavgifter för programvara. Precis som list- och utvärderingspubliceringsalternativen behåller utgivaren 100 % av programvarulicensavgifterna.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Priser för användningsbaserad betalning och prenumeration (webbplatsbaserad)

När Microsoft publicerar ett erbjudande om betalnings- eller prenumerationstransaktioner tillhandahåller microsoft teknik och tjänster för att behandla inköp, returer och återbetalningar av programvarulicenser. I det här fallet tillåter utgivaren Microsoft att fungera som en agent för dessa ändamål. Utgivaren tillåter Microsoft att underlätta licenstransaktionen för programvara, samtidigt som de behåller sin beteckning som säljare, leverantör, distributör och licensgivare.

Microsoft gör det möjligt för kunder att beställa, licensiera och använda utgivarprogramvara, med förbehåll för villkoren för både Microsofts kommersiella Marketplace och utgivarens licensavtal för slutanvändare. Utgivare måste tillhandahålla sitt licensavtal för slutanvändare eller välja [standardkontraktet](https://docs.microsoft.com/azure/marketplace/standard-contract) när de skapar erbjudandet.

### <a name="free-software-trials"></a>Testversioner av fri programvara

För transact publiceringsscenarier kan utgivaren göra en programvarulicens tillgänglig gratis i 30 dagar eller 90 dagar. Den här diskonteringsfunktionen inkluderar inte kostnaden för Azure-infrastrukturanvändning som drivs av användning av partnerlösningen.

### <a name="private-offers"></a>Privata erbjudanden

Förutom att använda erbjudandetyper och faktureringsmodeller för att tjäna pengar på ett erbjudande kan utgivare utföra ett privat erbjudande, komplett med förhandlad och avtalsspecifik prissättning eller anpassade konfigurationer. Privata erbjudanden stöds av alla 3 transact publiceringsalternativ.

Det här alternativet tillåter högre eller lägre priser än det allmänt tillgängliga erbjudandet. Privata erbjudanden kan användas för att rabatt eller lägga till en premie för ett erbjudande. Privata erbjudanden kan göras tillgängliga för en eller flera kunder genom att vit lista deras Azure-prenumeration på erbjudandenivå.

### <a name="examples"></a>Exempel

#### <a name="pay-as-you-go"></a>Användningsbaserad betalning

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

### <a name="bring-your-own-license-byol"></a>Ta med din egen licens (BYOL)

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

### <a name="saas-app-subscription"></a>SaaS App-prenumeration

Det här alternativet måste konfigureras för att sälja via Microsoft och kan prissättas till ett schablonbelopp eller per användare månadsvis eller år.

• Om du aktiverar alternativet Sälj via Microsoft för ett SaaS-erbjudande har du följande kostnadsstruktur.

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

**Reducerad marknadsplatsserviceavgift:** För vissa SaaS-produkter som du publicerar på vår kommersiella marknadsplats minskar Microsoft sin Marketplace-serviceavgift från 20 % (enligt beskrivningen i Microsofts utgivaravtal) till 10 %.  För att din produkt ska kvalificera måste minst en av dina produkter anges av Microsoft som antingen IP-samförsäljning klar eller IP-samförsäljning prioriteras. För att få den reducerade Marketplace-serviceavgiften för månaden måste berättigandet uppfyllas minst fem (5) arbetsdagar före slutet av föregående kalendermånad. Reducerad Marketplace-serviceavgift gäller inte för virtuella datorer, hanterade appar eller andra produkter som görs tillgängliga via vår kommersiella marknadsplats.  Den här reducerade marketplace-serviceavgiften kommer att vara tillgänglig för kvalificerade erbjudanden, med licensavgifter som samlas in av Microsoft mellan den 1 maj 2019 och den 30 juni 2020.  Efter den tiden återgår Marketplace Service Fee till sitt normala belopp.
