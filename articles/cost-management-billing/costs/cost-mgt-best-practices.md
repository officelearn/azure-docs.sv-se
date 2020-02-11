---
title: Optimera din molninvestering med Azure Cost Management | Microsoft Docs
description: Den här artikeln hjälper dig att få ut det mesta möjliga av dina molninvesteringar, minska kostnaderna och utvärdera var pengarna spenderas.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 47a53136a8f299e5c8654d122d8e319be4ae29db
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "75988388"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Så här optimerar du din molninvestering med Azure Cost Management

Med Azure Cost Management får du verktyg för att planera, analysera och minska dina utgifter så att du kan maximera molninvesteringarna. Det här dokumentet innehåller ett metodiskt tillvägagångssätt för kostnadshantering och beskriver de verktyg som du kan använda när du hanterar organisationens kostnadsutmaningar. Azure gör det enkelt att bygga och distribuera molnlösningar. Det är dock viktigt att dessa lösningar optimeras så att kostnaderna för din organisation minimeras. När du följer de principer som beskrivs i det här dokumentet och använder våra verktyg kan du se till att din organisation är förberedd att nå framgång.

## <a name="methodology"></a>Metodik

Kostnadshantering är ett organisationsproblem som bör ske löpande före investeringar i molnresurser. För att kunna implementera kostnadshantering och optimera kostnaderna måste din organisation:

- Vara förberedd med rätt verktyg
- Redogöra för kostnader
- Vidta lämpliga åtgärder för att optimera utgifterna

Tre nyckelgrupper som beskrivs nedan måste vara samstämmiga i organisationen så att ni kan hantera kostnader på rätt sätt.

- **Ekonomiavdelningen** – de som ansvarar för godkännande av budgetbegäranden i organisationen baserat på prognoser av molnutgifter. De betalar motsvarande faktura och tilldelar kostnader till olika team i syfte att öka ansvarsfullheten.
- **Chefer** – affärsbeslutsfattare i en organisation som behöver förstå molnutgifter för att hitta bästa utgiftsresultatet.
- **Appteam** – utvecklare som hanterar molnresurser dagligen och utvecklar tjänster för att uppfylla organisationens behov. Dessa team behöver flexibiliteten att leverera det högsta värdet inom sina definierade budgetar.

### <a name="key-principles"></a>Nyckelprinciper

Använd de principer som beskrivs nedan för att ge organisationen en framgångsposition inom molnkostnadshantering.

#### <a name="planning"></a>Planering

Med heltäckande planering kan du skräddarsy molnanvändningen till dina specifika affärsbehov. Ställ följande frågor:

- Vilka affärsproblem löser jag?
- Vilka användningsmönster förväntar jag mig från mina resurser?

Svaren hjälper dig att välja de erbjudanden som passar dig bäst. De avgör vilken infrastruktur som bör användas och hur den ska användas för att maximera effektiviteten i Azure.

#### <a name="visibility"></a>Synlighet

Med rätt struktur kan Cost Management hjälpa dig att informera andra om de Azure-kostnader som de ansvarar för eller om de pengar som de spenderar. I Azure finns tjänster som är utformade för att ge dig insikter om *var* dina pengar spenderas. Dra nytta av dessa verktyg. De kan hjälpa dig att hitta resurser som underutnyttjas, eliminera slöseri och maximera kostnadsbesparingar.

#### <a name="accountability"></a>Ansvar

Tillskriv kostnader i organisationen så att ansvariga personer faktiskt tar ansvar för vad deras team spenderar. För att helt förstå organisationens Azure-utgifter bör du organisera resurser så att insikterna om kostnadstillskrivning maximeras. God organisering hjälper till att hantera och minska kostnaderna samt hålla personer ansvariga vad gäller effektiva utgifter i din organisation.

#### <a name="optimization"></a>Optimering

Vidta åtgärder för att minska utgifterna. Få ut det mesta möjliga baserat på resultat av planering ökad kostnadssynlighet. Det kan vara fördelaktigt att köpa och licensiera optimeringar samt ändringar av infrastrukturdistributionen som beskrivs närmare senare i det här dokumentet.

#### <a name="iteration"></a>Iteration

Alla i din organisation måste delta i livscykeln för kostnadshantering. De behöver vara engagerade kontinuerligt för att optimera kostnaderna. Var rigorös när det gäller den här iterativa processen och gör den till en viktig princip för ansvarsfull molnstyrning i organisationen.

