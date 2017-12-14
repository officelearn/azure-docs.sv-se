---
title: "Förhindra att oväntade kostnader, hantera fakturerings - Azure | Microsoft Docs"
description: "Lär dig att undvika oväntade debiteringar på fakturan Azure. Använda funktionerna för hantering och uppföljning av kostnaden för en Microsoft Azure-prenumeration."
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: tonguyen
ms.openlocfilehash: 4bbb13f31c42a57815dca176408d5c2e82feb5e0
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Förhindra oväntat avgifter med Azure fakturerings- och kostnaden management

När du registrerar dig för Azure finns det flera saker du kan göra för att få en bättre uppfattning av dina utgifter. Den [prisnivå Kalkylatorn](https://azure.microsoft.com/pricing/calculator/) kan ge en uppskattning av kostnaderna innan du skapar en Azure-resurs. Den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ger du den aktuella kostnadsuppdelning och prognos för din prenumeration. Om du vill gruppera och förstå kostnaderna för olika projekt eller team titta på [resurs taggning](../azure-resource-manager/resource-group-using-tags.md). Om din organisation har ett rapporteringssystem som du föredrar att använda, ta en titt på [fakturering API: er](billing-usage-rate-card-overview.md). 

- Om din prenumeration är en Enterprise-avtal (EA), är public preview för att visa dina kostnader i Azure portal tillgänglig. Om din prenumeration via Cloud Solution Providers (CSP) eller Azure sponsring kanske sedan några av följande funktioner inte gäller dig. Se [ytterligare resurser för EA och CSP sponsring](#other-offers) för mer information.

- Om din prenumeration är en kostnadsfri utvärderingsversion [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure i Open (AIO) eller BizSpark, din prenumeration inaktiveras automatiskt när din kredit används. Lär dig mer om [utgiftsgränser](#spending-limit) för att undvika att din prenumeration unexpectantly inaktiverad.

- Om du har registrerat dig för [kostnadsfritt Azure-konto](https://azure.microsoft.com/en-us/free/), [du kan använda några av de mest populära Azure tjänsterna kostnadsfritt för 12 månader](billing-create-free-services-included-free-account.md). Tillsammans med de rekommendationer som visas nedan, se [undvika komma debiteras kostnadsfritt konto](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Hämta uppskattade kostnaderna innan du lägger till Azure-tjänster

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Beräkna kostnaden med hjälp av prisnivå Kalkylatorn

Kolla in den [prisnivå Kalkylatorn](https://azure.microsoft.com/pricing/calculator/) att hämta en månatlig uppskattade kostnaden för tjänsten som du är intresserad av. Du kan lägga till en första part Azure-resurs för att få en uppskattning kostnad.

![Skärmbild av menyn prisnivå Kalkylatorn](./media/billing-getting-started/pricing-calc.png)

Till exempel uppskattas A1 Windows virtuell dator (VM) kostnaden $66.96 USD per månad i beräkningstimmar om du låter rutan vara igång hela tiden:

![Skärmbild av prisnivå Kalkylatorn visar att en A1 Windows VM bedöms kostnad $66.96 USD per månad](./media/billing-getting-started/pricing-calcVM.png)

Mer information om priser finns [vanliga frågor och svar](https://azure.microsoft.com/pricing/faq/). Eller om du vill kommunicera med en Azure säljare kontakta 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Granska den beräknade kostnaden i Azure-portalen

När du lägger till en tjänst i Azure-portalen, är det vanligtvis en vy som visar en liknande uppskattade kostnaden per månad. Till exempel när du väljer storleken på din Windows-VM visas månatliga uppskattade kostnaden för beräkningstimmar:

![Exempel: en A1 Windows VM bedöms kostnad $66.96 USD per månad](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="set-up-billing-alerts"></a>Ställa in faktureringsvarningar

Konfigurera fakturaaviseringar för att få e-postmeddelanden när förbrukningskostnader överskrider ett belopp som du anger. Om du har månatliga krediter ställa in aviseringar för när du använder upp en angiven mängd. Mer information finns i [konfigurera fakturering aviseringar för Microsoft Azure-prenumerationer](billing-set-up-alerts.md).

![Skärmbild av e-post för fakturering avisering](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Den här funktionen är fortfarande under förhandsgranskning så du bör regelbundet kontrollera din användning.

Du kanske vill använda kostnadsuppskattning från prisnivå Kalkylatorn som en riktlinje för din första aviseringen.

### <a name="spending-limit"></a>Kontrollera att du har en utgiftsgräns

Om du har en prenumeration som använder krediter sedan aktiveras utgiftsgränsen du som standard. Det här sättet när du lägger till alla dina krediter ditt kreditkort inte hämta debiteras. Finns det [fullständig lista över Azure erbjudanden och tillgängligheten för utgiftsgräns](https://azure.microsoft.com/support/legal/offer-details/).

Om du har nått din utgiftsgräns inaktiveras dock dina tjänster. Det innebär att dina virtuella datorer har frigjorts. Om du vill undvika avbrott i tjänsten måste du inaktivera utgiftsgränsen. Alla överförbrukning hämtar debiteras till kreditkortet på filen. 

Om du har stött på utgiftsgräns på, gå till den [prenumerationer Visa mitt konto](https://account.windowsazure.com/Subscriptions). En banderoll visas om din utgiftsgräns finns på:

![Skärmbild som visar en varning om utgifter gränsen som på mitt konto](./media/billing-getting-started/spending-limit-banner.PNG)

Klicka på listen och följ anvisningarna för att ta bort utgiftsgränsen. Om du inte ange kreditkortsinformation när du registrerade dig, måste du ange det för att ta bort utgiftsgränsen. Mer information finns i [Azure utgiftsgränsen – hur den fungerar och hur du aktiverar eller ta bort den](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Sätt att övervaka dina kostnader när du använder Azure-tjänster

### <a name="tags"></a>Lägga till taggar i dina resurser för att gruppera dina faktureringsuppgifter

Du kan använda taggar om du vill gruppera faktureringsinformation för tjänster som stöds. Till exempel om du kör flera virtuella datorer för olika team, kan du använda taggar för att kategorisera kostnader per kostnadsställe (HR, marknadsföring, ekonomi) eller miljö (produktion, Förproduktion, test). 

![Skärmbild som visar hur du konfigurerar taggar i portalen](./media/billing-getting-started/tags.PNG)

Taggar visas i olika kostnaden reporting vyer. Till exempel de som är synliga i din [kostnad analysen](#costs) direkt och [innehåller information om användning .csv](#invoice-and-usage) efter din första faktureringsperioden.

Mer information finns i [med taggar för att organisera dina Azure-resurser](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a>Regelbundet kontrollera portal för kostnadsuppdelning och bränna hastighet

När du har hämtat dina tjänster som körs regelbundet kontrollera hur mycket de kostnadshantering du. Du kan se de aktuella utgifter och bränna hastighet på Azure-portalen. 

1. Besök den [prenumerationer bladet i Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välja en prenumeration.

2. Du bör se kostnaden nedbrytning och bränna hastighet i popup-bladet. Den stöds inte för erbjudandet (en varning visas längst upp).

    ![Skärmbild av bränna hastighet och analys på detaljnivå i Azure-portalen](./media/billing-getting-started/burn-rate.PNG)

3. Klicka på **kostnad analysis** i listan till vänster för att se kostnadsuppdelning av resursen. Vänta 24 timmar efter att du lägger till en tjänst att fylla i data.

    ![Skärmbild av vyn kostnaden för analys i Azure-portalen](./media/billing-getting-started/cost-analysis.PNG)

4. Du kan filtrera efter andra egenskaper som [taggar](#tags), resursgrupp och timespan. Klicka på **tillämpa** bekräfta filter och **hämta** om du vill exportera vyn till en Comma-Separated fil (.csv).

5. Dessutom kan du klicka på en resurs för att se dagligen tillbringar historik och hur mycket resurskostnader varje dag.

    ![Skärmbild av historikvyn utgifter i Azure-portalen](./media/billing-getting-started/costhistory.PNG)

Vi rekommenderar att du kontrollerar kostnaderna visas med beräknar du såg när du har valt tjänsterna. Om kostnaderna wildly skiljer sig från beräknar dubbelkolla prisavtal (A1 vs A0 VM, till exempel) som du har valt för dina resurser. 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Överväg att aktivera kostnaden skärande funktioner som automatisk avstängning för virtuella datorer

Beroende på ditt scenario kan du konfigurera automatisk avstängning för dina virtuella datorer i Azure-portalen. Mer information finns i [automatisk avstängning för virtuella datorer med Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Skärmbild av automatisk avstängning alternativet i portalen](./media/billing-getting-started/auto-shutdown.PNG)

Automatisk avstängning är inte densamma som när du stänger av den virtuella datorn med Energialternativ. Automatisk avstängning stoppar och tar bort dina virtuella datorer om du vill stoppa extra kostnader. Mer information finns i frågor och svar om prissättning [virtuella Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) om VM-tillstånd.

För mer kostnaden skärande funktioner för utvecklings- och testmiljöer, kolla [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Aktivera och checka ut Azure Advisor-rekommendationer

[Azure Advisor](../advisor/advisor-overview.md) är en förhandsvisningsfunktion som hjälper dig att minska kostnaderna genom att identifiera resurser med låg belastning. Aktivera den i Azure-portalen:

![Skärmbild av Azure Advisor-knappen i Azure-portalen](./media/billing-getting-started/advisor-button.PNG)

Sedan kan du hämta rekommenderade åtgärder i den **kostnaden** fliken i Advisor instrumentpanelen:

![Skärmbild av Advisor kostnaden rekommendation exempel](./media/billing-getting-started/advisor-action.PNG)

Mer information finns i [kostnaden för Advisor-rekommendationer](../advisor/advisor-cost-recommendations.md).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Granska kostnader i slutet av din faktureringsperioden

Fakturan ska vara tillgänglig efter att din faktureringsperioden. Du kan också [hämta gamla fakturor och användningsfiler i detalj](billing-download-azure-invoice-daily-usage-date.md) att se till att du har korrekt debiteras. Mer information om att jämföra det dagliga arbetet med fakturan finns [förstå fakturan för Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>Fakturerings-API

Använda vår fakturering API för att genom programmering få användningsdata. Använda RateCard-API och API: et för användning tillsammans för att hämta fakturerade förbrukningen. Mer information finns i [få insikter om dina Microsoft Azure-resursförbrukning](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a>Ytterligare resurser och specialfall

### <a name="ea-csp-and-sponsorship-customers"></a>Kunder EA, CSP och sponsring
Kontakta din Kontoansvariga eller Azure partner att komma igång.

| Erbjudande | Resurser |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise-avtal (EA) | [EA portal](https://ea.azure.com/), [hjälp docs](https://ea.azure.com/helpdocs), och [Power BI-rapport](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | Tala med din leverantör |
| Sponsring av Azure | [Sponsring portal](https://www.microsoftazuresponsorships.com/) |

Om du hanterar IT för stora organisationer rekommenderar vi att läsa [Azure enterprise kodskelett](../azure-resource-manager/resource-manager-subscription-governance.md) och [enterprise IT vitboken](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (PDF hämtning endast på engelska).

#### <a name="EA"></a>Preview Enterprise-avtal kostnad vyer i Azure Portal 

Enterprise kostnaden vyer är för närvarande i förhandsversion. Konfigurationsobjekt för att Observera:
- Prenumerationen kostnaderna baseras på användning och hänsyn inte till förskottsinbetalade belopp, överskott, inkluderade kvantiteter, justeringar och skatter. De faktiska debiteringarna beräknas på nivån registrering. 
- Beloppen som visas i Azure-portalen kan fördröjas jämfört med om du värdena i Enterprise portal.  
- Om du inte ser kostnader, kan det bero på något av följande skäl:
    - Du äger konto och din registrering administratör har inaktiverat ”AO visa avgifterna” inställningen.  Kontakta administratören för att få åtkomst till kostnaderna för registrering. 
    - Du är administratör för avdelning och administratören registrering har inaktiverat ”DA visa avgifterna” inställningen.  Administratören registrering för att få åtkomst. 
    - Du har köpt Azure via en kanalpartner och partnern har inte publicerats prisinformation.  
- Inställningar som rör kostnaden åtkomst uppdateras i Enterprise portal, har en fördröjning på några minuter innan ändringarna återspeglas i Azure-portalen.
- Utgiftsgräns, berör fakturaaviseringar och faktura riktlinjer inte EA prenumerationer.

### <a name="check-your-subscription-and-access"></a>Kontrollera din prenumeration och åtkomst

Visa kostnaderna kräver [prenumerationer behörighet till faktureringsinformationen](billing-manage-access.md), men bara kontoadministratören kan komma åt den [Kontocenter](https://account.azure.com/Subscriptions), ändra faktureringsinformationen och hantera prenumerationer. Kontoadministratören är den person som har gått igenom registreringsprocessen. Mer information finns i [lägga till eller ändra Azure-administratörsroller som hanterar prenumerationen eller tjänster](billing-add-change-azure-subscription-administrator.md).

Om du är kontoadministratören, gå till den [prenumerationer bladet i Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och titta på listan över prenumerationer som du har åtkomst till. Tittar du under **min roll**. Om det står *kontoadministratören*, och du är ok. Om det står något annat som *ägare*, och du inte har fullständig behörighet.

![Skärmbild av din roll i vyn prenumerationer i Azure-portalen](./media/billing-getting-started/sub-blade-view.PNG)

Om du inte kontoadministratören och sedan någon förmodligen gav dig delvis åtkomst via [Azure Active Directory-rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md) (RBAC). Hantera prenumerationer och ändra fakturering info, [hitta kontoadministratören](billing-subscription-transfer.md#whoisaa) och be dem att utföra uppgifter eller [överföra prenumerationen till dig](billing-subscription-transfer.md).

Om din kontoadministratör är inte längre med din organisation och du behöver hantera fakturering, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 
## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
