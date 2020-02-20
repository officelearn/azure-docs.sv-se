---
title: Azure EA-avtal och ändringar
description: I den här artikeln förklaras hur Azure EA-avtal och ändringar påverkar din användning av Azure EA-portalen.
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: c38f28120b7c84077d8d5012c4be4698680e5fdb
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200820"
---
# <a name="azure-ea-agreements-and-amendments"></a>Azure EA-avtal och ändringar

I den här artikeln beskrivs hur Azure EA-avtal och ändringar kan påverka åtkomst, användning och betalningar för Azure-tjänster.

## <a name="amendment-status"></a>Ändringsstatus

Eventuella ändringar för Azure-kunder måste göras via partnern eller Microsoft-kontoteamet och behandlas via det regionala driftcentret. Om du tror att en ändring inte har behandlats kan du kontakta din partner, programvaruguide eller Microsoft-kontoteamet.

## <a name="enrollment-provisioning-status"></a>Status för registreringens etablering

Startdatumet för ett nytt ekonomiskt åtagande definieras som det datum då det regionala åtgärdscentret bearbetade det. Eftersom beställningar med ekonomiskt åtagande via Azure EA-portalen och bearbetas i UTC-tidszonen kan det uppstå en fördröjning om din inköpsorder med ekonomiskt åtagande har bearbetats i en annan region. Du ser startdatumet för det ekonomiska åtagandet som startdatumet för täckningen i inköpsordern på https://www.explore.ms. Startdatumet för täckningen är när det ekonomiska åtagandet visas i Azure EA-portalen.

## <a name="special-pricing-and-azure-commitment-discount-amendments"></a>Särskilda priser och rabattändringar för Azure-åtaganden

Om du behöver särskilda priser och ACD-ändringar (Azure Commitment discount) måste du diskutera dem med ditt kontoteam. Azure Ops-teamet kan tillämpa ACD när en ändring har bearbetats av affärskontoret och en begäran har skickats till Azure Ops-teamet. ACD-rabatten gäller endast den aktuella månaden. Eventuella tidigare månader kompenseras med en tidigarelagd kredit. 

## <a name="support-offer-not-provisioned"></a>Supporterbjudandet har inte etablerats

Du kan beställa standardsupport eller Pro-Direct-support genom att köpa supportspecifika SKU:er. Beställningen liknar EA Azure-inköpsordrar för monetära SKU:er. Exempelvis 6QK-00001. SKU-numren för support är W6T-00002 (Pro-Direct) och W6T-00003 (Standard). Kontrollera aktuella supporterbjudanden innan du köper support-SKU:er.

