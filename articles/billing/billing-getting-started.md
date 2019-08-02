---
title: Förhindra oväntade kostnader och hantera fakturering i Azure
description: Lär dig hur du undviker oväntade avgifter på din Azure-faktura. Använd funktioner för kostnads spårning och hantering för en Azure-prenumeration.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b64e84c3fff27675029ff35f27972a4aca014ec3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612097"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Förhindra oväntade avgifter med fakturering och kostnads hantering i Azure

När du registrerar dig för Azure finns det flera saker du kan göra för att få en bättre uppfattning om dina utgifter:

- [Pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) kan ge en uppskattning av kostnaderna innan du skapar en Azure-resurs. 

- [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ger dig den aktuella kostnads uppdelningen och prognosen för din prenumeration. 

- Om du vill gruppera och förstå kostnader för olika projekt eller team kan du titta på [resurs taggning](../azure-resource-manager/resource-group-using-tags.md). Om din organisation har ett rapporterings system som du föredrar att använda kan du kolla in fakturerings- [API: erna](billing-usage-rate-card-overview.md).

- Om din prenumeration har skapats från en Enterprise-avtal (EA) kan du se dina kostnader i Azure Portal. Om din prenumeration är via en leverantör av moln lösningar (CSP) eller Azure-sponsring, kan vissa av följande funktioner inte tillämpas på dig. Mer information finns i [ytterligare resurser för EA, CSP och sponsring](#other-offers).

- Om din prenumeration är ett kostnads fritt utvärderings erbjudande, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure I Open (AIO) eller BizSpark inaktive ras din prenumeration automatiskt när alla krediter används. Lär dig mer om [utgifts gränser](#spending-limit) för att undvika att din prenumeration har inaktiverats av oväntad anledning.

- Om du har registrerat dig för ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) [kan du använda några av de mest populära Azure-tjänsterna kostnads fritt i 12 månader](billing-create-free-services-included-free-account.md). Tillsammans med rekommendationerna nedan kan du läsa [mer i undvika att debiteras kostnads fritt konto](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Få beräknade kostnader innan du lägger till Azure-tjänster

Här följer ytterligare information om hur du uppskattar kostnader med följande verktyg:
- Priskalkylator för Azure
- Azure Portal
- Utgiftsgräns

Bilderna i följande avsnitt visar exempel på prissättning i amerikanska dollar.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Uppskatta kostnaden online med pris kalkylatorn

Ta en titt på [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att få en uppskattad månads kostnad för tjänsten som du är intresse rad av. Du kan lägga till valfri Azure-resurs från första part för att få en uppskattad kostnad. I pris Kalkylatorn kan du ändra valuta typ.

![Skärm bild av pris Kalkylatorns meny](./media/billing-getting-started/pricing-calc.png)

I pris kalkylatorn beräknas till exempel en a1 virtuell Windows-dator (VM) för att kosta en viss mängd/månad under beräknings timmar om du låter den köra hela tiden:

![Skärm bild av pris kalkylatorn som visar en a1 beräknad kostnad för Windows VM per månad](./media/billing-getting-started/pricing-calcvm.png)

Mer information om priser finns i [vanliga frågor och svar om prissättning](https://azure.microsoft.com/pricing/faq/). Om du vill prata med en Azure-säljare ska du ringa telefonnumret som visas överst på sidan med vanliga frågor och svar.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Granska uppskattade kostnader i Azure Portal

När du lägger till en tjänst i Azure Portal är det normalt en vy som visar en uppskattad kostnad per månad i din fakturerade valuta. Om du till exempel väljer storleken på din virtuella Windows-dator kan du se den uppskattade månads kostnaden för beräknings timmarna:

![Exempel: en a1 Windows VM som visar uppskattad kostnad per månad](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a>Kontrol lera om du har en utgifts gräns på

Om du har en prenumeration som använder kredit, aktive ras utgifts gränsen för dig som standard. På så sätt får ditt kredit kort inte debiteras när du lägger ut alla dina krediter. Se den [fullständiga listan med Azure-erbjudanden och tillgängligheten för utgifts gränsen](https://azure.microsoft.com/support/legal/offer-details/).

Men när du når utgifts gränsen kommer tjänsterna att inaktive ras. Det innebär att dina virtuella datorer är friallokerade. Du måste inaktivera utgifts gränsen för att undvika avbrott i tjänsten. All överanvändning debiteras på ditt kredit kort i filen.

Om du vill se om du har en utgifts gräns på går du till [vyn prenumerationer i konto centret](https://account.windowsazure.com/Subscriptions). En banderoll visas om din utgifts gräns är på, ungefär så här:

![Skärm bild som visar en varning om utgifts gränsen i konto centret](./media/billing-getting-started/spending-limit-banner.png)

Klicka på banderollen och följ anvisningarna för att ta bort utgifts gränsen. Om du inte angav kredit korts information när du registrerade dig måste du ange den för att ta bort utgifts gränsen. Mer information finns i [utgifts gränsen för Azure – hur det fungerar och hur du aktiverar eller tar bort den](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="use-budgets-and-cost-alerts"></a>Använda budgetar och kostnads varningar

Du kan skapa [budgetar](../cost-management/tutorial-acm-create-budgets.md) för att hantera kostnader [](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) och skapa aviseringar som automatiskt meddelar intressenter om utgifts avvikelser och överförbrukning av risker. Aviseringar baseras på utgifter jämfört med budget-och kostnads trösklar.

## <a name="monitor-costs-when-using-azure-services"></a>Övervaka kostnader när du använder Azure-tjänster
Du kan övervaka kostnader med följande verktyg:

- Tags
- Kostnads uppdelning och brännings takt
- Kostnadsanalys

### <a name="tags"></a>Lägg till taggar till resurser för att gruppera fakturerings data

Du kan använda taggar för att gruppera fakturerings data för tjänster som stöds. Om du till exempel kör flera virtuella datorer för olika team kan du använda taggar för att kategorisera kostnader efter kostnads ställe (till exempel: HR, marknadsföring, ekonomi osv.) eller-miljö (till exempel: produktion, för produktion, test).

![Skärm bild som visar hur du konfigurerar Taggar i portalen](./media/billing-getting-started/tags.png)

Taggarna visas i olika vyer för kostnads rapportering. De är till exempel synliga i [vyn kostnads analys](#costs) direkt och i den detaljerade användningen CSV-filen efter din första fakturerings period.

Mer information finns i [använda taggar för att ordna dina Azure-resurser](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a>Övervaka kostnads analys och brännings takt

När du har dina Azure-tjänster igång måste du kontrol lera kostnaderna regelbundet. Du kan se de aktuella utgifterna och brännings takten i Azure Portal.

1. Besök [prenumerationerna i Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välj en prenumeration.

2. Om den stöds för din prenumeration kan du se kostnads uppdelning och brännings takten.

    ![Skärm bild av brännings takt och detalj nivå i Azure Portal](./media/billing-getting-started/burn-rate.PNG)

3. Klicka på [kostnads analys](../cost-management/quick-acm-cost-analysis.md) i listan till vänster om du vill se kostnads uppdelning per resurs. När du har lagt till en tjänst väntar du 24 timmar på att de data som ska visas.

    ![Skärm bild av vyn kostnads analys i Azure Portal](./media/billing-getting-started/cost-analysis.png)

4. Du kan filtrera efter olika egenskaper, t. ex. [taggar](#tags), resurs typ, resurs grupp och TimeSpan. Klicka på **tillämpa** för att bekräfta filtren och **hämtningen** om du vill exportera vyn till en fil med kommaavgränsade värden (. csv).

5. Dessutom kan du klicka på en resurs för att se din dagliga utgifts historik och hur mycket resurs kostnader varje dag.

    ![Skärm bild av vyn utgifts historik i Azure Portal](./media/billing-getting-started/costhistory.png)

Jämför kostnaderna som du ser med de uppskattningar som du såg när du valde tjänsterna. Om kostnaderna skiljer sig avsevärt från uppskattningarna kontrollerar du vilken pris plan som du har valt för dina resurser.

## <a name="optimize-and-reduce-costs"></a>Optimera och minska kostnaderna
Om du inte känner till principerna för kostnads hantering kan du läsa [hur du optimerar din moln investering med Azure Cost Management](../cost-management/cost-mgt-best-practices.md).

I Azure Portal kan du också optimera och minska kostnaderna för Azure med automatisk avstängning för VM-och Advisor-rekommendationer.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Överväg kostnader för att klippa ut funktioner som automatisk avstängning för virtuella datorer

Beroende på ditt scenario kan du konfigurera automatisk avstängning för de virtuella datorerna i Azure Portal. Mer information finns i [Automatisk avstängning för virtuella datorer med hjälp av Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Skärm bild av alternativet för automatisk avstängning i portalen](./media/billing-getting-started/auto-shutdown.png)

Automatisk avstängning är inte detsamma som när du stänger av den virtuella datorn med energi alternativ. Automatisk avstängning stoppar och avallokerar de virtuella datorerna för att stoppa ytterligare användnings kostnader. Mer information finns i vanliga frågor och svar om prissättning för virtuella [Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och virtuella [Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) för VM-tillstånd.

Om du vill ha mer kostnads besparingar för dina utvecklings-och test miljöer kan du läsa [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Aktivera och granska Azure Advisor rekommendationer

[Azure Advisor](../advisor/advisor-overview.md) hjälper dig att minska kostnaderna genom att identifiera resurser med låg användning. Besök Advisor i Azure Portal:

![Skärm bild av knappen Azure Advisor i Azure Portal](./media/billing-getting-started/advisor-button.png)

Du kan få rekommendationer på fliken **kostnad** på instrument panelen för Advisor:

![Skärm bild av exempel på Advisor-kostnads rekommendation](./media/billing-getting-started/advisor-action.png)

Läs kursen [optimera kostnader från rekommendationer](../cost-management/tutorial-acm-opt-recommendations.md) för en guidad självstudie om rekommendationer för kostnads besparingar.

## <a name="review-costs-against-your-latest-invoice"></a>Granska kostnaderna mot din senaste faktura

Den senaste fakturan är tillgänglig i slutet av fakturerings perioden. Du kan också [Hämta fakturor och detaljerade användnings filer](billing-download-azure-invoice-daily-usage-date.md) för att se till att du debiteras korrekt. Mer information om hur du jämför din dagliga användning med din faktura finns i [förstå faktureringen för Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>Fakturerings-API

Använd Azures fakturerings-API för att hämta användnings data program mässigt. Använd RateCard-API: et och användnings-API: et tillsammans för att få din fakturerings bara användning. Mer information finns i få insikter om [din Microsoft Azure resursförbrukning](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a>Ytterligare resurser och särskilda fall

### <a name="ea-csp-and-sponsorship-customers"></a>EA-, CSP-och sponsring-kunder
Prata med din konto ansvarige eller Azure-partner för att komma igång.

| Erbjudande | Resurser |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise-avtal (EA) | [EA-portalen](https://ea.azure.com/), [hjälp dokument](https://ea.azure.com/helpdocs)och [Power BI rapport](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | Prata med din leverantör |
| Azure-sponsring | [Sponsring Portal](https://www.microsoftazuresponsorships.com/) |

Om du hanterar den för en stor organisation rekommenderar vi att du läser [Azure Enterprise-Autogenerera](/azure/architecture/cloud-adoption-guide/subscription-governance) och [företagets IT-White Paper](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (. pdf-hämtning, endast engelska).

#### <a name="EA"></a>Enterprise-avtal Cost views i Azure Portal

Enterprise Cost views finns för närvarande i en offentlig för hands version. Objekt att Observera:

- Prenumerations kostnader baseras på användning och inkluderar inte förbetalda belopp, överförbrukning, inkluderade kvantiteter, justeringar och skatter. Faktiska avgifter beräknas på registrerings nivån.
- Beloppen som visas i Azure Portal kan vara annorlunda än vad som finns i Enterprise Portal. Det kan ta några minuter innan ändringarna visas i Azure Portal på Enterprise Portal.
- Om du inte ser några kostnader kan det bero på någon av följande orsaker:
    - Du har inte behörighet på prenumerations nivån. Om du vill visa vyer för företags kostnader måste du vara fakturerings läsare, läsare, deltagare eller ägare på prenumerations nivå.
    - Du är konto ägare och registrerings administratören har inaktiverat inställningen "AO View charges".  Kontakta registrerings administratören för att få åtkomst till kostnader.
    - Du är avdelnings administratör och din registrerings administratör har inaktiverat inställningen för att **Visa avgift för da** .  Kontakta registrerings administratören för att få åtkomst.
    - Du har köpt Azure via en kanal partner och partnern släppte ingen pris information.  
- Om du uppdaterar inställningar som rör kostnads åtkomst i Enterprise Portal uppstår en fördröjning på några minuter innan ändringarna visas i Azure Portal.
- Vägledning för utgifts gräns och faktura gäller inte för EA-prenumerationer.

### <a name="check-your-subscription-and-access"></a>Kontrol lera din prenumeration och åtkomst

Om du vill visa kostnader måste du ha [åtkomst till fakturerings information på prenumerations nivå](billing-manage-access.md). Endast konto administratören kan komma åt [konto centret](https://account.azure.com/Subscriptions), ändra fakturerings information och hantera prenumerationer. Konto administratören är den person som gick igenom registrerings processen. Mer information finns i [Lägg till eller ändra Azure Administrator-roller som hanterar prenumerationen eller tjänsterna](billing-add-change-azure-subscription-administrator.md).

För att se om du är konto administratör, gå till [prenumerationer i Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Visa listan över prenumerationer och hitta **min roll**. Om *konto administratör* är rollen har du fullständig behörighet. Om det står något annat, t. ex. *ägare*, har du inte fullständig behörighet.

![Skärm bild av din roll i vyn prenumerationer i Azure Portal](./media/billing-getting-started/sub-blade-view.PNG)

Om du vill hantera prenumerationer och ändra fakturerings information [letar du reda på konto administratören](billing-subscription-transfer.md#whoisaa). Be konto administratören att slutföra uppgifterna eller [överför prenumerationen till dig](billing-subscription-transfer.md).

Om din konto administratör inte längre är med din organisation och du behöver hantera fakturering, kontaktar du [oss](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Begär en Serviceavtal kredit för en tjänste incident

Serviceavtalet beskriver Microsofts åtaganden för drifttid och anslutning. En tjänst incident rapporteras när Azure-tjänster upplever ett problem som påverkar drift tiden eller anslutningen, vilket ofta kallas för ett *avbrott*. Om vi inte uppnår och underhåller tjänste nivåerna för varje tjänst enligt beskrivningen i SLA, kan du vara berättigad till en kredit för en del av dina månatliga service avgifter.

Så här begär du en kredit:

1. Logga in på [Azure Portal](https://portal.azure.com/). Om du har flera konton kontrollerar du att du använder det som påverkades av Azures stillestånds tid. 
2. Skapa en ny supportbegäran.
3. Välj **fakturering**under **typ av problem**.
4. Under **problem typ**väljer du **åter betalnings förfrågan**.
5. Lägg till information för att ange att du tillfrågas om en SLA-kredit, ange datum/tid/tidszon samt de tjänster som påverkas (virtuella datorer, webbplatser osv.)
6. Verifiera din kontakt information och välj **skapa** för att skicka in din begäran.

SLA-tröskelvärden varierar beroende på tjänst. Till exempel har SQL-webbnivån ett service avtal på 99,9%, virtuella datorer har ett service avtal på 99,95% och SQL standard-nivån har ett service avtal på 99,99%.

För vissa tjänster finns det krav på att service avtalet ska gälla. Till exempel måste virtuella datorer ha två eller fler distribuerade instanser i samma tillgänglighets uppsättning.

Mer information finns i [service nivå avtal](https://azure.microsoft.com/support/legal/sla/) och i [SLA-Sammanfattning för Azure-tjänster](https://azure.microsoft.com/support/legal/sla/summary/) .

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en support förfrågan](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om att använda [utgifts gränser](billing-spending-limit.md) för att förhindra överförbrukning.
- Börja [analysera dina Azure-kostnader](../cost-management/quick-acm-cost-analysis.md).
