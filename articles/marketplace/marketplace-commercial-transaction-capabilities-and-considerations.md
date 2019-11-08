---
title: Funktioner och överväganden för Marketplace-kommersiella transaktioner | Azure
description: I den här artikeln beskrivs prissättnings-, fakturerings-, fakturerings-och utbetalnings överväganden för en erbjudande typ.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/29/2018
ms.author: pabutler
ms.openlocfilehash: 9aa41e63c275737874d57ba016e297a64f3eb124
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823009"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Funktioner och överväganden för kommersiella Marketplace-transaktioner

Den här artikeln beskriver följande handelsrelaterade ämnen för den kommersiella Marketplace

* Publicerings alternativ för Marketplace
* Allmän översikt över Transact
* Transact-fakturerings modeller
* Transact-krav

## <a name="marketplace-publishing-options"></a>Publicerings alternativ för Marketplace

Följande publicerings alternativ är tillgängliga för kommersiella Marketplace-utgivare.

### <a name="list--trial-publishing-options"></a>Lista & publicerings alternativ för utvärderings version

Utgivare kan dra nytta av publicerings alternativen lista, utvärdering och BYOL för kampanj-och användar förvärv. Med dessa alternativ deltar inte Microsoft direkt i utgivarens program licens transaktioner och det finns ingen kopplad transaktions avgift. Utgivare ansvarar för att stödja alla aspekter av program licens transaktionen, inklusive men inte begränsat till: order, uppfyllelse, mätning, fakturering, fakturering, betalning och insamling. Med publicerings alternativen lista och utvärdering behåller utgivaren 100% av licens avgifterna för Publisher-programvaran som samlas in från kunden. 

### <a name="transact-publishing-option"></a>Alternativ för Transact-publicering

Förutom publicerings alternativen lista och utvärdering är alternativet för att använda Transact-publicering tillgängligt för utgivare. Detta drar nytta av Microsofts globalt tillgängliga funktioner och gör det möjligt för Microsoft att vara värd för moln Marketplace-transaktioner för utgivarens räkning.

## <a name="transact-general-overview"></a>Allmän översikt över Transact

När du använder alternativet för Transact-publicering möjliggör Microsoft försäljning av program från tredje part och distribution av vissa erbjudande typer till kundens Azure-prenumeration. Utgivaren måste beakta faktureringen av infrastruktur avgifter, och utgivarens egna avgifter för program varu licenser, när du väljer en fakturerings modell och erbjudande typ.

Alternativet för Transact-publicering stöds för närvarande för följande erbjudande typer: Virtual Machines, Azure-program och SaaS-appar.


