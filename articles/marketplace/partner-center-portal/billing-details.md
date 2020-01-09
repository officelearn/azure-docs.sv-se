---
title: Fakturering av affärs platser | Azure Marketplace
description: Den här artikeln beskriver Commerce-relaterade ämnen för kommersiella marknads platser.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: 3f610dbc2c5cf052729857c09de1d437e52ac20a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480585"
---
# <a name="commercial-marketplace-billing"></a>Fakturering av affärs platser 

Den här artikeln beskriver Commerce-relaterade ämnen för den kommersiella Marketplace:

- [Publicerings alternativ för Marketplace](#marketplace-publishing-options) 
- [Allmän översikt över Transact](#transact-general-overview)
- [Transact-fakturerings modeller](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Publicerings alternativ för Marketplace 

Handels Marketplace innehåller flera publicerings alternativ för utgivare.

### <a name="list-and-trial-publishing-options"></a>Publicerings alternativ för lista och utvärdering

Utgivare kan använda listan, utvärderings versionen och ta med dina egna licens alternativ (BYOL) för publicering av kampanj-och användar förvärvs syfte. Med dessa alternativ deltar inte Microsoft direkt i utgivarens program licens transaktioner och det finns ingen kopplad transaktions avgift. Utgivare ansvarar för att stödja alla aspekter av program licens transaktionen, inklusive men inte begränsat till: order, uppfyllelse, mätning, fakturering, fakturering, betalning och insamling. Med publicerings alternativen lista och utvärdering behåller utgivaren 100% av licens avgifterna för Publisher-programvaran som samlas in från kunden.

### <a name="transact-publishing-option"></a>Alternativ för Transact-publicering

Förutom publicerings alternativen lista och utvärdering är alternativet för att använda Transact-publicering tillgängligt för utgivare. Det här alternativet drar nytta av Microsofts globalt tillgängliga funktioner och gör det möjligt för Microsoft att vara värd för moln Marketplace-transaktioner för utgivarens räkning.

## <a name="transact-general-overview"></a>Allmän översikt över Transact

När du använder alternativet för Transact-publicering möjliggör Microsoft försäljning av program från tredje part och distribution av vissa erbjudande typer till kundens Azure-prenumeration. Utgivaren måste beakta faktureringen av infrastruktur avgifter, och utgivarens egna avgifter för program varu licenser, när du väljer en fakturerings modell och erbjudande typ. 

Alternativet för Transact-publicering stöds för närvarande för följande erbjudande typer: virtuella datorer, Azure-program och SaaS-program. 

![Transact i Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Kostnader för fakturerings infrastruktur

#### <a name="for-virtual-machines-and-azure-applications"></a>För virtuella datorer och Azure-program

För virtuella datorer och Azure-program debiteras Azures infrastruktur användnings avgifter till kundens Azure-prenumeration. Avgifter för infrastruktur användning priss ätts och presenteras separat från program varu leverantörens licens avgifter på kundens faktura.

#### <a name="for-saas-apps"></a>För SaaS-appar

För SaaS-appar måste utgivaren beakta Azures användnings avgifter för Azure och licens avgifter för program vara som ett enda kostnads objekt. Den representeras som en fast avgift för kunden. Användningen av Azure-infrastrukturen hanteras och debiteras partnern direkt. Den faktiska infrastruktur användnings avgiften ses inte av kunden. Utgivare väljer vanligt vis att paketera användnings avgifter för Azure-infrastruktur i sina priser för program varu licenser. Avgifter för program varu licenser är inte avgiftsbelagda eller konsumtions beroende.

## <a name="transact-billing-models"></a>Transact-fakturerings modeller

Beroende på vilket transaktions alternativ som används kan utgivarens licens avgifter visas på följande sätt:

- *Kostnads fri*: ingen avgift för program varu licenser.
- *Bring Your Own License (BYOL)* : alla tillämpliga avgifter för program varu licenser hanteras direkt mellan utgivaren och kunden. Microsoft passerar bara genom användnings avgifter för Azure-infrastruktur. (Endast Virtual Machines och Azure-program.)
- *Betala*per användning: licens avgifter för program vara presenteras som en pris taxa per timme, per kärna (vCPU) baserat på den Azure-infrastruktur som används. Den här modellen gäller bara för virtuella datorer och Azure-program.
- *Prenumerations pris*: licens avgifter för program vara presenteras som en månatlig eller årlig, återkommande avgift som faktureras som en fast taxa eller per plats. Den här modellen gäller endast för SaaS-appar och appar som hanteras av Azure program.
- *Kostnads fri utvärderings version av program vara*: ingen avgift för program varu licenser i 30 dagar eller 90 dagar.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Priser för kostnads fri och BYOL-licens (kostnads fritt)

När du publicerar ett kostnads fritt eller kostnads fritt transaktions erbjudande, spelar Microsoft inte en roll för att under lätta försäljnings transaktionen för dina licens avgifter för program vara. Liksom publicerings alternativen lista och utvärdering behåller utgivaren 100% av licens avgifterna för program varan.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Betala per användning och prenumeration (baserad på plats)

När du publicerar ett erbjudande enligt principen betala per användning eller prenumerations transaktion tillhandahåller Microsoft teknik och tjänster för att bearbeta inköp av program varu licenser, returer och debitering. I det här scenariot godkänner utgivaren att Microsoft agerar som en agent i detta syfte. Utgivaren gör det möjligt för Microsoft att under lätta för program licens transaktionen, samtidigt som säljaren, leverantören, distributören och licens givaren behålls.

Microsoft gör det möjligt för kunder att beställa, licensiera och använda program vara för utgivare, som omfattas av de allmänna villkoren för både Microsofts kommersiella marknads plats och utgivarens licens avtal för användare. Utgivare måste ange sitt licens avtal för slutanvändare eller välja [standard kontraktet](https://docs.microsoft.com/azure/marketplace/standard-contract) när erbjudandet skapas.

### <a name="free-software-trials"></a>Kostnads fria program varu försök

I scenarier med Transact-publicering kan utgivare göra en program varu licens kostnads fri i 30 dagar eller 90 dagar. Den här rabatt funktionen omfattar inte kostnaden för användningen av Azure-infrastrukturen som drivs av partner lösningen.

### <a name="private-offers"></a>Privata erbjudanden

Förutom att använda erbjudande typer och fakturerings modeller för att betala ett erbjudande, kan utgivare använda sig av ett privat erbjudande i Transact, med förhandlad och leverantörsspecifik prissättning, eller anpassade konfigurationer. Privata erbjudanden stöds av alla tre alternativ för Transact-publicering.

Det här alternativet tillåter högre eller lägre priser än det offentligt tillgängliga erbjudandet. Privata erbjudanden kan användas för att rabattera eller lägga till en Premium för ett erbjudande. Privata erbjudanden kan göras tillgängliga för en eller flera kunder med en vit lista över sin Azure-prenumeration på erbjudande nivån.

### <a name="examples"></a>Exempel

#### <a name="pay-as-you-go"></a>Användningsbaserad betalning

* Om du aktiverar alternativet betala per användning, har du följande kostnads struktur.

|Din licens kostnad  | $1,00 per timme  |
|---------|---------|
|Användnings kostnad i Azure (D1/1-kärna)    |   $0,14 per timme     |
|*Kunden debiteras av Microsoft*    |  *$1,14 per timme*       |

* I det här scenariot faktureras Microsoft $1,14 per timme för användning av den publicerade VM-avbildningen.

|Microsoft-räkningar  | $1,14 per timme  |
|---------|---------|
|Microsoft betalar 80% av din licens kostnad|   $0,80 per timme     |
|Microsoft behåller 20% av din licens kostnad  |  $0,20 per timme       |
|Microsoft håller 100% av Azures användnings kostnad | $0,14 per timme |

### <a name="bring-your-own-license-byol"></a>Ta med din egen licens (BYOL)

* Om du aktiverar alternativet BYOL har du följande kostnads struktur.

|Din licens kostnad  | Licens avgift förhandlad och debiteras av dig  |
|---------|---------|
|Användnings kostnad i Azure (D1/1-kärna)    |   $0,14 per timme     |
|*Kunden debiteras av Microsoft*    |  *$0,14 per timme*       |

* I det här scenariot faktureras Microsoft $0,14 per timme för användning av den publicerade VM-avbildningen.

|Microsoft-räkningar  | $0,14 per timme  |
|---------|---------|
|Microsoft håller Azures användnings kostnad    |   $0,14 per timme     |
|Microsoft behåller 0% av din licens kostnad   |  $0,00 per timme       |

### <a name="saas-app-subscription"></a>SaaS-app-prenumeration

Det här alternativet måste konfigureras att sälja via Microsoft och kan prisas enligt en fast taxa eller per användare per månad eller årlig basis.

• Om du aktiverar alternativet Sälj via Microsoft för ett SaaS-erbjudande har du följande kostnads struktur.

|Din licens kostnad       | $100,00 per månad  |
|--------------|---------|
|Användnings kostnad i Azure (D1/1-kärna)    | Debiteras direkt till utgivaren, inte hos kunden |
|*Kunden debiteras av Microsoft*    |  *$100,00 per månad (Obs! utgivaret måste ha ett konto för alla kostnader i licens avgiften*  |

* I det här scenariot faktureras Microsoft $100,00 för din program varu licens och du betalar ut $80,00 till utgivaren.
* Partner som har kvalificerat sig för den minskade service avgiften för Marketplace ser en reducerad transaktions avgift på SaaS-erbjudandena från maj 2019 till och med juni 2020. I det här scenariot faktureras Microsoft $100,00 för din program varu licens och du betalar ut $90,00 till utgivaren.

|Microsoft-räkningar  | $100,00 per månad  |
|---------|---------|
|Microsoft betalar 80% av din licens kostnad <br> \* Microsoft betalar 90% av din licens kostnad för kvalificerade SaaS-appar   |   $80,00 per månad <br> \* $90,00 per månad    |
|Microsoft behåller 20% av din licens kostnad <br> \* Microsoft behåller 10% av din licens kostnad för kvalificerade SaaS-appar.  |  $20,00 per månad <br> \* $10,00     |

**Minskad service avgift för Marketplace:** För vissa SaaS-produkter som du publicerar på vår kommersiella marknads plats kommer Microsoft att minska sin service avgift för Marketplace från 20% (enligt beskrivningen i Microsoft Publisher Agreement) till 10%.  För att produkten ska kunna kvalificeras måste minst en av dina produkter utformas av Microsoft som antingen IP-samförsäljnings färdiga eller IP-samsäljen prioriteras. För att få den här minskade service avgiften för Marketplace för den månaden måste berättigande vara minst fem (5) arbets dagar före slutet av föregående kalender månad. Minskad service avgift för Marketplace gäller inte för virtuella datorer, hanterade appar eller andra produkter som görs tillgängliga via vår kommersiella marknads plats.  Den här minskade service avgiften för Marketplace kommer att vara tillgänglig för kvalificerade erbjudanden, med licens avgifter som samlas in av Microsoft mellan den 1 maj 2019 och 30 juni 2020.  Efter den tiden kommer Marketplace-tjänstens avgift att återgå till sin normala mängd.