![Diagram över nyckelprinciper som visar synlighet, ansvar och optimering](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Planera utifrån kostnader

Innan du distribuerar molnresurser bör du utvärdera följande saker:

- Det Azure-erbjudande som bäst uppfyller dina behov
- De resurser som du planerar att använda
- Hur mycket de kan komma att kosta

Azure innehåller verktyg som hjälper dig i utvärderingsprocessen. Verktygen kan ge en bra uppfattning om den investering som krävs för att möjliggöra dina arbetsbelastningar. Sedan kan du välja den bästa konfigurationen för din situation.

### <a name="azure-onboarding-options"></a>Alternativ för Azure-införande

Det första steget för att maximera upplevelsen i Cost Management är att undersöka och bestämma vilket Azure-erbjudande som passar dig bäst. Tänk igenom hur du planerar att använda Azure i framtiden. Överväg även hur du vill att din faktureringsmodell ska konfigureras. Beakta följande när du fattar beslutet:

- Hur länge planerar jag att använda Azure? Håller jag på att testa eller planerar jag att skapa en mer långsiktig infrastruktur?
- Hur vill jag betala för Azure? Bör jag förskottsbetala till ett lägre pris eller få en faktura i slutet av månaden?

Mer information om de olika alternativen finns i [Så köper du Azure Stack](https://azure.microsoft.com/pricing/purchase-options/). Flera av de vanligaste faktureringsmodellerna beskrivs nedan.

#### <a name="freehttpsazuremicrosoftcomfree"></a>[Kostnadsfri](https://azure.microsoft.com/free/)

- 12 månader med populära kostnadsfria tjänster
- 200 USD i kredit för att utforska tjänster i 30 dagar
- 25+ tjänster är alltid kostnadsfria

#### <a name="pay-as-you-gohttpsazuremicrosoftcomoffersms-azr-0003p"></a>[Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p)

- Inga minimibelopp eller åtaganden
- Konkurrenskraftiga priser
- Betala bara för det du använder
- Du kan avbryta när som helst

#### <a name="enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement"></a>[Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Alternativ för ekonomiska åtaganden i förskott
- Åtkomst till reducerad Azure-prissättning

## <a name="estimate-the-cost-of-your-solution"></a>Uppskatta kostnaden för din lösning

Innan du distribuerar en infrastruktur bör du utvärdera hur mycket din lösning kommer att kosta. Utvärderingen hjälper dig att skapa en budget för organisationens arbetsbelastning i förväg. Sedan kan du använda en budget över tid för att beräkna giltigheten för din första uppskattning. Och du kan jämföra den med den faktiska kostnaden för din distribuerade lösning.

### <a name="azure-pricing-calculator"></a>Priskalkylator för Azure

Med priskalkylatorn för Azure kan du skapa olika kombinationer av Azure-tjänster för att se en uppskattning av kostnaderna. Du kan implementera din lösning på olika sätt i Azure – var och en kan påverka dina övergripande utgifter. Genom att fundera över alla infrastrukturbehov för din molndistribution använder du verktyget på bästa sätt. Detta kan hjälpa dig att få en gedigen uppskattning av dina beräknade utgifter i Azure.

Mer information finns på sidan med [priskalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate är en tjänst som bedömer din organisations aktuella arbetsbelastningar i lokala datacenter. Du får en inblick i vad du kan behöva från en Azure-ersättningslösning. Först analyserar Migrate dina lokala datorer för att avgöra om migreringen är möjlig. Sedan rekommenderar den VM-storlekar i Azure för att maximera prestanda. Slutligen skapar den en kostnadsuppskattning för en Azure-baserad lösning.

Mer information finns på sidan om [Azure Migrate](../../site-recovery/migrate-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Analysera och hantera kostnader

Håll dig informerad om hur din organisations kostnader utvecklas över tid. Använd följande tekniker för att korrekt förstå och hantera utgifter.

### <a name="organize-and-tag-your-resources"></a>Organisera och tagga resurser

Organisera resurser utifrån kostnad. När du skapar prenumerationer och resursgrupper bör du tänka på de team som ansvarar för tillhörande kostnader. Kontrollera att din rapportering tar hänsyn till organisationen. Prenumerationer och resursgrupper tillhandahåller lämpliga kategorier för att organisera och tillskriva utgifter i organisationen. Taggar är ett bra sätt att tillskriva kostnader. Du kan använda taggar som ett filter. Och du kan använda dem för att gruppera när du analyserar data och undersöker kostnader. Kunder med Enterprise-avtal kan även skapa avdelningar och placera prenumerationer under dem. Kostnadsbaserad organisering i Azure hjälper till att hålla relevanta personer i din organisation ansvariga för att minska respektive teams utgifter.

### <a name="use-cost-analysis"></a>Använda kostnadsanalys

Med kostnadsanalys kan du analysera organisationens kostnader på djupet genom att segmentera och koncentrera dina kostnader med hjälp av standardmässiga resursegenskaper. Tänk på följande vanliga frågor som vägledning för din analys. Genom att besvara dessa frågor regelbundet håller du dig mer informerad och kan fatta mer kostnadsmedvetna beslut.

- **Uppskattade kostnader för den aktuella månaden** – hur stora är mina kostnader hittills den här månaden? Kommer jag att hålla mig under budgeten?
- **Undersök avvikelser** – utför rutinkontroller för att se till att kostnaderna är rimliga för normal användning. Hur ser trenderna ut? Finns det några avvikande värden?
- **Fakturaavstämning** – är min senaste fakturerade kostnad mer än föregående månad? Hur förändrades utgiftsvanorna månad till månad?
- **Intern återbetalning** – hur bör avgifterna analyseras för min organisation nu när jag vet hur mycket jag debiteras?

Mer information finns i [kostnadsanalys](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Exportera faktureringsdata enligt ett schema

Behöver du importera dina faktureringsdata till ett externt system, till exempel en instrumentpanel eller ett finanssystem? Konfigurera automatiserade exporter till Azure Storage och undvik manuell nedladdning av filer varje månad. Du kan sedan enkelt konfigurera automatiska integreringar med andra system för att hålla dina faktureringsdata synkroniserade.

Mer information om export av faktureringsdata finns i [Skapa och hantera exporterade data](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Skapa budgetar

När du har identifierat och analyserat dina utgiftsmönster är det viktigt att du börjar sätta gränser för dig själv och dina team. Azure-budgetar ger dig möjlighet att ange en kostnads- eller användningsbaserad budget med många tröskelvärden och larm. Se till att granska de budgetar som du skapar regelbundet för att se budgetförbrukningen och göra nödvändiga ändringar. Med Azure-budgetar kan du även ange en automatiseringsutlösare när en viss budgettröskel uppnås. Du kan till exempel konfigurera tjänsten så att den stänger av virtuella datorer. Eller så kan du flytta infrastrukturen till en annan prisnivå som svar på en budgetutlösare.

Mer information finns i [Azure-budgetar](tutorial-acm-create-budgets.md).

Mer information om budgetbaserad automatisering finns i [Budgetbaserad automatisering](../manage/cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Agera för att optimera
Använd följande metoder för att optimera utgifter.

### <a name="cut-out-waste"></a>Eliminera slöseri

När du har distribuerat din infrastruktur i Azure är det viktigt att se till att den används. Det enklaste sättet att börja spara omedelbart är att granska resurserna och ta bort alla som inte används. Därefter bör du fastställa om dina resurser används så effektivt som möjligt.

#### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor är en tjänst som bland annat identifierar virtuella datorer med lågt utnyttjande vad gäller CPU- eller nätverksanvändning. Därefter kan du välja att antingen stänga av eller ändra storlek på datorn baserat på den uppskattade kostnaden för att fortsätta köra datorerna. Advisor ger även rekommendationer för köp av reserverade instanser. Rekommendationerna baseras på dina senaste 30 dagars användning av virtuella datorer. När du agerar utifrån rekommendationer kan det hjälpa dig att minska dina utgifter.

Mer information finns på sidan om [Azure Advisor](../../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>Ändra storlek på virtuella datorer på rätt sätt

Storleken på virtuella datorer har en betydande inverkan på din totala Azure-kostnad. Det antal virtuella datorer som behövs i Azure motsvarar kanske inte det som du för närvarande har distribuerat i ett lokalt datacenter. Se till att välja rätt storlek för de arbetsbelastningar som du planerar att köra.

Mer information finns i [Azure IaaS: rätt storlek och kostnad](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/).

### <a name="use-purchase-discounts"></a>Använda inköpsrabatter

Azure har många rabatter som din organisation kan dra nytta av för att spara pengar.

#### <a name="azure-reservations"></a>Azure-reservationer

Med Azure-reservationer kan du förskottsbetala för ett till tre års virtuella datorer eller beräkningskapacitet för SQL Database. Förskottsbetalning gör att du kan få rabatt på de resurser du använder. Med Azure-reservationer kan du minska dina kostnader för virtuella datorer eller beräkning för SQL Database avsevärt – upp till 72 procent på Betala per användning-priser med ett eller tre års förskottsbetalning. Reservation ger en rabatt och påverkar inte de virtuella datorerna eller SQL-databasernas körningsstatus.

Mer information finns i [Vad är Azure-reservationer?](../reservations/save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Använd Azure Hybrid Benefit

Om du redan har Windows Server- eller SQL Server-licenser i dina lokala distributioner kan du använda Azure Hybrid Benefit-programmet för att spara i Azure. Med den Windows Server-förmånen täcker vi kostnaden för operativsystemet (på upp till två virtuella datorer). Du betalar bara för basberäkningskostnaderna. Du kan använda befintliga SQL Server licenser för att spara upp till 55 procent på vCore-baserade SQL Database-alternativ. Alternativen omfattar SQL Server för virtuella Azure-datorer och SQL Server Integration Services.

Mer information finns i [kalkylatorn för besparingar med Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Andra resurser

I Azure finns även en tjänst som gör att du kan skapa tjänster som drar nytta av överskottskapacitet i Azure till reducerade priser. Mer information finns i [Använda lågprioriterade virtuella datorer med Batch](../../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Nästa steg
- Om Cost Management är nytt för dig kan du läsa [Vad är Azure Cost Management](../cost-management-billing-overview.md) för att lära dig hur det hjälper till att övervaka och kontrollera Azure-utgifter och optimera resursanvändning.