![[Agera Enterprise-erbjudanden på Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Kostnader för fakturerings infrastruktur

**För Virtual Machines-och Azure-program**

För Virtual Machines-och Azure-program debiteras Azures infrastruktur användnings avgifter till kundens Azure-prenumeration.  Avgifter för infrastruktur användning priss ätts och presenteras separat från program varu leverantörens licens avgifter på kundens faktura.

**För SaaS-appar**

För SaaS-appar måste utgivaren beakta Azures användnings avgifter för Azure och licens avgifter för program vara som ett enda kostnads objekt.  Den representeras som en fast avgift för kunden. Användningen av Azure-infrastrukturen hanteras och debiteras partnern direkt.  Den faktiska infrastruktur användnings avgiften ses inte av kunden.  Utgivare väljer vanligt vis att paketera användnings avgifter för Azure-infrastruktur i sina priser för program varu licenser.  Avgifter för program varu licenser är inte avgiftsbelagda eller konsumtions beroende.

## <a name="transact-billing-models"></a>Transact-fakturerings modeller

Beroende på vilket transaktions alternativ som används kan utgivarens licens avgifter visas på följande sätt:  

* Kostnads fri: ingen avgift för program varu licenser. 

* Bring your own license (BYOL): alla tillämpliga avgifter för program varu licenser hanteras direkt mellan utgivaren och kunden. Microsoft passerar bara genom användnings avgifter för Azure-infrastruktur. (Endast Virtual Machines och Azure-program.)

* Betala per användning: licens avgifter för program vara presenteras som en pris taxa per timme, per kärna (vCPU) baserat på den Azure-infrastruktur som används. Detta gäller endast för Virtual Machines och Azure-program.

* • Prenumerations prissättning: licens avgifter för program vara presenteras som en månatlig eller årlig, återkommande avgift debiteras som en fast taxa eller per plats. Detta gäller endast för SaaS-appar och appar som hanteras av Azure program.

* Kostnads fri utvärderings version av program vara: ingen avgift för program varu licenser i 30 dagar eller 90 dagar.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Priser för kostnads fri och BYOL-licens (kostnads fritt)

När du publicerar ett kostnads fritt eller kostnads fritt transaktions erbjudande, spelar Microsoft inte en roll för att under lätta försäljnings transaktionen för dina licens avgifter för program vara. Liksom publicerings alternativen lista och utvärdering behåller utgivaren 100% av licens avgifterna för program varan. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Betala per användning och prenumeration (baserad på plats)

När du publicerar ett erbjudande enligt principen betala per användning eller prenumerations transaktion tillhandahåller Microsoft teknik och tjänster för att bearbeta inköp av program varu licenser, returer och åter betalningar. I det här scenariot godkänner utgivaren att Microsoft agerar som en agent i detta syfte. Utgivaren gör det möjligt för Microsoft att under lätta för program licens transaktionen, samtidigt som säljaren, leverantören, distributören och licens givaren behålls.

Microsoft gör det möjligt för kunder att beställa, licensiera och använda program vara för utgivare, som omfattas av de allmänna villkoren för både Microsofts kommersiella marknads plats och utgivarens licens avtal för användare. Utgivare måste ange sitt licens avtal för slutanvändare eller välja [standard kontraktet](https://docs.microsoft.com/azure/marketplace/standard-contract) när erbjudandet skapas.


### <a name="free-software-trials"></a>Kostnads fria program varu försök

I scenarier med Transact-publicering kan utgivare göra en program varu licens kostnads fri i 30 dagar eller 90 dagar. Den här rabatt funktionen omfattar inte kostnaden för användningen av Azure-infrastrukturen som drivs av partner lösningen.

### <a name="private-offers"></a>Privata erbjudanden

Förutom att använda erbjudande typer och fakturerings modeller för att betala ett erbjudande, kan utgivare använda sig av ett privat erbjudande i Transact, som kompletterar med förhandlade, detaljerade priser eller anpassade konfigurationer. Privata erbjudanden stöds av alla tre alternativ för Transact-publicering.

Det här alternativet tillåter högre eller lägre priser än det offentligt tillgängliga erbjudandet. Privata erbjudanden kan användas för rabatt, eller för att lägga till en Premium för ett erbjudande. Privata erbjudanden kan göras tillgängliga för en eller flera kunder med en vit lista över sin Azure-prenumeration på erbjudande nivån.


### <a name="examples"></a>Exempel

**Betala per användning** 

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

**Ta med din egen licens (BYOL)**

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

**SaaS-app-prenumeration**

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

* **Minskad service avgift för Marketplace:** För vissa SaaS-produkter som du publicerar på vår kommersiella marknads plats kommer Microsoft att minska sin service avgift för Marketplace från 20% (enligt beskrivningen i Microsoft Publisher Agreement) till 10%.  För att produkten ska kunna kvalificeras måste minst en av dina produkter utformas av Microsoft som antingen IP-samförsäljnings färdiga eller IP-samsäljen prioriteras. För att få den här minskade service avgiften för Marketplace för den månaden måste berättigande vara minst fem (5) arbets dagar före slutet av föregående kalender månad. Minskad service avgift för Marketplace gäller inte för virtuella datorer, hanterade appar eller andra produkter som görs tillgängliga via vår kommersiella marknads plats.  Den här minskade service avgiften för Marketplace kommer att vara tillgänglig för kvalificerade erbjudanden, med licens avgifter som samlas in av Microsoft mellan den 1 maj 2019 och 30 juni 2020.  Efter den tiden kommer Marketplace-tjänstens avgift att återgå till sin normala mängd.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Kund fakturering, betalning, fakturering och samlingar

**Fakturering och betalning**

Utgivare kan använda kundens önskade fakturerings metod för att leverera prenumerationer eller PAYGO program licens avgifter.

**Enterprise-avtal** 

Om kundens önskade fakturerings metod är Microsoft Enterprise-avtal debiteras licens avgifterna för program varan med hjälp av fakturerings metoden som en specificerad kostnad, separat från alla Azure-specifika användnings kostnader.

**Kredit kort och månads faktura** 

Kunder kan också betala med hjälp av ett kredit kort och en månads faktura. I det här fallet debiteras licens avgifterna för program varan precis som Enterprise-avtal scenariot, som en specificerad kostnad, separat från alla Azure-särskilda användnings kostnader.

Exempel: om kunden köper med ett kredit kort:

|Beskrivning    |    Date  |
|----------|----------|
|Order period   | Aug, 15, 2018-Aug 30, 2018 |
|Slut period (månad)   | 30 aug 2018 |
|Fakturerings datum | 1 september 2018 |
|Kund betalnings datum | 1 september 2018 |
|Depositions period (endast kredit kort, 30 dagar) | 1 september 2018 – – 30 september 2018 |
|Start för samlings period | 1 september 2018 |
|Samlings period slut (maximalt 30 dagar) | 30 september 2018 |
|Beräknings datum för utbetalning (månatlig på 15) | 1 oktober 2018 |
|Utbetalnings datum | 15 oktober 2018 |

Om kunden köper med en Enterprise-avtal:

| Beskrivning |    Date  |
|----------|----------|
|Order period | Aug, 15, 2018-Aug 30, 2018 |
|Slut period (kvartal) | 30 september 2018 |
|Fakturerings datum | 15 oktober 2018 |
|Depositions period (endast kredit kort, 30 dagar) | Saknas |
|Start för samlings period | 15 oktober 2018 |
|Samlings period slut (maximalt 90 dagar) | 15 Jan 2019 |
|Kund betalnings datum | 30 december 2018 |
|Beräknings datum för utbetalning (månatlig på 15) | 15 Jan 2019 |
|Utbetalnings datum | Feb 15, 2019 |

**Kostnads fria krediter och penning åtagande** 

Vissa kunder väljer att förskottsbetala Azure med ett penning åtagande i Enterprise-avtal eller ha fått kostnads fria krediter för användning med Azure. Även om de här krediterna kan användas för att betala för Azure-användning, kan de inte användas för att betala för licens avgifter för Publisher-programvaran.

**Fakturering och samlingar** 

Licens faktureringen för Publisher-programvaran visas med kunden vald fakturerings metod och följer tids linjen för fakturerings tids linjen. Kunder utan en Enterprise-avtal på plats faktureras per månad för program varu licenser för Marketplace. Kunder med en Enterprise-avtal faktureras per månad via en faktura som presenteras kvartals vis.

När du har valt prenumerations modell eller pris sättning enligt principen betala per användning fungerar Microsoft som utgivarens agent och ansvarar för alla aspekter av fakturering, betalning och insamling.

### <a name="publisher-payout-and-reporting"></a>Utbetalning och rapportering av utgivare

* Alla licens avgifter för program vara som samlas in av Microsoft som en agent omfattas av 20% transaktions avgift om inget annat anges och dras av vid utbetalning av utgivare.

* Kunderna köper vanligt vis med Enterprise-avtal eller ett kredit kort aktiverat avtal för betala per användning. Avtals typen bestämmer fakturerings-, fakturerings-, samlings-och utbetalnings tiden.

>[!NOTE] 
>All rapportering och insikter för alternativet Transact Publishing är tillgängliga via avsnittet Insights i avsnittet Cloud Partner Portal eller Analytics i Partner Center.

#### <a name="billing-questions-and-support"></a>Fakturerings frågor och support

Mer information och juridiska principer finns i [utgivar avtalet](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (tillgängligt i Cloud Partner Portal).

Om du vill ha hjälp med fakturerings frågor kan du kontakta [supporten för marknads plats utgivare](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact-krav

Transact-kraven för olika erbjudande typer beskrivs i det här avsnittet.

### <a name="requirements-for-all-offer-types"></a>Krav för alla erbjudande typer

- En Microsoft-konto och ekonomisk information krävs för alternativet för Transact-publicering, oavsett pris sättnings modell för erbjudandet.
- Obligatorisk finansiell information inkluderar utbetalnings konto och skatte profil.

Mer information om hur du konfigurerar dessa konton finns i [Hantera ditt partner Center-konto](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Krav för särskilda erbjudande typer

Alternativet för att använda Transact-publicering är bara tillgängligt för användning med följande Marketplace-erbjudande typer: 

**Virtuell dator** 

Välj från kostnads fri, egen licens, eller betala per användning – pris modeller och presentera som SKU: er definierade på erbjudande nivån. På kundens Azure-faktura presenterar Microsoft utgivarens licens avgifter separat från de underliggande avgifterna för Azure-infrastrukturen. Avgifterna för Azure-infrastrukturen styrs av användningen av utgivarens program vara.

**Azure-program: lösnings mal len eller hanterad app** 

Måste etablera en eller flera virtuella datorer och hämta genom summan av priserna för den virtuella datorn. För hanterade appar i en enda plan kan en månatlig månatlig prenumeration väljas som pris modell i stället för prissättningen för den virtuella datorn. I vissa fall skickas Azure Infrastructure användnings avgifter till kunden separat från licens avgifter för program vara, men på samma fakturerings instruktion. Men om du konfigurerar ett hanterat program erbjudande för ISV-infrastruktur debiteras Azure-resurserna för utgivaren och kunden får en fast avgift som omfattar kostnaden för infrastruktur, program varu licenser och hanterings tjänster.

## <a name="next-steps"></a>Nästa steg

* Granska behörighets kraven i avsnittet publicerings alternativ per erbjudande typ för att slutföra valet och konfigurationen av ditt erbjudande.
* Granska publicerings mönstren efter butik för exempel på hur din lösning mappar till en erbjudande typ och konfiguration.
