---
title: Undvika oväntade kostnader, hantera fakturering i Azure | Microsoft Docs
description: Lär dig att undvika oväntade kostnader på Azure-fakturan. Använda cost spårnings- och funktioner för en Microsoft Azure-prenumeration.
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: banders
ms.openlocfilehash: d215e2ba5d650b532b9d7554ccca9ad5537cf4de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60919446"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Förhindra oväntade avgifter med Azure-fakturering och kostnadshantering

När du registrerar dig för Azure, finns det flera saker du kan göra för att få en bättre uppfattning av dina utgifter. Den [priskalkylator](https://azure.microsoft.com/pricing/calculator/) kan ge en uppskattning av kostnaderna innan du skapar en Azure-resurs. Den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ger dig den aktuella kostnadsdata och prognos för din prenumeration. Om du vill gruppera och uppskatta kostnaden för olika projekt eller team kan du titta på [resurstaggning](../azure-resource-manager/resource-group-using-tags.md). Om din organisation har ett rapporteringssystem som du föredrar att använda kan du kolla den [billing API: er](billing-usage-rate-card-overview.md).

- Om din prenumeration är ett Enterprise Agreement (EA), är den offentliga förhandsversionen för att se dina kostnader i Azure portal tillgänglig. Om din prenumeration via Cloud Solution Provider (CSP) eller Azure-sponsring, kanske några av de följande funktionerna inte gäller för dig. Se [ytterligare resurser för EA, CSP och sponsring](#other-offers) för mer information.

- Om din prenumeration är en kostnadsfri utvärderingsversion, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure i Open (AIO) eller BizSpark, din prenumeration inaktiveras automatiskt när alla krediten är slut. Lär dig mer om [utgiftsgränser](#spending-limit) för att förhindra att din prenumeration inaktiveras oväntat.

- Om du har registrerat dig för [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/), [du kan använda några av de mest populära Azure-tjänsterna kostnadsfritt i 12 månader](billing-create-free-services-included-free-account.md). Tillsammans med de rekommendationer som visas nedan, se [undvika att debiteras kostnadsfritt konto](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Få uppskattade kostnader innan du lägger till Azure-tjänster

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Beräkna kostnader online med hjälp av priskalkylatorn

Kolla in den [priskalkylator](https://azure.microsoft.com/pricing/calculator/) att hämta en beräknade månadskostnaden för tjänsten som du är intresserad av. Du kan lägga till en första part Azure-resurs för att få en uppskattning kostnad.

![Skärmbild av menyn prisnivå Kalkylatorn](./media/billing-getting-started/pricing-calc.png)

A1 Windows Virtual Machine (VM) körs exempelvis beräknas kostnaden 66.96 USD/månad i beräkningstimmar om du låter den köra hela tiden:

![Skärmbild av priskalkylatorn som visar att en A1 Windows VM bedöms kostnad 66.96 USD per månad](./media/billing-getting-started/pricing-calcVM.png)

Mer information om priser finns i den här [vanliga frågor och svar](https://azure.microsoft.com/pricing/faq/). Eller om du vill prata med en Azure säljare kontakta 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Granska den uppskattade kostnaden i Azure portal

Vanligtvis när du lägger till en tjänst i Azure portal finns en vy som visar en liknande uppskattad kostnad per månad. Till exempel när du väljer storleken på din virtuella Windows-dator kan se du den uppskattade månadskostnaden för beräkningstimmar:

![Exempel: en A1 Windows VM bedöms kostnad 66.96 USD per månad](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="spending-limit"></a> Kontrollera om du har en utgiftsgräns

Om du har en prenumeration som använder krediter är sedan utgiftsgränsen aktiverad för du som standard. Det här sättet när du lägger din kredit ditt kreditkort inte betala för det. Se den [fullständig lista över Azure-erbjudanden och tillgänglighet för utgiftsgränsen](https://azure.microsoft.com/support/legal/offer-details/).

Men om du når utgiftsgränsen inaktiveras dina tjänster. Det innebär att de virtuella datorerna frigörs. För att undvika driftstopp för tjänsten, måste du inaktivera utgiftsgränsen. Eventuell överanvändning hämtar debiteras till kreditkortet på filen. 

Om du vill se om du har har utgiftsgräns på, går du till den [prenumerationer visa i Kontocenter](https://account.windowsazure.com/Subscriptions). En banderoll visas om din utgiftsgräns är på:

![Skärmbild som visar en varning om utgifter gränsen som på i Kontocenter](./media/billing-getting-started/spending-limit-banner.PNG)

Klicka på banderollen och följ anvisningarna för att ta bort utgiftsgränsen. Om du inte har angett kreditkortsinformation när du registrerade dig, måste du ange den för att ta bort utgiftsgränsen. Mer information finns i [Azure-utgiftsgräns – hur det fungerar och hur du aktivera eller ta bort den](https://azure.microsoft.com/pricing/spending-limits/).

Du kan använda den [Cloudyn](https://www.cloudyn.com/) -tjänsten för att skapa aviseringar som automatiskt meddela intressenter utgifter avvikelser och höga risker. Du kan skapa aviseringar med hjälp av rapporter som har stöd för aviseringar baserat på tröskelvärden för budgetar och kostnader. Läs mer om hur du använder Cloudyn [självstudien: Granska användning och kostnader](../cost-management/tutorial-review-usage.md).

I det här exemplet används rapporten **Actual Cost Over Time** (Faktisk kostnad över tid) till att skicka ett meddelande när dina Azure VM-utgifter närmar sig din totala budget. I det här scenariot har du en total budget på 20 000 dollar, och du vill få ett meddelande när kostnaderna närmar sig hälften av budgeten – 9 000 dollar – och ytterligare ett meddelande när kostnaderna når 10 000 dollar.

1. På menyn längst upp i Cloudyn-portalen väljer du **Kostnader** > **Kostnadsanalys** > **Faktisk kostnad över tid**. 
2. Ställ in **Groups** (Grupper) på **Service** och sätt **Filter on the service** (Filtrera efter tjänsten) till **Azure/VM**. 
3. Längst upp till höger i rapporten väljer du **Åtgärder** och sedan **Schemalägg rapport**.
4. Om du vill skicka e-post med rapporten till dig själv vid schemalagt intervall väljer du fliken **Schemaläggning** i rapportdialogrutan **Spara eller schemalägg den här**. Välj **Skicka via e-post**. Eventuella taggar samt den gruppering och filtrering du använde ingår i den rapport som du får via e-post. 
5. Välj fliken **Tröskel** och sedan **Actual Cost vs. Threshold** (Faktisk kostnad jämfört med tröskelvärde). 
   1. I tröskelrutan **Röd avisering** anger du 10000. 
   2. I tröskelrutan **Gul avisering** anger du 9000. 
   3. I rutan **Antal på varandra följande aviseringar** anger du det antal på varandra följande aviseringar som ska tas emot. När du har fått det antal aviseringar som du angett, skickas inga fler aviseringar. 
6. Välj **Spara**.

    ![Exemplet visar röda och gula aviseringar baserat på tröskelvärden för utgifter](./media/billing-getting-started/schedule-alert01.png)

Du kan även välja tröskelvärdesmåttet **Cost Percentage vs. Budget** (Kostnadernas procentandel av budgeten) till att skapa aviseringar. På så sätt kan du ange tröskelvärden som procentandelar av budgeten i stället för valutavärden.

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Sätt att övervaka dina kostnader när du använder Azure-tjänster

### <a name="tags"></a> Lägga till taggar till dina resurser för att gruppera dina faktureringsdata

Du kan använda taggar för gruppen faktureringsinformation för tjänster som stöds. Till exempel om du kör flera virtuella datorer för olika team, kan du använda taggar för att kategorisera kostnader efter kostnadsställe (HR, marknadsföring, ekonomi) eller miljö (produktion, före produktion, test). 

![Skärmbild som visar hur du konfigurerar taggar i portalen](./media/billing-getting-started/tags.PNG)

Taggarna som visas i olika kostnaden reporting vyer. Till exempel de är synliga i din [kostnad analysvy](#costs) omedelbart och detaljerat .csv visas efter din första faktureringsperiod.

Mer information finns i [med taggar för att organisera Azure-resurser](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Regelbundet kontrollera portal för kostnadsdata och bränna hastighet

När du får dina tjänster kan regelbundet kontrollerar du de hur mycket kostar du. Du kan se den aktuella kostnader och bränna hastighet i Azure-portalen.

1. Gå till den [prenumerationer i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välja en prenumeration.

2. Om det finns stöd för din prenumeration, se kostnadsdata och bränna hastighet.

    ![Skärmbild av bränna hastighet och analys på detaljnivå i Azure portal](./media/billing-getting-started/burn-rate.PNG)

3. Klicka på **analys av kostnader** i listan till vänster för att se kostnadsdata av resurs. Vänta 24 timmar när du lägger till en tjänst att fylla i data.

    ![Skärmbild av cost analysvyn i Azure-portalen](./media/billing-getting-started/cost-analysis.PNG)

4. Du kan filtrera efter olika egenskaper som [taggar](#tags), resurstyp, resursgrupp och tidsintervall. Klicka på **tillämpa** att bekräfta filtren och **hämta** om du vill exportera vyn till en fil med Comma-Separated värden (.csv).

5. Dessutom kan du klicka på en resurs för att se din dagliga spendera historik och hur mycket kostar resursen varje dag.

    ![Skärmbild av historikvyn kostnader i Azure-portalen](./media/billing-getting-started/costhistory.PNG)

Vi rekommenderar att du kontrollerar kostnaderna som visas med beräkningar som du fick när du har valt tjänsterna. Kostnaderna är kundefterfrågan samma beräkningar, dubbelkolla prisplanen som du har valt för dina resurser.

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Överväg att aktivera kostnaden övergripande funktioner som automatisk avstängning för virtuella datorer

Beroende på ditt scenario kan du konfigurera automatisk avstängning för dina virtuella datorer i Azure-portalen. Mer information finns i [automatisk avstängning för virtuella datorer med Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Skärmbild med alternativ för automatisk avstängning i portalen](./media/billing-getting-started/auto-shutdown.PNG)

Automatisk avstängning är inte samma som när du stänger av den virtuella datorn med Energialternativ. Automatisk avstängning stoppar och frigör dina virtuella datorer om du vill stoppa extra kostnader. Mer information finns i priser vanliga frågor och svar för [virtuella Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) om VM-tillstånd.

För fler kostnaden övergripande funktioner för dina utvecklings- och testmiljöer, Kolla in [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Aktivera och kolla in Azure Advisor-rekommendationer

[Azure Advisor](../advisor/advisor-overview.md) är en funktion som hjälper dig att minska kostnaderna genom att identifiera resurser med låg belastning. Besök Advisor i Azure portal:

![Skärmbild av Azure Advisor-knappen i Azure-portalen](./media/billing-getting-started/advisor-button.PNG)

Sedan kan du få användbara rekommendationer i den **kostnaden** flik i Advisor-instrumentpanelen:

![Skärmbild av Advisor kostnaden rekommendation exempel](./media/billing-getting-started/advisor-action.PNG)

Mer information finns i [Advisor kostnadsrekommendationer](../advisor/advisor-cost-recommendations.md).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Granska kostnader i slutet av faktureringsperioden

Efter slutet av faktureringsperioden blir fakturan tillgänglig. Du kan också [ladda ned tidigare fakturor och detaljerat användningsfiler](billing-download-azure-invoice-daily-usage-date.md) att kontrollera att du debiterades korrekt. Läs mer om att jämföra det dagliga arbetet med fakturan [förstå fakturan för Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>Fakturerings-API

Använda vårt API för fakturering för att programmässigt hämta användningsdata. Använd RateCard-API och API för användning tillsammans för att få din Debiterad användning. Mer information finns i [insyn i din Microsoft Azure-resursförbrukning](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Ytterligare resurser och specialfall

### <a name="ea-csp-and-sponsorship-customers"></a>EA, CSP och sponsring kunder
Tala med din kontoansvarige eller Azure-partner för att komma igång.

| Erbjudande | Resurser |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise-avtal (EA) | [EA-portalen](https://ea.azure.com/), [hjälpa docs](https://ea.azure.com/helpdocs), och [Power BI-rapport](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | Tala med din provider |
| Sponsring av Azure | [Sponsring portal](https://www.microsoftazuresponsorships.com/) |

Om du hanterar IT för en stor organisation kan vi rekommenderar att du läser [Azure enterprise kodskelett](/azure/architecture/cloud-adoption-guide/subscription-governance) och [enterprise IT white paper om](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (PDF-nedladdning, endast på engelska).

#### <a name="EA"></a> Förhandsversion av Enterprise-avtal kostnad vyer i Azure-portalen 

Enterprise kostnaden vyer är för närvarande i offentlig förhandsversion. Objekt att Observera:

- Prenumeration kostnaderna baseras på användning och omfattar inte förbetalda belopp, överdrag, inkluderade antalet, justeringar och skatter. Faktiska kostnader beräknas på registreringsnivå.
- Belopp som visas i Azure-portalen kan skilja sig från vad som finns i Enterprise portal. Uppdateringar i Enterprise portal kan ta några minuter innan ändringarna visas i Azure-portalen.
- Om du inte ser kostnader, kan det vara för en av följande orsaker:
    - Du har inte behörigheter på prenumerationsnivån. Om du vill visa företaget kostnaden vyer, måste du vara Billing Reader, läsare, deltagare eller ägare på prenumerationsnivån.
    - Du är en kontoinnehavare och din registrering-administratör har inaktiverat de ”AO visa debiteringar” ställa in.  Kontakta din administratör för registreringen för att få åtkomst till kostnader. 
    - Du är administratör avdelning och din registrering-administratör har inaktiverat de ”DA visa debiteringar” ställa in.  Kontakta din administratör för registreringen för att få åtkomst.
    - Du har köpt Azure via en kanalpartner och partnern släppa inte prisinformation.  
- Om du uppdaterar inställningar som rör kostnad åtkomst i Enterprise portal, finns det en fördröjning på några minuter innan ändringarna visas i Azure-portalen.
- Utgiftsgränsen och faktura riktlinjer gäller inte för EA-prenumerationer.

### <a name="check-your-subscription-and-access"></a>Kontrollera din prenumeration och åtkomst

Om du vill visa kostnader, måste du ha [prenumerationer på servernivå åtkomst till faktureringsinformation](billing-manage-access.md). Endast Kontadministratören kan komma åt den [Kontocenter](https://account.azure.com/Subscriptions)ändra faktureringsinformation och hantera prenumerationer. Kontoadministratören är den person som har gått igenom registreringsprocessen. Mer information finns i [Lägg till eller ändra Azure-administratörsroller som hanterar prenumerationen eller tjänsterna](billing-add-change-azure-subscription-administrator.md).

Om du vill se om du är kontoadministratören, gå till [prenumerationer i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Titta på listan över prenumerationer som du har åtkomst till. Titta under **min roll**. Om status är Stopped *kontoadministratören*, sedan är du ok. Om det är något annat som *ägare*, och du inte har fullständig behörighet.

![Skärmbild av din roll i vyn prenumerationer i Azure portal](./media/billing-getting-started/sub-blade-view.PNG)

Om du inte är kontoadministratören och sedan någon förmodligen gav dig delvis åtkomst med hjälp av [Azure Active Directory rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md) (RBAC). Hantera prenumerationer och fakturering info, ändra [hitta kontoadministratören](billing-subscription-transfer.md#whoisaa). Be kontoadministratören för att utföra uppgifter eller [överföra prenumerationen till dig](billing-subscription-transfer.md).

Om din kontoadministratör inte längre med din organisation och du behöver hantera fakturering, [Kontakta oss](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="how-to-request-a-service-level-agreement-credit-for-a-service-incident"></a>Hur du begär en kredit på serviceavtal för en incident i tjänsten

Serviceavtalet beskriver Microsofts åtaganden för drifttid och anslutning. En incident i tjänsten rapporteras när Azure-tjänster har ett problem som påverkar drifttid eller anslutning kallas ofta ”driftstörningar”. Om vi inte uppnå och bibehålla tjänsten för varje tjänst enligt beskrivningen i serviceavtalet, sedan vara du berättigad kredit för en del av din månatliga avgifter.

Att begära en kredit:

1. Logga in på [Azure Portal](https://portal.azure.com/). Om du har flera konton, se till att du använder något som påverkades av Azure driftstopp. Detta hjälper att automatiskt samla in nödvändig information samt lösa fallet snabbare.
2. Skapa en ny supportbegäran.
3. Under **typ av problem**väljer **fakturering**.
4. Under **problemtyp**väljer **återbetala begära**.
5. Lägg till information om du vill ange att begär du för en SLA-kredit nämner den datum/tid/tidszonen, samt de berörda tjänsterna (virtuella datorer, webbplatser, osv.)
6. Verifiera din kontaktinformation och välj den **skapa** knappen Skicka din begäran.

SLA-tröskelvärdena varierar beroende på tjänsten. Exempelvis Webbnivå för SQL har ett serviceavtal på 99,9%, virtuella datorer har ett serviceavtal på 99,95% och SQL Standard-nivån har ett SLA med 99,99%.

Det finns kraven för serviceavtalet ska gälla för vissa tjänster. Virtuella datorer måste till exempel har två eller fler distribuerade instanser i samma Tillgänglighetsuppsättning.

Mer information finns i den [serviceavtal](https://azure.microsoft.com/en-us/support/legal/sla/) dokumentation och [sammanfattning av serviceavtal för Azure-tjänster](https://azure.microsoft.com/en-us/support/legal/sla/summary/) dokumentation.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