Registreringen måste ha minst ett aktivt konto för att supporterbjudandet ska etableras.

 Azures [EA-supportavtalserbjudande](https://azure.microsoft.com/offers/enterprise-agreement-support/) är tillgängligt för vissa kunder.

## <a name="enrollment-status"></a>Registreringsstatus

Registreringar har något av följande statusvärden. Varje värde avgör hur du kan använda registreringen. Registreringsstatusen avgör i vilket stadie registreringen är. Statusen anger om du måste aktivera registreringen innan du kan använda den. Du kan också se om inledande perioden har upphört så att du debiteras för överförbrukning.

**Väntar** – registreringsadministratören måste logga in i Azure EA-portalen. Därefter får registreringen statusen **Aktiv**.

**Aktiv** – registreringen är tillgänglig och kan användas. Du kan skapa konton och prenumerationer i Azure EA-portalen. Registreringen är aktiv tills Enterprise-avtalets slutdatum.

**Obegränsad utökad giltighet** – statusen Obegränsad utökad giltighet tilldelas efter Enterprise-avtalets slutdatum. Innan EA-registreringen når Enterprise-avtalets slutdatum bör registreringsadministratören välja att:

- Förnya registreringen genom att lägga till ett nytt ekonomiskt åtagande
- Överföra den befintliga registreringen till en ny registrering
- Migrera till MOSP-programmet (Microsoft Online Subscription program)
- Bekräfta inaktiveringen av alla tjänster som är kopplade till registreringen

**Upphört** – EA-registreringen upphör när den når Enterprise-avtalets slutdatum. EA-kunden avregistreras från den utökade giltigheten och alla tjänster inaktiveras.

Från och med den 1 augusti 2019 godkänns inte nya avanmälningsformulär för Azures kommersiella kunder. I stället försätts alla registreringar i statusen Obegränsad utökad giltighet. Om du vill sluta använda Azure-tjänsterna ska du stänga din prenumeration i [Azure EA-portalen](https://portal.azure.com). Eller så kan din partner skicka en uppsägningsförfrågan till https://www.explore.ms. Kunder med avtal för myndigheter påverkas inte.

**Överförd** – statusen Överförd används för registreringar vars konton och tjänster har överförts till en ny registrering. Registreringar överförs inte automatiskt om ett nytt registreringsnummer genereras vid förnyandet. Det tidigare registreringsnumret måste tas med i kundens förnyelsebegäran om överföringen ska ske automatiskt.

## <a name="partner-markup"></a>Pålägg för partner

Påläggen för partner förbättrar kundernas kostnadsrapportering i Azure EA-portalen. I Azure EA-portalen visas den användning och de priser som partnern har konfigurerat för kunden.

Påläggen gör att partneradministratörer kan lägga till ett procentuellt prispålägg på de indirekta Enterprise-avtalen. Det procentuella pålägget gäller för alla Microsofts förstapartstjänster i Azure EA-portalen, till exempel mätarpriser, ekonomiska åtaganden och beställningar. När partnern har publicerat pålägget ser kunderna kostnaderna för Azure i Azure EA-portalen. Några exempel är användningssammanfattningar, prislistor och nedladdade användningsrapporter.

Från och med september 2019 kan en partner lägga till påslag när som helst under en tidsperiod. De behöver inte vänta till nästa tidsperiod för att tillämpa pålägget.

Microsoft använder inte det angivna pålägget och tillhörande priser för något annat ändamål, om inte kanalpartnern uttryckligen godkänner det.

### <a name="how-the-calculation-works"></a>Så här fungerar beräkningen

Licenstjänsteleverantören tillhandahåller ett enda procentvärde i EA-portalen.  All kommersiell information på portalen lyfts upp av procenttalet som anges av licenstjänsteleverantören. Exempel:

- Kunden registrerar ett Enterprise-avtal med ekonomiskt åtagande på 100 000 USD.
- Mätarpriset för tjänst A är 10 USD/timme.
- Licenstjänsteleverantören anger en påläggsprocent på 10 % på EA-portalen.
- Exemplet nedan är hur kunden ser den kommersiella informationen:
    - Saldo: 110 000 USD.
    - Mätarpris för tjänst A: 11 USD/timme.
    - Användnings-/värdinformation för tjänst A för användning i 100 timmar: 1 100 USD.
    - Tillgängligt saldo för kunden efter avdrag för förbrukning av tjänst A: 108 900 USD.

### <a name="when-to-use-a-markup"></a>När du ska använda ett pålägg

Använd funktionen om du anger samma påläggsprocent på ALLA kommersiella transaktioner inom Enterprise-avtalet. Det vill säga – om du gör ett pålägg på det ekonomiska åtagandets information, mätarpriserna, beställningsinformationen osv.

Använd inte påläggsfunktionen om:
- Du använder olika priser mellan priser för ekonomiskt åtagande och mätare.
- Du använder olika priser för olika mätare.

Om du använder olika priser för olika mätare rekommenderar vi att utveckla en anpassad lösning utifrån API-nyckeln, som kan tillhandahållas av kunden, för att hämta förbrukningsdata och tillhandahålla rapporter.

### <a name="other-important-information"></a>Annan viktig information

Den här funktionen är avsedd att ge en uppskattning av Azure-kostnaden till slutkunden. Licenstjänsteleverantören ansvarar för alla ekonomiska transaktioner med kunden under EA-avtalet.

Se till att granska den kommersiella informationen – saldo, prislista osv innan du publicerar de pålagda priserna till slutkund.

### <a name="how-to-add-a-price-markup"></a>Så här lägger du till ett prispålägg

**Steg ett: Lägga till prispålägg**

1. I EA-portalen väljer du **Reports** (Rapporter) i det vänstra navigeringsfältet.
1. Under _Usage Summary_ (Användningssammanfattning) klickar du på den blå frasen **Markup** (Pålägg).
1. Ange påläggsprocenten (mellan -100 till 100) och klicka på **Preview** (Förhandsversion).


**Steg två: Granska och verifiera**

Granska påläggspriset i _Usage Summary_ (Användningssammanfattning) för åtagandeperioden i kundvyn. Microsoft-priset är fortfarande tillgängligt i partnervyn. Vyerna kan växlas med hjälp av partnerpåläggets växlingsknapp för personer högst upp till höger.

1. Granska priserna i prisdokumentet.
1. Du kan göra ändringar innan du publicerar genom att välja **Edit** (Redigera) på fliken _View Usage Summary > Customer View_ (Visa användningssammanfattning > Kundvy).  
  Pålägg görs för både tjänsterna och åtagandesaldon med samma procentsatser. Om du har olika procentsatser för saldo och mätarpriser, eller olika procentsatser för olika tjänster, ska du inte använda den här funktionen.

**Steg tre: Publicera**

När priserna har granskats och verifierats klickar du på **Publish** (Publicera).
  
Priser med pålägg blir tillgängliga för företagsadministratörer direkt efter att du har valt publicera. Redigeringar kan inte göras av pålägg. Du måste inaktivera pålägg och börja om med steg 1.

### <a name="which-enrollments-have-a-markup-enabled"></a>Vilka registreringar har pålägg aktiverat?

Om du vill kontrollera om en registrering har en pålägg publicerad klickar du på **Manage** (Hantera) i det vänstra navigeringsfältet och klickar på fliken **Enrollment** (Registrering). Välj den registreringsrutan du vill markera och ser påläggsstatusen under _Enrollment Detail_ (Registreringsinformation). Här visas den aktuella statusen för påläggsfunktionen för det Enterprise-avtalet som Disabled (Inaktiverad), Preview (Förhandsversion) eller Published (Publicerad).

### <a name="how-can-the-customer-download-usage-estimates"></a>Hur kan kunden ladda ned användningsberäkningar?

När partnerpålägg har publicerats får den indirekta kunden åtkomst till månatliga CSV-filer för saldo och debiteringar samt månatliga CSV-filer för användning. Användningsinformationsfilerna innehåller resurskostnader och utökade kostnader.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>Hur kan jag som partner använda pålägg på befintliga EA-kunder som tidigare fanns hos en annan partner?
Partners kan använda påläggsfunktionen (på Azure EA) efter att ett byte av kanalpartner har bearbetats. Du behöver inte vänta på nästa årsperiod.


## <a name="resource-commitment-and-requesting-quota-increases"></a>Resursåtagande och begära kvotökningar

**Systemet tillämpar följande standardkvoter per prenumeration:**

| **Resurs** | **Standardkvot** | **Kommentarer** |
| --- | --- | --- |
| Beräkningsinstanser i Microsoft Azure | 20 samtidiga små beräkningsinstanser eller deras motsvarighet i andra beräkningsinstansstorlekar. | I följande tabell ges information om hur du beräknar det motsvarande antalet små instanser:<ul><li> Extra liten – 1 motsvarande liten instans </li><li> Liten – 1 motsvarande liten instans </li><li> Medelstor – 2 motsvarande små instanser </li><li> Stor – 4 motsvarande små instanser </li><li> Extra stor – 8 motsvarande små instanser </li> </ul>|
| Virtuella datorer – beräkningsinstanser i Microsoft Azure v2 | EA: 350 kärnor | Virtuella datorer – GA IaaS v2:<ul><li> A0\_A7-familjen – 350 kärnor </li><li> B\_A0\_A4-familjen – 350 kärnor </li><li> A8\_A9-familjen – 350 kärnor </li><li> DF-familjen – 350 kärnor</li><li> GF – 350 kärnor </li></ul>|
| Microsoft Azure Hosted Services | 6 värdtjänster | Den här gränsen för värdtjänster kan inte höjas till högre än sex för en enskild prenumeration. Om du behöver fler värdtjänster lägger du till ytterligare prenumerationer. |
| Microsoft Azure Storage | 5 lagringskonton, med maxstorlek på 100 TB för varje konto. | Du kan öka antalet lagringskonton till upp till 20 per prenumeration. Om du behöver fler lagringskonton lägger du till ytterligare prenumerationer. |
| SQL Azure | 149 databaser av någon typ (t.ex. Web Edition eller Business Edition). |   |
| Åtkomstkontroll | 50 namnområden per konto. 100 miljoner Access Control-transaktioner per månad |   |
| Service Bus | 50 namnområden per konto. 40 Service Bus-anslutningar | Kunder som köper Service Bus-anslutningar via anslutningspaket får kvoter som motsvarar mittpunkten mellan anslutningspaketet de har köpt och det näst högsta anslutningspaketstorleken. Kunder som väljer ett 500-paket har en kvot på 750. |

## <a name="resource-commitment"></a>Resursåtagande

Microsoft tillhandahåller tjänster till dig upp till minst den associerade användningsnivå som ingår i det månatliga åtagandet du köpt (tjänståtagandet), men alla andra ändringar i användningsnivå gällande tjänsteresurserna (t.ex. ett högre antal beräkningsinstanser som körs eller en ökning av det använda lagringsutrymmet) gäller under förutsättning att dessa tjänsteresurser finns tillgängliga.

Kvoter som beskrivs ovan är inte ett tjänståtagande. För att kunna bestämma det antal samtidiga mindre beräkningsinstanser (eller motsvarande) som Microsoft ska tillhandahålla under tjänståtagandet, bestäms detta genom att dividera det antal bekräftade timmar med mindre beräkningsinstanser som köps under en månad med antalet timmar under årets kortaste månad (dvs. februari – 672 timmar).

## <a name="requesting-a-quota-increase"></a>Begära en kvotökning

Du kan begära en kvotökning när som helst genom att skicka en [onlinebegäran](https://g.microsoftonline.com/0WAEP00en/6). Ange följande information för att bearbeta din begäran:

- Microsoft-kontot eller arbets- eller skolkontot som är associerat med kontoinnehavaren för din prenumeration. Det här är e-postadressen som används för att logga in på Microsoft Azure-portalen för att hantera dina prenumerationer. Identifiera även att det här är kontot som är associerat med en EA-registrering.
- Resurserna och beloppet som du vill ha en kvotökning för.
- Prenumerations-ID på Azure Developer-portalen associerat med din tjänst.
  - Om du vill ha information om hur du får ditt prenumerations-ID kan du [kontakta supporten](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="azure-compute-pre-purchase-plan-cpp"></a>Azure Compute Pre-Purchase-prenumeration (CPP)

Azure Compute Pre-Purchase-prenumerationen (CPP – tidigare kallad Azure Compute Promo) är ett erbjudande som ger kunder med förutsägbara arbetsbelastningar med stabilt tillstånd en möjlighet att få databehandling till en betydande rabatt genom att förskottsköpa Azure Compute för 12 månader. Det är tillgängligt för EA och synligt i EA-portalen.

Om du har beställt en CPP-SKU är det för en specifik typ av dator i ett visst specifikt datacenter. Som funktionen ser ut idag tillhandahåller vi ingen lista över alla CPP-köp som en kund har gjort, så det finns ingen lista över väntande krediter. Men när du konfigurerar de köpta datortyperna i datacentret där de köptes ser du den månatliga fördelningen i användningssammanfattningsrapporten på EA-portalen.

Det förskottsköpta beloppet visas som en kredit i kolumnen ”Included Units” (Inkluderade enheter) och användning mot det resulterar i inga debiteringar tills de inkluderade enheterna förbrukas. När de har förbrukats sker ytterligare användning till de förhandlade priserna i prisdokumentet med normala EA-kundpriser.

Hur krediterna köps baseras på antalet datorer i en klass per månad. Exempel: Om du har köpt 70 virtuella A2-datorer i USA, östra 2 skulle du inte riktigt se det i EA-portalen på samma sätt som du köpte det.

Månatliga datorer beräknas som 744 timmar per månad. Konvertering av det till timmar skulle bli 70 gånger 744 eller 52 080 köpta timmar virtuella A2-datorer per månad. Eftersom virtuella A2-datorer i EA-portalens användningssammanfattning rapporteras i 100-timmarssteg skulle du se en kredit på 520,8 (52 080/100) ”inkluderade enheter” per månad och du skulle se de förbrukade enheterna hittills under månaden. Användningskostnaderna bör visas som noll tills de månatliga förbrukade enheterna överstiger de månatliga inkluderade enheterna.

Olika datortyper använder olika måttenheter så till exempel virtuella D3- och D4-datorer använder en 10-timmars måttenhet i vår användningssammanfattningsrapport så ett köp av 70 av dessa skulle visa 5 208 inkluderade enheter per månad (52 080/10).

I allmänhet ger dig formeln nedan antalet virtuella datorer som köpts baserat på deras måttenhet:

<center><b> (”inkluderad mängd” * ”måttenheter”) / 744 </b></center>

## <a name="plan-skus"></a>Plan-SKU:er

Plan-SKU:er ger möjlighet att köpa en svit med integrerade tjänster tillsammans till ett rabatterat pris. Plan-SKU:erna är utformade att komplettera varandra genom ytterligare integrerade erbjudanden och programsvit för större kostnadsbesparingar.

Ett exempel skulle vara prenumerationen på Operations Management Suite (OMS). OMS erbjuder ett enkelt sätt att få åtkomst till en fullständig uppsättning molnbaserade hanteringsfunktioner, inklusive analys, konfiguration, automatisering, säkerhet, säkerhetskopiering och haveriberedskap. OMS-prenumerationer omfattar rättigheter till System Center-komponenter för att tillhandahålla en komplett lösning för hybridmolnmiljöer.

Företagsadministratörer kan tilldela kontoinnehavare för att etablera tidigare köpta plan-SKU:er i EA-portalen genom att följa dessa steg:

### <a name="view-the-price-sheet-to-check-included-quantity"></a>Visa prisdokumentet för att kontrollera inkluderad mängd

1. Logga in som företagsadministratör.
1. Klicka på **Reports** (Rapporter) i det vänstra navigeringsfältet.
1. Klicka på fliken **Price Sheet** (Prisdokument).
1. Klicka på nedladdningsikonen i det övre högra hörnet.
1. Hitta motsvarande plan-SKU-artikelnummer med filter på kolumnen ”Included Quantity” (Inkluderad mängd) och välj värden större än ”0”.

### <a name="provision-the-plan-skus"></a>Etablera plan-SKU:er

Företagsadministratörer kan lägga till nya kontoinnehavare, associera en befintlig kontoinnehavare eller begära befintliga kontoinnehavare att logga in på Azure EA-portalen för att etablera tidigare köpta plan-SKU:er i Azure EA-portalen genom att följa stegen nedan.  

**Lägga till en ny kontoinnehavare (slutfört av EA-administratör):**

1. I Azure EA-portalen väljer du **Manage** (Hantera) i det vänstra navigeringsfältet.
1. Klicka på fliken **Account** (Konto).
1. Klicka på **+Add Account** (+Lägg till konto) på sidan Account (Konto).
1. Välj en avdelning eller lämna som ej tilldelad.
1. Välj avsedd autentiseringstyp.
1. Ange ett användarvänligt namn du vill använda för att identifiera det här kontot i rapportering.
1. Ange kontoinnehavarens e-postadress som du vill ska associeras med det nya kontot.
1. Bekräfta den e-postadress du vill associera med det nya kontot.
1. Klicka på **Lägg till**.
1. Du kan lägga till ett annat konto genom att klicka på **Add Another Account** (Lägg till ett annat konto) eller så kan du klicka på knappen **Add** (Lägg till) i det nedre högra hörnet i det vänstra verktygsfältet.
1. Nu kan kontoinnehavaren logga in för att lägga till de relevanta prenumerationerna för att etablera plan-SKU:erna

**Associera en befintlig kontoinnehavare:**

1. Klicka på **Manage** (Hantera) i EA-portalen.
1. Klicka på fliken **Account** (Konto).
1. Klicka på **+Add an account** (+Lägg till konto). Ange det Microsoft-konto eller arbets- eller skolkonto som är associerat med det befintliga kontot.
1. Bekräfta det Microsoft-konto eller arbets- eller skolkonto som är associerat med det befintliga kontot.
1. Ange ett namn du vill använda för att identifiera det här kontot i rapportering.
1. Klicka på **Lägg till**.
1. Du kan lägga till ytterligare ett konto genom att välja alternativet **+Add an Account** (+Lägg till konto) igen eller gå tillbaka till startsidan genom att klicka på knappen **Administrator** (Administratör).  
1. Om du klickar för att visa sidan Account (Konto) visas den nyligen tillagda sidan med statusen Pending (Väntar). Statusen ändras till Active (Aktiv) när kontoinnehavaren har loggat in på EA-portalen för första gången.

### <a name="existingnew-account-owners-to-create-new-subscriptions"></a>Befintliga/nya kontoinnehavare för att skapa nya prenumerationer

**Steg ett: Logga in på kontot**
1. I Azure EA-portalen väljer du fliken **Manage** (Hantera) och går till **Subscription** (Prenumeration) på den översta menyn.
1. Kontrollera att du är inloggad som kontoinnehavare för kontot.
1. Klicka på **+Add Subscription** (Lägg till prenumeration).
1. Klicka på **Köp**.

Första gången du lägger till en prenumeration på ett konto måste du ange dina kontaktuppgifter. När du lägger till fler prenumerationer fylls dina kontaktuppgifter i åt dig.

Första gången du lägger till en prenumeration på ditt konto uppmanas du att godkänna MOSA-avtalet och en prisplan. Dessa avsnitt gäller INTE för Enterprise-avtalskunder men är nödvändiga just nu för att etablera din prenumeration. Tillägget till din Microsoft Azure Enterprise-avtalsregistrering ersätter ovanstående, och din kontraktsmässiga relation ändras inte. Markera rutan som anger att du godkänner villkoren.

**Steg två: Uppdatera prenumerationens namn**

Alla nya prenumerationer läggs till med det standardmässiga prenumerationsnamnet ”Microsoft Azure Enterprise”. Det är viktigt att uppdatera prenumerationsnamnet för att skilja det från andra prenumerationer i företagsregistreringen och se till att det går att känna igen på rapporter för företagsnivå.

Klicka på **Subscriptions** (Prenumerationer), klicka på den prenumeration som du har skapat och klicka sedan på **Edit Subscription Details** (Redigera prenumerationsdetaljer).

Uppdatera prenumerationsnamnet och tjänstadministratören och klicka på bockmarkeringen. Prenumerationsnamnet visas på rapporter och är även namnet på det projekt som är associerat med prenumerationen i utvecklingsportalen.
Det kan ta upp till 24 timmar innan nya prenumerationer sprids i prenumerationslistan.

Bara kontoinnehavare kan visa och hantera prenumerationer.

### <a name="troubleshooting"></a>Felsökning

**Kontoinnehavare visas med väntande status**

När nya kontoinnehavare läggs till i registreringen första gången visas alltid statusen ”väntande”. När kontoinnehavaren får välkomstmeddelandet kan han/hon logga in och aktivera sitt konto. Den här aktiveringen uppdaterar kontostatusen från ”väntande” till ”aktiv”.

**Användningar som debiteras efter plan-SKU:er har köpts**

Det här scenariot inträffar när kunden har distribuerat tjänster under fel registreringsnummer eller valt fel tjänster.

För att verifiera om du distribuerar under rätt registrering kan du kontrollera informationen om dina inkluderade enheter via prisdokumentet. Logga in som företagsadministratör och klicka på **Reports** (Rapporter) i det vänstra navigeringsfältet och välj fliken **Price Sheet** (Prisdokument). Klicka på nedladdningsikonen i det övre högra hörnet och hitta motsvarande plan-SKU-artikelnummer med filter på kolumnen ”Included Quantity” (Inkluderad mängd) och välj värden större än ”0”.

Se till att din OMS-plan visas i prisdokumentet under inkluderade enheter. Om det inte finns några inkluderade enheter för OMS-plan för registreringen kan din OMS-plan finnas under en annan registrering. Kontakta supporten för Azure EA-portalen på [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

Om de inkluderade enheterna för tjänsterna i prisdokumentet inte stämmer överens med det du har distribuerat, t.ex. Operational Insights Premium – analyserade data kontra Operational Insights Standard – analyserade data, betyder det att du kan ha distribuerat tjänster som inte omfattas av planen. Kontakta supporten för Azure EA-portalen på [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) så att vi kan hjälpa dig.

**Etablerade plan-SKU-tjänster på fel registrering**

Om du har flera registreringar och har distribuerat tjänster under fel registreringsnummer, som inte har en OMS-plan, kontaktar du supporten för Azure EA-portalen på [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

## <a name="next-steps"></a>Nästa steg

- Om du vill börja använda Azures EA-portalen kan du läsa [Kom igång med Azure EA-portalen](ea-portal-get-started.md).
- Administratörer i Azure EA-portalen bör läsa [Administration i Azure EA-portalen](ea-portal-administration.md). Där finns mer information om vanliga administrativa uppgifter.
