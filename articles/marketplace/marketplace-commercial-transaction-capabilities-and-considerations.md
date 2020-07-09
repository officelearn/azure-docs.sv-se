---
title: Transact-funktioner för Microsofts kommersiella Marketplace
description: Den här artikeln beskriver pris-, fakturerings-, fakturerings-och utbetalnings överväganden för Transact-alternativet för kommersiella Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 653c55fa7476fa5fed077002db226297a33dfef6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119437"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Transact-funktioner för kommersiella Marketplace

## <a name="transactions-by-publishing-option"></a>Transaktioner per publicerings alternativ

Antingen utgivaren eller Microsoft ansvarar för att hantera program licens transaktioner för erbjudanden på den kommersiella marknaden. Det publicerings alternativ du väljer för ditt erbjudande avgör vem som hanterar transaktionen. Se [bestämma publicerings alternativ](./determine-your-listing-type.md#choose-a-publishing-option) för tillgänglighet och förklaringar för varje publicerings alternativ.

### <a name="list-trial-and-byol-publishing-options"></a>Publicerings alternativ för lista, utvärdering och BYOL

Utgivare med befintliga funktioner i Commerce kan välja lista, utvärdering och BYOL publicerings alternativ för kampanj-och användar förvärv. Med dessa alternativ deltar inte Microsoft direkt i utgivarens program licens transaktioner och det finns ingen kopplad transaktions avgift. Utgivare ansvarar för att stödja alla aspekter av program licens transaktionen, inklusive men inte begränsat till order, uppfyllelse, avläsning, fakturering, fakturering, betalning och insamling. Med publicerings alternativen lista och utvärdering behåller utgivaren 100% av licens avgifterna för Publisher-programvaran som samlas in från kunden.

### <a name="transact-publishing-option"></a>Alternativ för Transact-publicering

Alternativet för att använda Transact-publicering är att dra nytta av Microsoft Commerce-funktioner och ger en heltäckande upplevelse från identifiering och utvärdering till köp och implementering. Transact-erbjudanden faktureras mot en befintlig Microsoft-prenumeration eller ett kredit kort, vilket gör att Microsoft kan vara värd för moln Marketplace-transaktioner för utgivarens räkning.

Du väljer alternativet Transact när du skapar ett nytt erbjudande i Partner Center. På sidan **erbjudande konfiguration** under **installations information**väljer du Ja, jag vill sälja via Microsoft och har Microsofts värd transaktioner för mig. Med det här alternativet visas endast om Transact är tillgängligt för din erbjudande typ.

## <a name="transact-overview"></a>Översikt över Transact

När du använder alternativet för användning av Transact-publicering möjliggör Microsoft försäljning av program vara från tredje part och distribution av vissa erbjudande typer till kundens Azure-prenumeration. Utgivaren måste beakta faktureringen av infrastruktur avgifter och dina egna licens avgifter för program vara när du väljer en fakturerings modell och erbjudande typ.

Alternativet för Transact-publicering stöds för närvarande för följande erbjudande typer:

- Virtuella datorer
- Azure-program
- SaaS-program

### <a name="billing-infrastructure-costs"></a>Kostnader för fakturerings infrastruktur

För **virtuella datorer** och **Azure-program**debiteras Azure Infrastructure Usage avgifter till kundens Azure-prenumeration. Avgifter för infrastruktur användning priss ätts och presenteras separat från program varu leverantörens licens avgifter på kundens faktura.

För **SaaS-appar**måste utgivaren beakta Azure-infrastrukturens användnings avgifter och program licens avgifter som ett enda kostnads objekt.  Den representeras som en fast avgift för kunden. Användningen av Azure-infrastrukturen hanteras och debiteras partnern direkt. Den faktiska infrastruktur användnings avgiften ses inte av kunden. Utgivare väljer vanligt vis att paketera användnings avgifter för Azure-infrastruktur i sina priser för program varu licenser. Avgifter för program varu licenser är inte avgiftsbelagda eller konsumtions beroende.

## <a name="transact-billing-models"></a>Transact-fakturerings modeller

Beroende på vilket transaktions alternativ som används är licens avgifter för program vara följande:

- **Kostnads fri** – ingen avgift för program varu licenser.
- **Bring Your Own License** (BYOL) – alla tillämpliga avgifter för program varu licenser hanteras direkt mellan utgivaren och kunden. Microsoft passerar bara genom användnings avgifter för Azure-infrastruktur. Detta gäller enbart för virtuella datorer och Azure-program.
- **Betala** per användning – licens avgifter för program vara presenteras som en pris taxa per timme, per kärna (vCPU) baserat på den Azure-infrastruktur som används. Detta gäller enbart för virtuella datorer och Azure-program.
- **Prenumerations priser** – licens avgifter för program vara presenteras som en månatlig eller årlig, återkommande avgift faktureras som en fast taxa eller per plats. Detta gäller för SaaS-appar (månatlig eller årlig) och Azure program-hanterade appar (varje månad).
- **Kostnads fri utvärdering av program vara** – ingen avgift för program varu licenser i 30 eller 90 dagar.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Priser för kostnads fri och BYOL-licens (kostnads fritt)

När du publicerar ett kostnads fritt eller kostnads fritt transaktions erbjudande, spelar Microsoft inte en roll för att under lätta försäljnings transaktionen för dina licens avgifter för program vara. Liksom publicerings alternativen lista och utvärdering behåller utgivaren 100% av licens avgifterna för program varan.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Betala per användning och prenumeration (baserad på plats)

När du publicerar ett erbjudande enligt principen betala per användning eller prenumerations transaktion tillhandahåller Microsoft teknik och tjänster för att bearbeta inköp av program varu licenser, returer och åter betalningar. I det här scenariot godkänner utgivaren att Microsoft agerar som en agent i detta syfte. Utgivaren gör det möjligt för Microsoft att under lätta för program licens transaktionen, samtidigt som säljaren, leverantören, distributören och licens givaren behålls.

Microsoft gör det möjligt för kunder att beställa, licensiera och använda program varan enligt villkoren i både Microsofts kommersiella marknads plats och ditt licens avtal för slutanvändare. Du måste ange ett eget licens avtal för slutanvändare eller välja [standard kontraktet](./standard-contract.md) när du skapar erbjudandet.

### <a name="free-software-trials"></a>Kostnads fria program varu försök

För scenario publicerings scenarier kan du göra en program varu licens tillgänglig kostnads fritt i 30 eller 90 dagar. Den här rabatt funktionen omfattar inte kostnaden för Azures infrastruktur användning som styrs av partner lösningen.

### <a name="private-offers"></a>Privata erbjudanden

Förutom att använda erbjudande typer och fakturerings modeller för att betala ett erbjudande kan du använda Transact ett privat erbjudande, komplett med förhandlad, detaljerad prissättning eller anpassade konfigurationer. Privata erbjudanden stöds av alla tre alternativ för Transact-publicering.

Det här alternativet tillåter högre eller lägre priser än det offentligt tillgängliga erbjudandet. Privata erbjudanden kan användas för att rabattera eller lägga till ett bidrag till ett erbjudande. Privata erbjudanden kan göras tillgängliga för en eller flera kunder genom att du får en lista över Azure-prenumerationer på erbjudande nivån.

### <a name="examples"></a>Exempel

**Betala per användning** 

Betala per användning har följande kostnads struktur:

|Din licens kostnad  | $1,00 per timme   |
|---------|---------|
|Användnings kostnad i Azure (D1/1-kärna)    |   $0,14 per timme     |
|*Kunden debiteras av Microsoft*    |  *$1,14 per timme*       |
||

I det här scenariot faktureras Microsoft $1,14 per timme för användning av den publicerade VM-avbildningen.

|Microsoft-räkningar  | $1,14 per timme  |
|---------|---------|
|Microsoft betalar 80% av din licens kostnad|   $0,80 per timme     |
|Microsoft behåller 20% av din licens kostnad  |  $0,20 per timme       |
|Microsoft håller 100% av Azures användnings kostnad | $0,14 per timme |
||

**Ta med din egen licens (BYOL)**

BYOL har följande kostnads struktur:

|Din licens kostnad  | Licens avgift förhandlad och debiteras av dig  |
|---------|---------|
|Användnings kostnad i Azure (D1/1-kärna)    |   $0,14 per timme     |
|*Kunden debiteras av Microsoft*    |  *$0,14 per timme*       |
||

I det här scenariot faktureras Microsoft $0,14 per timme för användning av den publicerade VM-avbildningen.

|Microsoft-räkningar  | $0,14 per timme  |
|---------|---------|
|Microsoft håller Azures användnings kostnad    |   $0,14 per timme     |
|Microsoft behåller 0% av din licens kostnad   |  $0,00 per timme       |
||

**SaaS-app-prenumeration**

Det här alternativet måste konfigureras att sälja via Microsoft och kan prisas enligt en fast taxa eller per användare per månad eller årlig basis. Om du aktiverar alternativet **Sälj via Microsoft** för ett SaaS-erbjudande har du följande kostnads struktur:

| Din licens kostnad       | $100,00 per månad  |
|--------------|---------|
| Användnings kostnad i Azure (D1/1-kärna)    | Debiteras direkt till utgivaren, inte hos kunden |
| *Kunden debiteras av Microsoft*    |  *$100,00 per månad (utgivare måste redovisa alla kostnader som uppstått eller genom att överföra infrastruktur i licens avgiften)*  |
||

I det här scenariot faktureras Microsoft $100,00 för din program varu licens och du betalar ut $80,00 till utgivaren.

Partner som har kvalificerat sig för den minskade service avgiften för Marketplace ser en reducerad transaktions avgift på SaaS-erbjudandena från maj 2019 till och med juni 2020.

I det här scenariot fakturerar Microsoft $100,00 för din program varu licens och utvärderar $90,00 till utgivaren:

|Microsoft-räkningar  | $100,00 per månad  |
|---------|---------|
|Microsoft betalar 80% av din licens kostnad <br> \*Microsoft betalar 90% av din licens kostnad för kvalificerade SaaS-appar   |   $80,00 per månad <br> \*$90,00 per månad    |
|Microsoft behåller 20% av din licens kostnad <br> \*Microsoft behåller 10% av din licens kostnad för alla kvalificerade SaaS-appar.  |  $20,00 per månad <br> \*$10,00     |

För vissa SaaS-produkter som du publicerar på den kommersiella marknaden kommer Microsoft att minska sin **service avgift för Marketplace** från 20% (enligt beskrivningen i Microsoft Publisher Agreement) till 10%. För att ditt erbjudande ska bli kvalificerat måste minst ett av dina erbjudanden utses av Microsoft som IP-samförsäljnings klar eller prioriterad IP-adress för samtidig försäljning. För att få den här minskade service avgiften för Marketplace för den månaden måste berättigande vara minst fem arbets dagar före slutet av föregående kalender månad. Minskad service avgift för Marketplace gäller inte för virtuella datorer, hanterade appar eller andra produkter som görs tillgängliga via den kommersiella marknads platsen. Denna reducerade avgift kommer att vara tillgänglig för kvalificerade erbjudanden, med licens avgifter som samlas in av Microsoft mellan den 1 maj 2019 och 30 juni 2020. Efter den tiden återgår avgiften till dess normala belopp.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Kund fakturering, betalning, fakturering och samlingar

**Fakturering och betalning** – du kan använda kundens önskade fakturerings metod för att leverera prenumerations-eller PAYGO program licens avgifter.

**Enterprise-avtal** – om kundens fakturerings metod är Microsoft Enterprise-avtal debiteras licens avgifterna för program varan med hjälp av fakturerings metoden som en specificerad kostnad, separat från alla Azure-specifika användnings kostnader.

**Kredit kort och månads faktura** – kunder kan också betala med ett kredit kort och en månads faktura. I det här fallet debiteras licens avgifterna för program varan precis som Enterprise-avtal scenariot, som en specificerad kostnad, separat från alla Azure-särskilda användnings kostnader.

**Kostnads fria krediter och betalnings åtagande** – vissa kunder väljer att förskottsbetala Azure med ett penning åtagande i Enterprise-avtal eller ha fått kostnads fria krediter för användning med Azure. Även om de här krediterna kan användas för att betala för Azure-användning, kan de inte användas för att betala för licens avgifter för Publisher-programvaran.

**Fakturering och samlingar** – licens fakturering för utgivar program vara presenteras med kunden vald fakturerings metod och följer tids linjen för fakturerings tids linjen. Kunder utan en Enterprise-avtal på plats faktureras per månad för program varu licenser för Marketplace. Kunder med en Enterprise-avtal faktureras per månad via en faktura som presenteras kvartals vis.

När du har valt prenumerations modell eller pris sättning enligt principen betala per användning fungerar Microsoft som utgivarens agent och ansvarar för alla aspekter av fakturering, betalning och insamling.

### <a name="publisher-payout-and-reporting"></a>Utbetalning och rapportering av utgivare

Alla licens avgifter för program vara som samlas in av Microsoft som en agent omfattas av 20% transaktions avgift om inget annat anges och dras av vid utbetalning av utgivare.

Kunderna köper vanligt vis med Enterprise-avtal eller ett kredit kort aktiverat avtal för betala per användning. Avtals typen bestämmer fakturerings-, fakturerings-, samlings-och utbetalnings tiden.

>[!NOTE]
>All rapportering och insikter för alternativet för Transact-publicering är tillgängliga via Analytics-avsnittet i Partner Center.

#### <a name="billing-questions-and-support"></a>Fakturerings frågor och support

Mer information och juridiska principer finns i [utgivar avtalet](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (tillgängligt i Partner Center).

Om du behöver hjälp med fakturerings frågor kontaktar du [supporten för support för Microsoft Marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact-krav

Det här avsnittet beskriver Transact-krav för olika erbjudande typer.

### <a name="requirements-for-all-offer-types"></a>Krav för alla erbjudande typer

- En Microsoft-konto och ekonomisk information krävs för alternativet för Transact-publicering, oavsett pris sättnings modell för erbjudandet.
- Obligatorisk finansiell information inkluderar utbetalnings konto och skatte profil.

Mer information om hur du konfigurerar dessa konton finns i [Hantera ditt kommersiella Marketplace-konto i Partner Center](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Krav för särskilda erbjudande typer

Alternativet för att använda Transact-publicering är bara tillgängligt för användning med följande Marketplace-erbjudande typer:

- **Virtuell dator** – Välj från kostnads fri, egen licens, eller betala per användning – pris modeller och presentera som SKU: er definierade på erbjudande nivån. På kundens Azure-faktura presenterar Microsoft utgivarens licens avgifter separat från de underliggande avgifterna för Azure-infrastrukturen. Avgifterna för Azure-infrastrukturen styrs av användningen av utgivarens program vara.

- **Azure-program: lösnings mal len eller hanterad app** – du måste etablera en eller flera virtuella datorer och hämta genom summan av priserna för den virtuella datorn. För hanterade appar i en enda plan kan en månatlig månatlig prenumeration väljas som pris modell i stället för prissättningen för den virtuella datorn. I vissa fall skickas Azure Infrastructure användnings avgifter till kunden separat från licens avgifter för program vara, men på samma fakturerings instruktion. Men om du konfigurerar ett hanterat program erbjudande för ISV-infrastruktur debiteras Azure-resurserna för utgivaren och kunden får en fast avgift som omfattar kostnaden för infrastruktur, program varu licenser och hanterings tjänster.

- **SaaS-program** – måste vara en lösning för flera innehavare, använda [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) för autentisering och integrera med [API: er för SaaS-utförande](partner-center-portal/pc-saas-fulfillment-api-v2.md). Användningen av Azure-infrastrukturen hanteras och faktureras direkt till dig (partnern), så du måste kontots användnings avgifter för Azure-infrastrukturen och licens avgifter för program vara som ett enda kostnads objekt. Detaljerad vägledning finns [i skapa ett nytt SaaS-erbjudande i den kommersiella Marketplace](partner-center-portal/create-new-saas-offer.md).

## <a name="next-steps"></a>Nästa steg

- Granska behörighets kraven i avsnittet publicerings alternativ per erbjudande typ för att slutföra valet och konfigurationen av ditt erbjudande.
- Granska publicerings mönstren efter butik för exempel på hur din lösning mappar till en erbjudande typ och konfiguration.
