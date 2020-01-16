---
title: Översikt över Azure Cost Management och fakturering | Microsoft Docs
description: Du använder Azure Cost Management-och fakturerings funktioner för att utföra fakturerings administrativa uppgifter och hantera fakturerings åtkomst till kostnader. Du kan även övervaka och kontrol lera Azure-utgifter och optimera användningen av Azure-resurser.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/20/2019
ms.topic: overview
ms.service: cost-management-billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: cadff1d83a8b47a540efe9b74ffaf6de171138b3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987518"
---
# <a name="what-is-azure-cost-management-and-billing"></a>Vad är Azure Cost Management och fakturering?

Med Azure-produkter och-tjänster betalar du bara för det du använder. När du skapar och använder Azure-resurser debiteras du för resurserna. Du använder Azure Cost Management-och fakturerings funktioner för att utföra fakturerings administrativa uppgifter och hantera fakturerings åtkomst till kostnader. Du har också dess funktioner för att övervaka och kontrol lera Azure-utgifter och optimera användningen av Azure-resurser.

## <a name="understand-azure-billing"></a>Förstå Azure-fakturering

Azures fakturerings funktioner används för att granska dina fakturerade kostnader och hantera åtkomst till fakturerings information. I större organisationer gör inköps-och finans team vanligt vis fakturerings uppgifter.

När du registrerar dig för att använda Azure skapas ett faktureringskonto. Du använder faktureringskontot för att hantera fakturor och betalningar samt spåra kostnader. Du kan ha åtkomst till flera faktureringskonton. Du kan till exempel ha registrerat dig för Azure för dina personliga projekt. Därför kan du ha en individuell Azure-prenumeration med ett fakturerings konto. Du kan även ha åtkomst via organisationens Enterprise-avtal eller Microsoft-kundavtal. För varje scenario har du ett separat fakturerings konto.

### <a name="billing-accounts"></a>Fakturerings konton

Azure Portal stöder för närvarande följande typer av fakturerings konton:

- **Microsoft Online Services-program**: ett enskilt fakturerings konto för ett Microsoft Online Services-program skapas när du registrerar dig för Azure via Azure-webbplatsen. Det gäller till exempel när du registrerar dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) eller ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller som [Visual studio-prenumerant](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise-avtal**: ett fakturerings konto för en Enterprise-avtal skapas när din organisation signerar ett [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) för att använda Azure.

- **Microsofts kund avtal**: ett fakturerings konto för ett Microsoft-kundavtal skapas när din organisation arbetar med en Microsoft-representant för att signera ett Microsofts kund avtal. Vissa kunder i utvalda regioner som registrerar sig via Azure-webbplatsen för ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller uppgraderar sitt [kostnadsfria Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) kan även ha ett faktureringskonto för ett Microsoft-kundavtal. Mer information finns i artikeln om att [komma igång med ditt faktureringskonto för Microsoft-kundavtal](./understand/mca-overview.md).

### <a name="scopes-for-billing-accounts"></a>Omfång för faktureringskonton
Ett omfång är en nod i ett fakturerings konto som du använder för att visa och hantera fakturering. Det är här du hanterar fakturerings data, betalningar, fakturor och genomför allmän konto hantering.

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services-programmet

|Omfång  |Definition  |
|---------|---------|
|Faktureringskonto     | Representerar en enskild ägare (kontoadministratör) för en eller flera Azure-prenumerationer. En kontoadministratör har behörighet att utföra olika faktureringsuppgifter, till exempel skapa prenumerationer, visa fakturor eller ändra faktureringen för prenumerationer.  |
|Prenumeration     |  Representerar en gruppering av Azure-resurser. En faktura skapas i prenumerations omfånget. Den har sina egna betalningsmetoder som används för att betala fakturan.|


#### <a name="enterprise-agreement"></a>Enterprise Agreement

|Omfång  |Definition  |
|---------|---------|
|Faktureringskonto    | Representerar en Enterprise-avtalsregistrering. Fakturan skapas i fakturerings konto omfånget. Den är strukturerad med avdelningar och registrerings konton.  |
|Avdelning     |  Valfri gruppering av registreringskonton.      |
|Registreringskonto     |  Representerar en enskild kontoägare. Azure-prenumerationer skapas under registrerings konto omfånget.  |


#### <a name="microsoft-customer-agreement"></a>Microsofts kundavtal

|Omfång  |Aktiviteter  |
|---------|---------|
|Faktureringskonto     |   Representerar ett kundavtal för flera Microsoft-produkter och -tjänster. Fakturerings kontot är strukturerat med hjälp av fakturerings profiler och faktura avsnitt.   |
|Faktureringsprofil     |  Representerar en faktura och dess betalningsmetod. Fakturan genereras i detta omfång. Fakturerings profilen kan ha flera faktura avsnitt.      |
|Fakturaavsnitt     |   Representerar en grupp med kostnader i en faktura. Prenumerationer och andra inköp är kopplade till faktura avsnittets omfattning.    |


## <a name="understand-azure-cost-management"></a>Förstå Azure Cost Management
Kostnadshantering är den process där du effektivt planerar och styr kostnaderna i företaget. Kostnadshanteringsuppgifter utförs normalt av ekonomi-, hanterings- och appteam. Azure Cost Management + fakturering hjälper organisationer att planera med kostnad i åtanke. Den hjälper också till att analysera kostnaderna effektivt och vidta åtgärder för att optimera moln utgifter. Om du vill veta mer om metoder för kostnadshantering för organisationer kan du läsa artikeln om [metodtips för Azure Cost Management](./costs/cost-mgt-best-practices.md).

Titta på [videon om Azure Cost Management översikt](https://www.youtube.com/watch?v=el4yN5cHsJ0) för en snabb översikt över hur Azure Cost Management kan hjälpa dig att spara pengar i Azure.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Fakturering är relaterat till kostnadshantering men inbegriper skillnader. I faktureringsprocessen ingår att fakturera kunder för varor eller tjänster och att hantera den affärsrelationen.

Kostnadshantering visar organisationens kostnader och användningsmönster med hjälp av avancerade analyser. Rapporter i Cost Management visar de användnings kostnader som förbrukas av Azure-tjänster och Marketplace-erbjudanden från tredje part. Kostnaderna baseras på förhandlade priser och faktor i reservations-och Azure Hybrid-förmån rabatter. Sammantaget visar rapporterna dina interna och externa kostnader för användning och Azure Marketplace-debiteringar. Andra debiteringar, till exempel köp av reservation, support och skatter visas ännu inte i rapporter. Rapporterna hjälper dig att förstå dina utgifter och resursanvändningen och kan hjälpa dig hitta utgiftsavvikelser. Förutsägelseanalys är också tillgängligt. Kostnadshantering använder Azure-hanteringsgrupper, budgetar och rekommendationer för att tydligt visa hur dina utgifter organiseras och hur du kan minska kostnaderna.

Du kan använda Azure-portalen eller olika API:er för exportautomatisering för att integrera kostnadsdata med externa system och processer. Automatiserad export av faktureringsdata och schemalagda rapporter är också tillgängligt.

### <a name="plan-and-control-expenses"></a>Planering och kontrollera utgifter

De sätt som Cost Management hjälpa dig att planera för och kontrol lera dina kostnader är: kostnads analys, budget, rekommendationer och export av kostnads hanterings data.

Du använder kostnadsanalys för att utforska och analysera dina organisationskostnader. Du kan visa aggregerade kostnader efter organisation för att se var kostnader ackumuleras och för att identifiera utgiftstrender. Du kan även se ackumulerade kostnader över tid för att beräkna månatliga, kvartalsvisa eller årliga kostnadstrender mot en budget.

Budgetar hjälper dig att planera och uppfylla finansiellt ansvar i din organisation. De bidrar till att förhindra att kostnadströsklar eller -gränser överskrids. Budgetar kan även hjälpa dig att informera andra om deras utgifter för att proaktivt hantera kostnader. De gör också att du kan se hur utgifterna utvecklas över tid.

Rekommendationer visar hur du kan optimera och förbättra effektiviteten genom att identifiera inaktiva och underutnyttjade resurser. Eller så kan de visa billigare resursalternativ. När du agerar utifrån rekommendationer ändrar sättet som du använder resurser för att spara pengar. För att tittar du först på rekommendationer för kostnadsoptimering för att hitta potentiell ineffektivitet i användningen. Därefter agerar du utefter en rekommendation för att ändra din Azure-resursanvändning till ett mer kostnadseffektivt alternativ. Sedan kontrollerar du åtgärden för att se till att den ändring du gör lyckas.

Om du använder externa system för att komma åt eller granska kostnadshanteringsdata kan du enkelt exportera data från Azure. Och du kan ange en daglig schemalagd export i CSV-format och lagra datafilerna i Azure Storage. Du kan sedan komma åt data från ditt externa system.

### <a name="consider-cloudyn"></a>Tjänsten Cloudyn

[Cloudyn](./cloudyn/overview.md) är en Azure-tjänst som är relaterad till Cost Management. Med Cloudyn kan du spåra molnanvändning och utgifter för dina Azure-resurser. Det stöder även andra molnleverantörer, inklusive AWS och Google. Instrumentpanelsrapporter som är lätta att förstå hjälper dig med kostnadsallokering och kostnadsrapporter/återbetalningar. För närvarande har Cost Management inte stöd för kostnadsfördelning/återbetalning eller andra molntjänstleverantörer. Cloudyn är dock ett alternativ som _har_ stöd för dem. För närvarande stöder Cost Management inte CSP-konton (Microsoft Cloud Service Provider), men Cloudyn gör det. Om du har CSP-konton eller om du vill använda showback/åter betalning kan du använda Cloudyn för att hantera dina kostnader.

Titta på [Azure Cost Management-och Cloudyn-videon](https://www.youtube.com/watch?v=PmwFWwSluh8) för att se rekommendationer när du ska använda antingen Azure Cost Management eller Cloudyn, baserat på dina affärs behov.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

### <a name="additional-azure-tools"></a>Ytterligare Azure-verktyg

Azure har andra verktyg som inte är en del av Azure Cost Management-och fakturerings funktionen. De spelar dock en viktig roll i kostnadshanteringsprocessen. Se följande länkar om du vill veta mer om dessa verktyg.

- [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) – använd det här verktyget för att beräkna dina startkostnader för moln.
- [Azure Migrate](../migrate/migrate-overview.md) – utvärdera din nuvarande datacenterarbetsbelastning för att få insikter om vad som behövs från en Azure-ersättningslösning.
- [Azure Advisor](../advisor/advisor-overview.md) – identifiera oanvända virtuella datorer och få rekommendationer om köp av reserverade Azure-instanser.
- [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) – använd dina aktuella lokala Windows Server- eller SQL Server-licenser för virtuella datorer i Azure för att spara.


## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med Cost Management och fakturering är nästa steg att börja använda tjänsten.

- Börja använda Azure Cost Management för att [analysera kostnader](./costs/quick-acm-cost-analysis.md).
- Du kan även läsa mer om [metodtips för Azure Cost Management](./costs/cost-mgt-best-practices.md).
