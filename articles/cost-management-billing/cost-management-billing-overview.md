---
title: Översikt över Azure Cost Management och Azure-fakturering | Microsoft Docs
description: Du använder funktioner i Azure Cost Management och Azure-fakturering för att utföra administrativa faktureringsuppgifter och hantera faktureringsåtkomst till kostnader. Du använder även funktionen för att övervaka och kontrollera Azure-utgifter samt optimera användningen av Azure-resurser.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: overview
ms.service: cost-management-billing
ms.custom: ''
ms.openlocfilehash: c2d039dbf2ac7cfaeaad7ad038eb669337243cce
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970123"
---
# <a name="what-is-azure-cost-management-and-billing"></a>Vad är Azure Cost Management och Azure-fakturering?

Med Azure-produkter och -tjänster betalar du bara för det du använder. När du skapar och använder Azure-resurser debiteras du för resurserna. Du använder funktioner i Azure Cost Management och Azure-fakturering för att utföra administrativa faktureringsuppgifter och hantera faktureringsåtkomst till kostnader. Du kan även använda dess funktioner för att övervaka och kontrollera Azure-utgifter samt optimera användningen av Azure-resurser.

## <a name="understand-azure-billing"></a>Förstå Azure-faktureringen

Funktionerna i Azure-fakturering används för att granska dina fakturerade kostnader och hantera åtkomst till faktureringsinformation. I större organisationer genomförs faktureringsuppgifter vanligtvis av inköps- och ekonomiteamen.

När du registrerar dig för att använda Azure skapas ett faktureringskonto. Du använder faktureringskontot för att hantera fakturor och betalningar samt spåra kostnader. Du kan ha åtkomst till flera faktureringskonton. Du kan till exempel ha registrerat dig för Azure för dina personliga projekt. Därför kan du ha en enskild Azure-prenumeration med ett faktureringskonto. Du kan även ha åtkomst via organisationens Enterprise-avtal eller Microsoft-kundavtal. För varje scenario skulle du ha två separata faktureringskonton.

### <a name="billing-accounts"></a>Faktureringskonton

Azure-portalen stöder för närvarande följande typer av faktureringskonton:

