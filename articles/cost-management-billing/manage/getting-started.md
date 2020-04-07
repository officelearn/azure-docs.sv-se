---
title: Förhindra och analysera oväntade avgifter med Azure Cost Management och fakturering
description: Lär dig hur du undviker oväntade kostnader på din Azure-faktura och använder kostnadsspårnings-och hanteringsfunktioner för ditt Azure-konto.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 3/30/2020
ms.author: banders
ms.openlocfilehash: 79af6f78e8e9bf93c49deafe79f6a421cbb77d1a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475256"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Förhindra oväntade avgifter med Azure-fakturering och kostnadshantering

När du registrerar dig för Azure finns det flera saker du kan göra för att få en bättre uppfattning om dina utgifter:

- Få uppskattningar av kostnader innan du lägger till tjänster med [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/), Azure-prisdokumentet eller när du lägger till tjänster i Azure-portalen.
- Övervaka dina kostnader med [budgetar](../costs/tutorial-acm-create-budgets.md), [aviseringar](../costs/cost-mgt-alerts-monitor-usage-spending.md) och [kostnadsanalys](../costs/quick-acm-cost-analysis.md).
- Granska kostnaderna på fakturan genom att jämföra dem med [de detaljerade användningsfilerna](download-azure-invoice-daily-usage-date.md).
- Integrera fakturerings- och kostnadsdata i ditt eget rapportsystem med hjälp av API:erna för [fakturering](https://docs.microsoft.com/rest/api/billing/) och [förbrukning](https://docs.microsoft.com/rest/api/consumption/).
- Använd ytterligare resurser och verktyg för Enterprise-avtal (EA), Cloud Solution Provider (CSP) och Azure Sponsorship-kunder.
- Använd [några av de populäraste Azure-tjänsterna utan kostnad i 12 månader](create-free-services.md) med ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Tillsammans med rekommendationerna nedan kan du läsa [Undvika att debiteras för ett kostnadsfritt konto](avoid-charges-free-account.md).

Om du behöver avbryta din Azure-prenumeration läser du [Avbryta din Azure-prenumeration](cancel-azure-subscription.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Få en kostnadsuppskattning innan du lägger till Azure-tjänster

Använd något av följande verktyg för att beräkna kostnaden för att använda en Azure-tjänst:
- Priskalkylator för Azure
- Azure-prisdokument
- Azure Portal

Bilderna i följande avsnitt innehåller exempel på priser i amerikanska dollar.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Beräkna kostnaden online med priskalkylatorn

Ta en titt på [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att beräkna månadskostnaden för den tjänst du vill lägga till. Du kan ändra valutan för att få uppskattningen i din lokala valuta.

![Skärmbild av menyn för priskalkylatorn](./media/getting-started/pricing-calc.png)

Du kan visa uppskattade kostnader för alla Azure-tjänster från första part. I skärmbilden nedan uppskattas till exempel beräkningstimmarna för en virtuell A1-dator med Windows kosta 66.96 USD i månaden om den körs utan avbrott:

![Skärmbild av priskalkylatorn som visar den beräknade månadskostnaden för en virtuell A1-dator med Windows](./media/getting-started/pricing-calcvm.png)

Mer information om priser finns i [vanliga frågor och svar om prissättning](https://azure.microsoft.com/pricing/faq/). Om du vill prata med en Azure-säljare ringer du telefonnumret som visas överst på sidan med vanliga frågor och svar.

### <a name="view-and-download-azure-price-sheet"></a>Visa och ladda ned Azure-prisdokument

Om du har åtkomst till Azure via ett Enterprise-avtal (EA) eller ett Microsoft-kundavtal (MCA) kan du visa och ladda ned prisdokumentet för ditt Azure-konto. Prisdokumentet är en Excel-fil som innehåller priser för alla Azure-tjänster. Mer information finns i [Visa och ladda ned dina Azure-priser](ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Gå igenom kostnadsuppskattningarna på Azure-portalen

Du kan visa den uppskattade kostnaden per månad när du lägger till en tjänst i Azure-portalen. När du till exempel väljer storleken på din virtuella Windows-dator kan du se den uppskattade månadskostnaden för beräkningstimmarna:

![Exempel: en virtuell A1-dator med Windows som visar den uppskattade månadskostnaden](./media/getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Övervaka kostnader när du använder Azure-tjänster
Du kan övervaka kostnader med följande verktyg:

- Budget-och kostnadsaviseringar
- Kostnadsanalys

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Spåra kostnader med budgetar och kostnadsaviseringar

Skapa [budgetar](../costs/tutorial-acm-create-budgets.md) för att hantera kostnader och skapa [aviseringar](../costs/cost-mgt-alerts-monitor-usage-spending.md) som automatiskt meddelar mottagarna om kostnadsavvikelser och överförbrukning.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> Utforska och analysera kostnader med kostnadsanalys

När dina Azure-tjänster är igång bör du regelbundet kontrollera kostnaderna så att du sparar dina utgifter för Azure. Du kan använda kostnadsanalys till att ta reda på var kostnaderna för din Azure-användning kommer ifrån.

1. Öppna [sidan Kostnadshantering och fakturering i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Klicka på **Kostnadsanalys** till vänster på skärmen så ser du aktuella kostnader uppdelade i olika kategorier som tjänst, plats och prenumeration. När du har lagt till en tjänst eller gör ett köp kan det ta upp till 24 timmar innan data visas. Som standard visas kostnaderna för den aktuella omfattningen i kostnadsanalysen. I skärmbilden nedan ser du till exempel kostnader för Contosos faktureringskonto. Använd reglaget Omfång om du vill växla till ett annat omfång i kostnadsanalysen. Mer information om omfång finns i [Förstå och arbeta med omfång](../costs/understand-work-scopes.md#scopes)

    ![Skärmbild av kostnadsanalysvyn på Azure-portalen](./media/getting-started/cost-analysis.png)

4. Du kan filtrera efter olika egenskaper som etiketter, resurstyper och tidsintervall. Klicka på **Lägg till filter** för att lägga till filtret för en egenskap och välja vilka värden du vill filtrera. Välj **Exportera** för att exportera vyn till en fil med kommaavgränsade värden (.csv).

5. Du kan också klicka på etiketterna i diagrammet om du vill se historiken för dagliga utgifter för etiketten. Här är ett exempel: I skärmbilden nedan ser du den dagliga kostnaden för att köra dina virtuella datorer genom att klicka på de virtuella datorerna.

    ![Skärmbild av utgiftshistorikvyn på Azure-portalen](./media/getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Optimera och minska kostnaderna
Mer information om kostnadshanteringsprinciperna finns i [Optimera molninvesteringen med Azure Cost Management](../costs/cost-mgt-best-practices.md).

På Azure-portalen kan du även optimera och minska kostnaderna för Azure med automatisk avstängning av virtuella datorer och Advisor-rekommendationer.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Överväg att använda kostnadsbesparingsfunktioner, t.ex. automatisk avstängning för virtuella datorer

Beroende på ditt scenario kan du konfigurera automatisk avstängning för dina virtuella datorerna på Azure-portalen. Mer information finns i [Automatisk avstängning för virtuella datorer med Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Skärmbild av alternativet för automatisk avstängning på portalen](./media/getting-started/auto-shutdown.png)

Automatisk avstängning är inte detsamma som när du stänger av den virtuella datorn med energialternativ. Automatisk avstängning stoppar och frigör de virtuella datorerna så att ytterligare användningskostnader stoppas. Mer information finns i avsnittet med vanliga frågor och svar om priser för [virtuella Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i olika VM-tillstånd.

Information om kostnadsbesparingsfunktioner för utvecklings- och testmiljöer finns i avsnittet om [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Aktivera och granska Azure Advisor-rekommendationer

[Azure Advisor](../../advisor/advisor-overview.md) hjälper dig att minska kostnaderna genom att identifiera resurser med låg användning. Sök efter **Advisor** i Azure-portalen:

![Skärmbild av knappen Azure Advisor på Azure-portalen](./media/getting-started/advisor-button.png)

Välj **Kostnad** till vänster. Du ser rekommendationer som kan åtgärdas på fliken **Kostnad**:

![Skärmbild av exempel på Advisor-kostnadsrekommendation](./media/getting-started/advisor-action.png)

Gå igenom självstudien [Optimera kostnader från rekommendationer](../costs/tutorial-acm-opt-recommendations.md) för en guidad självstudie om Advisor-rekommendationer för kostnadsbesparing.


## <a name="integrate-with-billing-and-consumption-apis"></a>Integrera med API:er för fakturering och förbrukning

Du kan använda Azure-API:erna för [fakturering](https://docs.microsoft.com/rest/api/billing/) och [förbrukning](https://docs.microsoft.com/rest/api/consumption/) till att hämta fakturerings- och kostnadsdata programmatiskt. Använd RateCard-API:et och användnings-API:et tillsammans för att visa din fakturerade användning. Mer information finns i [Få insikter om din resursförbrukning i Microsoft Azure](usage-rate-card-overview.md).

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> Ytterligare resurser och särskilda fall

### <a name="ea-csp-and-sponsorship-customers"></a>EA- och CSP-kunder och kunder med sponsring
Prata med din kontoansvarige eller Azure-partner för att komma igång.

| Erbjudande | Resurser |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise-avtal (EA) | [EA-portal](https://ea.azure.com/), [hjälpdokument](https://ea.azure.com/helpdocs) och [Power BI-rapport](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Leverantör av molnlösningar (CSP) | Prata med din leverantör |
| Azure-sponsring | [Sponsringsportalen](https://www.microsoftazuresponsorships.com/) |

Om du hanterar IT för en stor organisation rekommenderar vi att du läser [Azure Enterprise-kodskelett](/azure/architecture/cloud-adoption-guide/subscription-governance) och [faktabladet om företags-IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (nedladdning av PDF-fil, endast på engelska).

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Kostnadsvyer för Enterprise-avtal på Azure-portalen

Enterprise-kostnadsvyer finns för närvarande som en offentlig förhandsversion. Saker att tänka på:

- Prenumerationskostnader baseras på användning och inkluderar inte förbetalda belopp, överförbrukning, inkluderade antal, justeringar eller moms. Faktiska avgifter beräknas på registreringsnivån.
- Beloppen som visas på Azure-portalen kan skilja sig från de som visas på Enterprise-portalen. Det kan ta några minuter innan ändringarna på Enterprise-portalen visas på Azure-portalen.
- Om du inte ser några kostnader kan det bero på något av följande:
    - Du har inte behörighet på prenumerationsnivå. Om du vill visa vyer för företagskostnader måste du ha rollen Faktureringsläsare, Läsare, Deltagare eller Ägare på prenumerationsnivå.
    - Du är kontoägare och registreringsadministratören har inaktiverat inställningen Visa debiteringar för kontoägare.  Kontakta registreringsadministratören för att få åtkomst till kostnaderna.
    - Du är avdelningsadministratör och registreringsadministratören har inaktiverat inställningen **Visa debiteringar** för avdelningsadministratörer.  Kontakta registreringsadministratören för att få åtkomst.
    - Du har köpt Azure via en kanalpartner och partnern publicerade ingen prisinformation.  
- Om du uppdaterar inställningar för kostnader och åtkomst i Enterprise-portalen kan det ta några minuter innan du ser ändringarna i Azure-portalen.
- Utgiftsgräns och fakturahjälp gäller inte för EA-prenumerationer.

### <a name="check-your-subscription-and-access"></a>Kontrollera din prenumeration och åtkomst

För att visa kostnader behöver du åtkomst på konto- eller prenumerationsnivå för kostnads- eller faktureringsinformationen. Åtkomsten varierar beroende på typen av faktureringskonto. Du kan läsa mer om faktureringskonton och se vilken typ av faktureringskonto du har i [Visa faktureringskonton i Azure-portalen](view-all-accounts.md).

Om du har åtkomst till Azure via ett MOSP-faktureringskonto (Microsoft Online Service Program) kan du läsa [Hantera åtkomst till faktureringsinformation för Azure ](manage-billing-access.md).

Om du har åtkomst till Azure via ett EA-faktureringskonto (EA) kan du läsa [Förstå administrativa roller för Azure Enterprise-avtal i Azure](understand-ea-roles.md).

Om du har åtkomst till Azure via ett MCA-faktureringskonto (Microsoft-kundavtal) kan du läsa [Förstå administrativa roller för Microsoft-kundavtal i Azure](understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Begära en SLA-kredit för en tjänstincident

Serviceavtalet beskriver Microsofts åtaganden vad gäller drifttid och anslutning. En tjänstincident, eller *avbrott*, rapporteras när det har uppstått problem med Azure-tjänster som påverkar driften eller anslutningen till tjänsten i fråga. Om vi inte lyckas upprätthålla tjänstnivåerna för respektive tjänst enligt relevant SLA kan du ha rätt till en kredit för en del av dina månatliga tjänstavgifter.

Så här begär du en kredit:

1. Logga in på [Azure-portalen](https://portal.azure.com/). Om du har flera konton kontrollerar du att du använder det som påverkades av avbrottet i Azure.
2. Skapa en ny supportbegäran.
3. Välj **Fakturering** under **Typ av problem**.
4. Välj **Begäran om återbetalning** under **Typ av problem**.
5. Förklara att du ber om en SLA-kredit, ange datum/tid/tidszon samt de berörda tjänsterna (virtuella datorer, webbplatser osv.)
6. Verifiera dina kontaktuppgifter och välj **Skapa** för att skicka in din begäran.

SLA-tröskelvärden varierar beroende på tjänst. Till exempel har SQL Web-nivån ett serviceavtal på 99,9 %, virtuella datorer har ett serviceavtal på 99,95 % och SQL Standard-nivån har ett serviceavtal på 99,99 %.

För vissa tjänster måste särskilda krav vara uppfyllda för att serviceavtalet ska gälla. Till exempel måste virtuella datorer ha två eller fler distribuerade instanser i samma tillgänglighetsuppsättning.

Mer information finns i avsnittet [Servicenivåavtal](https://azure.microsoft.com/support/legal/sla/) och [Sammanfattning av serviceavtal för Azure-tjänster](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="analyze-unexpected-charges"></a>Analysera oväntade kostnader

Den infrastruktur du skapat för organisationens molnresurser är förmodligen komplex. Många Azure-resurstyper kan ha olika typer av avgifter. Azure-resurser kan ägas av olika team i organisationen och de kan ha olika typer av faktureringsmodeller som gäller för olika resurser. För att få en bättre förståelse för avgifterna kan du göra en analys med hjälp av en eller flera av strategierna i följande avsnitt.

### <a name="review-your-invoice-and-identify-the-resource-that-is-responsible-for-the-charge"></a>Granska din faktura och identifiera vilken resurs som bär ansvaret för avgiften

Hur du köper Azure-tjänster hjälper dig att avgöra vilken metodik och vilka verktyg som är tillgängliga när du identifierar vilken resurs som är kopplad till en avgift. För att avgöra vilken metod som gäller för dig bör du först [identifiera din typ av Azure-erbjudande](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Identifiera sedan din kundkategori i listan med [Azure-erbjudanden som stöds](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

Följande artiklar innehåller detaljerade anvisningar som förklarar hur du granskar din faktura baserat på kundtyp. I varje artikel finns anvisningar om hur du hämtar en CSV-fil som innehåller användnings- och kostnadsinformation för en viss faktureringsperiod.

- [Process för granskning av faktura för betala per användning](../understand/review-individual-bill.md#compare-invoiced-charges-with-usage-file)
- [Process för granskning av faktura för Enterprise-avtal](../understand/review-enterprise-agreement-bill.md)
- [Process för att granska Microsoft-kundavtal](../understand/review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Process för att granska Microsoft-partneravtal](../understand/review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

På din Azure-faktura samlas avgifter för månaden per _mätare_. Mätare används för att spåra en resurs användning över tid och används för att beräkna din faktura. När du skapar en enskild Azure-resurs, till exempel en virtuell dator, skapas en eller flera mätare för den resursen.

Filtrera CSV-filen över användning på _Mätarnamn_ som det visas på den faktura du vill analysera för att se alla poster som gäller den mätaren. _Instans-ID_ för radposten motsvarar den faktiska Azure-resursen som gett upphov till kostnaden.

När du har identifierat den aktuella resursen kan du använda Kostnadsanalys i Azure Cost Management för att vidare analysera kostnaderna för resursen. Läs mer om att använda kostnadsanalys i [Börja analysera kostnader](../costs/quick-acm-cost-analysis.md).

### <a name="identify-spikes-in-cost-over-time"></a>Identifiera toppar i kostnaderna över tid

Ibland kanske du inte vet vilka kostnader som har lett till förändringar av de fakturerade avgifterna. För att förstå vad som har förändrats kan du använda Kostnadsanalys för att [visa en uppdelning per dag eller månad av kostnaderna över tid](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). När du har skapat vyn ska du gruppera dina avgifter efter antingen **tjänst** eller **resurs** för att identifiera ändringarna. Du kan också ändra vyn till ett **linje**diagram för att visualisera data bättre.

![Exempel som visar kostnader över tid i kostnadsanalys](./media/getting-started/costs-over-time.png)

### <a name="determine-resource-pricing-and-understand-its-billing-model"></a>Fastställa resurspriser och förstå faktureringsmodellen

En enskild resurs kan ge upphov till kostnader på flera Azure-produkter och -tjänster. Se sidan med [Azure-priser per produkt](https://azure.microsoft.com/pricing/#product-pricing) för att läsa mer om priserna för respektive Azure-tjänst. Till exempel kan en enskild virtuell dator (VM) som skapats i Azure ha följande mätare som skapas för att spåra dess användning. Var och en kan ha olika priser.

- Beräkningstimmar
- IP-adresstimmar
- Inkommande dataöverföring
- Utgående dataöverföring
- Standard – hanterad disk
- Standard – åtgärder för hanterad disk
- Standard – I/O-disk
- Standard – I/O-blockblobsläsning
- Standard – I/O-blockblobsskrivning
- Standard – I/O-blockblobsborttagning

När den virtuella datorn skapas börjar varje mätare att skicka användningsposter. Användningen och priset för mätaren spåras i Azure-mätarsystemet. Du kan se de mätare som användes för att beräkna din faktura i CSV-filen för användning.

### <a name="find-the-people-responsible-for-the-resource-and-engage-them"></a>Hitta de användare som ansvarar för resursen och engagera dem

Ofta vet det team som ansvarar för en viss resurs vilka ändringar som har gjorts för en resurs. Att engagera dem är till hjälp eftersom du får veta varför kostnaderna kanske uppkommer. Det ägnade teamet kanske till exempel nyligen har skapat resursen, uppdaterat dess SKU (och därmed ändrat resurstaxan) eller ökat belastningen på resursen på grund av kodändringar. Fortsätt att läsa nedanstående avsnitt för mer information om hur du kan avgöra vem som äger en resurs.

#### <a name="analyze-the-audit-logs-for-the-resource"></a>Analysera spårningsloggarna för resursen

Om du har behörighet att visa en resurs bör du även ha åtkomst till dess spårningsloggar. Granska loggarna för att ta reda på vilken användare som är ansvarig för de senaste ändringarna av en resurs. Mer information finns i [Visa och hämta Azure aktivitetslogghändelser](../../azure-monitor/platform/activity-log-view.md).

#### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analysera användarbehörigheter till resursens överordnade omfång

Användare som har skrivåtkomst till en prenumeration eller resursgrupp har ofta information om de resurser som skapats. De bör kunna förklara syftet med en resurs eller hänvisa dig till någon som vet. Mer information om att identifiera användare med behörighet för ett prenumerationsomfång finns i [Visa rolltilldelningar](../../role-based-access-control/check-access.md#view-role-assignments). Du kan använda en liknande process för resursgrupper.

### <a name="get-help-to-identify-charges"></a>Få hjälp med att identifiera kostnader

Om du har använt ovanstående strategier och fortfarande inte förstår varför du har debiterats en kostnad, eller om du behöver hjälp med andra fakturafrågor, kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om hur du använder [utgiftsgränser](spending-limit.md) för att förhindra överförbrukning.
- Börja [analysera dina Azure-kostnader](../costs/quick-acm-cost-analysis.md).
