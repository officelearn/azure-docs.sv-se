---
title: Förhindra oväntade kostnader och hantera fakturering i Azure
description: Lär dig hur du undviker oväntade avgifter på din Azure-faktura. Använd tvärspårnings- och hanteringsfunktioner för en Azure-prenumeration.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: d6c287d5ead0095a4f7bb5ad754212b134f7103c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719805"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Förhindra oväntade avgifter med Azure-fakturering och kostnadshantering

När du registrerar dig för Azure finns det flera saker du kan göra för att få en bättre uppfattning om dina utgifter:

- [Priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) kan ge en uppskattning av kostnaderna innan du skapar en Azure-resurs. 

- På [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) kan du visa den aktuella kostnadsuppdelningen och prognosen för din prenumeration. 

- Om du vill gruppera och förstå kostnader för olika projekt eller team kan du läsa mer om [resurstaggning](../azure-resource-manager/resource-group-using-tags.md). Om din organisation har ett rapporteringssystem som du föredrar att använda kan du läsa mer om [fakturerings-API:erna](billing-usage-rate-card-overview.md).

- Om din prenumeration skapades från ett Enterprise-avtal (EA) kan du se dina kostnader på Azure-portalen. Om din prenumeration skapades genom en leverantör av molnlösningar (CSP) eller Azure-sponsring, kanske vissa av följande funktioner inte gäller dig. Mer information finns i [Fler resurser för EA, CSP och sponsring](#other-offers).

- Om din prenumeration är ett kostnadsfritt utvärderingserbjudande, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure i Open (AIO) eller BizSpark, inaktiveras din prenumeration automatiskt när alla dina krediter förbrukats. Lär dig mer om [utgiftsgränser](#spending-limit) för att undvika att din prenumeration oväntat inaktiveras.

- Om du har registrerat dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) [kan du använda några av de populäraste Azure-tjänsterna kostnadsfritt i 12 månader](billing-create-free-services-included-free-account.md). Tillsammans med rekommendationerna nedan kan du läsa [Undvika att debiteras för ett kostnadsfritt konto](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Få en kostnadsuppskattning innan du lägger till Azure-tjänster

Här följer ytterligare information om hur du beräknar kostnader med hjälp av följande verktyg:
- Priskalkylator för Azure
- Azure Portal
- Utgiftsgräns

Bilderna i följande avsnitt innehåller exempel på priser i amerikanska dollar.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Beräkna kostnaden online med priskalkylatorn

Prova [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) och beräkna månadskostnaden för den tjänst som du är intresserad av. Du kan lägga till valfri Azure-resurs från första part för att få en kostnadsuppskattning. Du kan ändra valutatyp i priskalkylatorn.

![Skärmbild av menyn för priskalkylatorn](./media/billing-getting-started/pricing-calc.png)

I priskalkylatorn beräknas exempelvis en virtuell A1-dator med Windows kosta ett visst belopp per månad i antal beräkningstimmar om den körs utan avbrott:

![Skärmbild av priskalkylatorn som visar den beräknade månadskostnaden för en virtuell A1-dator med Windows](./media/billing-getting-started/pricing-calcvm.png)

Mer information om priser finns i [vanliga frågor och svar om prissättning](https://azure.microsoft.com/pricing/faq/). Om du vill prata med en Azure-säljare ringer du telefonnumret som visas överst på sidan med vanliga frågor och svar.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Gå igenom kostnadsuppskattningarna på Azure-portalen

När du lägger till en tjänst på Azure-portalen visas normalt en vy med den uppskattade kostnaden per månad i din faktureringsvaluta. När du till exempel väljer storleken på din virtuella Windows-dator kan du se den uppskattade månadskostnaden för beräkningstimmarna:

![Exempel: en virtuell A1-dator med Windows som visar den uppskattade månadskostnaden](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a> Kontrollera om en utgiftsgräns är aktiverad

Om du har en prenumeration som använder krediter är utgiftsgränsen aktiverad som standard. På så sätt debiteras inte ditt kreditkort när du har utnyttjat alla dina krediter. Se den [fullständiga listan med Azure-erbjudanden och information om tillgängliga utgiftsgränser](https://azure.microsoft.com/support/legal/offer-details/).

Dock inaktiveras tjänsterna när du når utgiftsgränsen. Det innebär att dina virtuella datorer frigörs. Du måste inaktivera utgiftsgränsen för att undvika avbrott i tjänsterna. All överförbrukning debiteras på ditt registrerade kreditkort.

Om du vill se om en utgiftsgräns är aktiverad går du till [prenumerationsvyn i Kontocenter](https://account.windowsazure.com/Subscriptions). En banderoll visas om din utgiftsgräns är aktiverad, ungefär så här:

![Skärmbild som visar en varning om utgiftsgränsen i Kontocenter](./media/billing-getting-started/spending-limit-banner.png)

Klicka på banderollen och följ anvisningarna för att ta bort utgiftsgränsen. Om du inte uppgav någon kreditkortsinformation när du registrerade dig måste du ange den för att ta bort utgiftsgränsen. Mer information finns i [Azure-utgiftsgränser – Så här fungerar utgiftsgränser och så här aktiverar eller inaktiverar du dem](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="use-budgets-and-cost-alerts"></a>Använda budgetar och kostnadsaviseringar

Du kan skapa [budgetar](../cost-management/tutorial-acm-create-budgets.md) för att hantera kostnader och skapa [aviseringar](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar.

## <a name="monitor-costs-when-using-azure-services"></a>Övervaka kostnader när du använder Azure-tjänster
Du kan övervaka kostnader med följande verktyg:

- Taggar
- Kostnadsuppdelning och förbrukningstakt
- Kostnadsanalys

### <a name="tags"></a> Lägg till taggar till resurser för att gruppera faktureringsdata

Du kan använda taggar för att gruppera faktureringsdata för tjänster som stöds. Om du till exempel kör flera virtuella datorer för olika team kan du använda taggar för att kategorisera kostnader efter kostnadsställe (till exempel HR, marknadsföring, ekonomi osv.) eller miljö (till exempel produktion, förproduktion eller test).

![Skärmbild som visar hur du konfigurerar taggar på portalen](./media/billing-getting-started/tags.png)

Taggarna visas i olika kostnadsrapporteringsvyer. Exempelvis visas de direkt i [kostnadsanalysvyn](#costs) och i CSV-filen med detaljerad användningsinformation efter din första faktureringsperiod.

Mer information finns i [Ordna dina Azure-resurser med hjälp av taggar](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Övervaka kostnadsuppdelning och förbrukningstakt

När dina Azure-tjänster är igång bör du regelbundet kontrollera kostnaderna. Du kan se de aktuella utgifterna och förbrukningstakten på Azure-portalen.

1. Gå till [Prenumerationer på Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välj en prenumeration.

2. Om det stöds för din prenumeration kan du se kostnadsuppdelningen och förbrukningstakten.

    ![Skärmbild av förbrukningstakten och kostnadsuppdelningen på Azure-portalen](./media/billing-getting-started/burn-rate.PNG)

3. Klicka på [Kostnadsanalys](../cost-management/quick-acm-cost-analysis.md) i listan till vänster om du vill visa kostnadsuppdelningen efter resurs. När du har lagt till en tjänst kan det dröja upp till 24 timmar innan data visas.

    ![Skärmbild av kostnadsanalysvyn på Azure-portalen](./media/billing-getting-started/cost-analysis.png)

4. Du kan filtrera efter olika egenskaper, t.ex. [taggar](#tags), resurstyp, resursgrupp och tidsintervall. Klicka på **Använd** för att bekräfta filtren och **Ladda ned** om du vill exportera vyn till en fil med kommaavgränsade värden (.csv).

5. Du kan också klicka på en resurs för att se din dagliga utgiftshistorik och hur mycket resurserna kostar varje dag.

    ![Skärmbild av utgiftshistorikvyn på Azure-portalen](./media/billing-getting-started/costhistory.png)

Jämför kostnaderna som du ser med uppskattningarna som visades när du valde tjänsterna. Om kostnaderna skiljer sig avsevärt från uppskattningarna kontrollerar du vilken prisplan du har valt för dina resurser.

## <a name="optimize-and-reduce-costs"></a>Optimera och minska kostnaderna
Mer information om kostnadshanteringsprinciperna finns i [Optimera molninvesteringen med Azure Cost Management](../cost-management/cost-mgt-best-practices.md).

På Azure-portalen kan du även optimera och minska kostnaderna för Azure med automatisk avstängning av virtuella datorer och Advisor-rekommendationer.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Överväg att använda kostnadsbesparingsfunktioner, t.ex. automatisk avstängning för virtuella datorer

Beroende på ditt scenario kan du konfigurera automatisk avstängning för dina virtuella datorerna på Azure-portalen. Mer information finns i [Automatisk avstängning för virtuella datorer med Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Skärmbild av alternativet för automatisk avstängning på portalen](./media/billing-getting-started/auto-shutdown.png)

Automatisk avstängning är inte detsamma som när du stänger av den virtuella datorn med energialternativ. Automatisk avstängning stoppar och frigör de virtuella datorerna så att ytterligare användningskostnader stoppas. Mer information finns i avsnittet med vanliga frågor och svar om priser för [virtuella Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i olika VM-tillstånd.

Information om kostnadsbesparingsfunktioner för utvecklings- och testmiljöer finns i avsnittet om [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Aktivera och granska Azure Advisor-rekommendationer

[Azure Advisor](../advisor/advisor-overview.md) hjälper dig att minska kostnaderna genom att identifiera resurser med låg användning. Gå till Advisor på Azure-portalen:

![Skärmbild av knappen Azure Advisor på Azure-portalen](./media/billing-getting-started/advisor-button.png)

Du kan få rekommendationer på fliken **Kostnad** på instrumentpanelen för Advisor:

![Skärmbild av exempel på Advisor-kostnadsrekommendation](./media/billing-getting-started/advisor-action.png)

Gå igenom självstudien [Optimera kostnader från rekommendationer](../cost-management/tutorial-acm-opt-recommendations.md) för en guidad självstudie om Advisor-rekommendationer för kostnadsbesparing.

## <a name="review-costs-against-your-latest-invoice"></a>Gå igenom kostnaderna på din senaste faktura

Den senaste fakturan är tillgänglig i slutet av faktureringsperioden. Du kan också [ladda ned fakturor och detaljerade användningsfiler](billing-download-azure-invoice-daily-usage-date.md) för att kontrollera att du debiteras korrekt. Mer information om hur du jämför din dagliga användning med din faktura finns i [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>Fakturerings-API

Använd Azures fakturerings-API för att hämta användningsdata via programmering. Använd RateCard-API:et och användnings-API:et tillsammans för att visa din fakturerade användning. Mer information finns i [Få insikter om din resursförbrukning i Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Ytterligare resurser och särskilda fall

### <a name="ea-csp-and-sponsorship-customers"></a>EA- och CSP-kunder och kunder med sponsring
Prata med din kontoansvarige eller Azure-partner för att komma igång.

| Erbjudande | Resurser |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise-avtal (EA) | [EA-portal](https://ea.azure.com/), [hjälpdokument](https://ea.azure.com/helpdocs) och [Power BI-rapport](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Leverantör av molnlösningar (CSP) | Prata med din leverantör |
| Azure-sponsring | [Sponsringsportalen](https://www.microsoftazuresponsorships.com/) |

Om du hanterar IT för en stor organisation rekommenderar vi att du läser [Azure Enterprise-kodskelett](/azure/architecture/cloud-adoption-guide/subscription-governance) och [faktabladet om företags-IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (nedladdning av PDF-fil, endast på engelska).

#### <a name="EA"></a> Kostnadsvyer för Enterprise-avtal på Azure-portalen

Enterprise-kostnadsvyer finns för närvarande som en offentlig förhandsversion. Saker att tänka på:

- Prenumerationskostnader baseras på användning och inkluderar inte förbetalda belopp, överförbrukning, inkluderade antal, justeringar eller moms. Faktiska avgifter beräknas på registreringsnivån.
- Beloppen som visas på Azure-portalen kan skilja sig från de som visas på Enterprise-portalen. Det kan ta några minuter innan ändringarna på Enterprise-portalen visas på Azure-portalen.
- Om du inte ser några kostnader kan det bero på något av följande:
    - Du har inte behörighet på prenumerationsnivå. Om du vill visa vyer för företagskostnader måste du ha rollen Faktureringsläsare, Läsare, Deltagare eller Ägare på prenumerationsnivå.
    - Du är kontoägare och registreringsadministratören har inaktiverat inställningen Visa debiteringar för kontoägare.  Kontakta registreringsadministratören för att få åtkomst till kostnaderna.
    - Du är avdelningsadministratör och registreringsadministratören har inaktiverat inställningen **Visa debiteringar** för avdelningsadministratörer.  Kontakta registreringsadministratören för att få åtkomst.
    - Du har köpt Azure via en kanalpartner och partnern publicerade ingen prisinformation.  
- Om du uppdaterar inställningar relaterade till kostnadsåtkomst på Enterprise-portalen uppstår en fördröjning på några minuter innan ändringarna visas på Azure-portalen.
- Utgiftsgräns och fakturahjälp gäller inte för EA-prenumerationer.

### <a name="check-your-subscription-and-access"></a>Kontrollera din prenumeration och åtkomst

Om du vill visa kostnader måste du [ha åtkomst till faktureringsinformation på prenumerationsnivå](billing-manage-access.md). Endast kontoadministratören kan komma åt [Kontocenter](https://account.azure.com/Subscriptions), ändra faktureringsinformation och hantera prenumerationer. Kontoadministratören är den person som gick igenom registreringsprocessen. Mer information finns i [Lägga till eller ändra Azure-administratörsroller som hanterar prenumerationen eller tjänsterna](billing-add-change-azure-subscription-administrator.md).

Du kan se om du är kontoadministratören genom att gå till [Prenumerationer på Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Visa listan över prenumerationer och leta upp **Min roll**. Om *Kontoadministratör* visas som rollen, betyder det att du har fullständig behörighet. Om det står något annat, t.ex. *Ägare*, har du inte fullständig behörighet.

![Skärmbild av din roll i vyn Prenumerationer på Azure-portalen](./media/billing-getting-started/sub-blade-view.PNG)

Om du vill hantera prenumerationer och ändra faktureringsinformation [letar du reda på kontoadministratören](billing-subscription-transfer.md#whoisaa). Be kontoadministratören att utföra uppgifterna eller att [överföra prenumerationen till dig](billing-subscription-transfer.md).

Om kontoadministratören inte längre arbetar i din organisation och du behöver hantera faktureringen [kontaktar du oss](https://go.microsoft.com/fwlink/?linkid=2083458).


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

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om hur du använder [utgiftsgränser](billing-spending-limit.md) för att förhindra överförbrukning.
- Börja [analysera dina Azure-kostnader](../cost-management/quick-acm-cost-analysis.md).