- **Microsoft Online Services-programmet**: Ett enskilt faktureringskonto för ett Microsoft Online Services-program skapas när du registrerar dig för Azure via Azure-webbplatsen. Det gäller till exempel när du registrerar dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) eller ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller som [Visual studio-prenumerant](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise-avtal**: Ett faktureringskonto för ett Enterprise-avtal skapas när din organisation tecknar ett [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) för att använda Azure.

- **Microsoft-kundavtal**: Ett faktureringskonto för ett Microsoft-kundavtal skapas när din organisation arbetar med en Microsoft-representant för att teckna ett Microsoft-kundavtal. Vissa kunder i utvalda regioner som registrerar sig via Azure-webbplatsen för ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller uppgraderar sitt [kostnadsfria Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) kan även ha ett faktureringskonto för ett Microsoft-kundavtal. Mer information finns i artikeln om att [komma igång med ditt faktureringskonto för Microsoft-kundavtal](./understand/mca-overview.md).

### <a name="scopes-for-billing-accounts"></a>Omfång för faktureringskonton
Ett omfång är en nod i ett faktureringskonto som du använder för att visa och hantera fakturering. Det är där du hanterar faktureringsdata, betalningar och fakturor samt sköter allmän kontohantering.

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services-programmet

|Omfång  |Definition  |
|---------|---------|
|Faktureringskonto     | Representerar en enskild ägare (kontoadministratör) för en eller flera Azure-prenumerationer. En kontoadministratör har behörighet att utföra olika faktureringsuppgifter, till exempel skapa prenumerationer, visa fakturor eller ändra faktureringen för prenumerationer.  |
|Prenumeration     |  Representerar en gruppering av Azure-resurser. En faktura genereras i prenumerationsomfånget. Den har sina egna betalningsmetoder som används för att betala fakturan.|


#### <a name="enterprise-agreement"></a>Enterprise-avtal

|Omfång  |Definition  |
|---------|---------|
|Faktureringskonto    | Representerar en Enterprise-avtalsregistrering. Fakturan genereras i omfång för faktureringskonto. Den struktureras med hjälp av avdelningar och registreringskonton.  |
|Avdelning     |  Valfri gruppering av registreringskonton.      |
|Registreringskonto     |  Representerar en enskild kontoägare. Azure-prenumerationer skapas under omfånget för registreringskonto.  |


#### <a name="microsoft-customer-agreement"></a>Microsoft-kundavtal

|Omfång  |Aktiviteter  |
|---------|---------|
|Faktureringskonto     |   Representerar ett kundavtal för flera Microsoft-produkter och -tjänster. Faktureringskontot struktureras med hjälp av faktureringsprofiler och fakturaavsnitt.   |
|Faktureringsprofil     |  Representerar en faktura och dess betalningsmetod. Fakturan genereras i detta omfång. Faktureringsprofilen kan ha flera fakturaavsnitt.      |
|Fakturaavsnitt     |   Representerar en grupp med kostnader i en faktura. Prenumerationer och andra inköp är associerade med omfånget för fakturaavsnitt.    |


## <a name="understand-azure-cost-management"></a>Förstå Azure Cost Management
Kostnadshantering är den process där du effektivt planerar och styr kostnaderna i företaget. Kostnadshanteringsuppgifter utförs normalt av ekonomi-, hanterings- och appteam. Azure Cost Management + fakturering hjälper organisationer att planera med kostnaderna i åtanke. Det hjälper även till att effektivt analysera kostnader och vidta åtgärder för att optimera molnutgifter. Om du vill veta mer om metoder för kostnadshantering för organisationer kan du läsa artikeln om [metodtips för Azure Cost Management](./costs/cost-mgt-best-practices.md).

Titta på [videon med en översikt av Azure Cost Management](https://www.youtube.com/watch?v=el4yN5cHsJ0) för att få en snabb översikt över hur Azure Cost Management kan hjälpa dig att spara pengar i Azure.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Fakturering är relaterat till kostnadshantering men inbegriper skillnader. I faktureringsprocessen ingår att fakturera kunder för varor eller tjänster och att hantera den affärsrelationen.

Kostnadshantering visar organisationens kostnader och användningsmönster med hjälp av avancerade analyser. Rapporter i Cost Management visar de användningskostnader som förbrukas av Azure-tjänster och Marketplace-erbjudanden från tredje part. Kostnaderna baseras på förhandlade priser och beaktar reservations- och Azure Hybrid-förmånsrabatter. Sammantaget visar rapporterna dina interna och externa kostnader för användning och Azure Marketplace-debiteringar. Andra debiteringar, till exempel köp av reservation, support och skatter visas ännu inte i rapporter. Rapporterna hjälper dig att förstå dina utgifter och resursanvändningen och kan hjälpa dig hitta utgiftsavvikelser. Förutsägelseanalys är också tillgängligt. Kostnadshantering använder Azure-hanteringsgrupper, budgetar och rekommendationer för att tydligt visa hur dina utgifter organiseras och hur du kan minska kostnaderna.

Du kan använda Azure-portalen eller olika API:er för exportautomatisering för att integrera kostnadsdata med externa system och processer. Automatiserad export av faktureringsdata och schemalagda rapporter är också tillgängligt.

### <a name="plan-and-control-expenses"></a>Planering och kontrollera utgifter

Med Cost Management kan du planera för och styra dina kostnader på följande sätt: Kostnadsanalys, budgetar, rekommendationer och export av kostnadshanteringsdata.

Du använder kostnadsanalys för att utforska och analysera dina organisationskostnader. Du kan visa aggregerade kostnader efter organisation för att se var kostnader ackumuleras och för att identifiera utgiftstrender. Du kan även se ackumulerade kostnader över tid för att beräkna månatliga, kvartalsvisa eller årliga kostnadstrender mot en budget.

Budgetar hjälper dig att planera och uppfylla finansiellt ansvar i din organisation. De bidrar till att förhindra att kostnadströsklar eller -gränser överskrids. Budgetar kan även hjälpa dig att informera andra om deras utgifter för att proaktivt hantera kostnader. De gör också att du kan se hur utgifterna utvecklas över tid.

Rekommendationer visar hur du kan optimera och förbättra effektiviteten genom att identifiera inaktiva och underutnyttjade resurser. Eller så kan de visa billigare resursalternativ. När du agerar utifrån rekommendationer ändrar sättet som du använder resurser för att spara pengar. För att tittar du först på rekommendationer för kostnadsoptimering för att hitta potentiell ineffektivitet i användningen. Därefter agerar du utefter en rekommendation för att ändra din Azure-resursanvändning till ett mer kostnadseffektivt alternativ. Sedan kontrollerar du åtgärden för att se till att den ändring du gör lyckas.

Om du använder externa system för att komma åt eller granska kostnadshanteringsdata kan du enkelt exportera data från Azure. Och du kan ange en daglig schemalagd export i CSV-format och lagra datafilerna i Azure Storage. Du kan sedan komma åt data från ditt externa system.

### <a name="consider-cloudyn"></a>Tjänsten Cloudyn

[Cloudyn](./cloudyn/overview.md) är en Azure-tjänst som är relaterad till Cost Management. Med Cloudyn kan du spåra molnanvändning och utgifter för dina Azure-resurser. Det stöder även andra molnleverantörer, inklusive AWS och Google. Instrumentpanelsrapporter som är lätta att förstå hjälper dig med kostnadsallokering och kostnadsrapporter/återbetalningar. För närvarande har Cost Management inte stöd för kostnadsfördelning/återbetalning eller andra molntjänstleverantörer. Cloudyn är dock ett alternativ som _har_ stöd för dem. För närvarande stöder Cost Management inte Microsoft CSP-konton (Cloud Service Provider, molntjänstleverantör), men det gör Cloudyn. Om du har CSP-konton eller om du vill använda showback/återbetalning kan du använda Cloudyn som hjälp att hantera dina kostnader.

Titta på [videon om Azure Cost Management och Cloudyn](https://www.youtube.com/watch?v=PmwFWwSluh8) för att se rekommendationer om när du ska använda antingen Azure Cost Management eller Cloudyn, baserat på dina affärsbehov.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

### <a name="additional-azure-tools"></a>Ytterligare Azure-verktyg

Azure har andra verktyg som inte är en del av funktionsuppsättningen i Azure Cost Management och Azure-fakturering. De spelar dock en viktig roll i kostnadshanteringsprocessen. Se följande länkar om du vill veta mer om dessa verktyg.

- [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) – använd det här verktyget för att beräkna dina startkostnader för moln.
- [Azure Migrate](../migrate/migrate-overview.md) – utvärdera din nuvarande datacenterarbetsbelastning för att få insikter om vad som behövs från en Azure-ersättningslösning.
- [Azure Advisor](../advisor/advisor-overview.md) – identifiera oanvända virtuella datorer och få rekommendationer om köp av reserverade Azure-instanser.
- [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) – använd dina aktuella lokala Windows Server- eller SQL Server-licenser för virtuella datorer i Azure för att spara.


## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med Cost Management och Azure-fakturering är nästa steg att börja använda tjänsten.

- Börja använda Azure Cost Management för att [analysera kostnader](./costs/quick-acm-cost-analysis.md).
- Du kan även läsa mer om [metodtips för Azure Cost Management](./costs/cost-mgt-best-practices.md).
