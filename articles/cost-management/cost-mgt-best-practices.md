---
title: Optimera din molninvestering med Azure Cost Management | Microsoft Docs
description: Den här artikeln hjälper dig att få ut maximalt av dina investeringar i molnet, minska kostnaderna och utvärdera där pengarna spenderas.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: ''
ms.openlocfilehash: 99e42f404fe103f406c4b3835f8c455bc2b91e07
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53017696"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Optimera din molninvestering med Azure Cost Management

Azure Cost Management ger dig verktyg för att planera för, analysera och minska dina utgifter för att maximera din investering i molnet. Det här dokumentet ger dig en metodisk metod för kostnadshantering och går igenom verktyg som är tillgängliga för dig när du har gått organisationens kostnaden utmaningar. Azure gör det enkelt att skapa och distribuera molnlösningar. Det är dock viktigt att dessa lösningar är optimerade för att minimera kostnaderna för din organisation. Följa principerna som beskrivs i det här dokumentet och med hjälp av våra verktyg hjälper till att kontrollera att din organisation är förberedd för att lyckas.

## <a name="methodology"></a>Metod

Kostnadshantering är det problem med organisationens och bör vara en pågående praxis som börjar innan du lägga pengar på molnresurser. För att kunna implementera kostnadshantering och optimera kostnaderna, måste din organisation:

- Vara beredd med rätt verktyg för att lyckas
- Vara ansvariga för kostnader
- Vidta lämpliga åtgärder för att optimera utgifter

Tre viktiga grupper, som beskrivs nedan, måste vara lika justerade i din organisation att se till att du har hanterar kostnader.

- **Finans** -personer som ansvarar för att godkänna budget begäranden i organisationen baserat på molnet utgifter prognoser. De betala motsvarande fakturan och tilldela olika team att driva på ansvarstagande kostnader.
- **Chefer** – Business-beslutsfattare i en organisation som behöver förstå molnet utgifter för att hitta det bästa utgifter resultat.
- **Appteamen** - tekniker hantera molnresurser på basis dagliga utveckla tjänster som uppfyller organisationens behov. Dessa grupper behöver flexibilitet att leverera ut mest värde i sina definierade budgetar.

### <a name="key-principles"></a>Viktiga principer

Använd de principer som beskrivs nedan för att placera din organisation för att lyckas i molnet kostnadshantering.

#### <a name="planning"></a>Planering

Omfattande, direkta planering kan du skräddarsy molnanvändning för dina specifika affärsbehov. Fråga dig själv:

- Vilka affärsproblem som lösa?
- Vilka användningsmönster förväntar jag från Mina resurser?

Dina svar hjälper dig att välja de erbjudanden som är rätt för dig. De bestämmer infrastrukturen som ska användas och hur de används för att maximera din Azure effektiviteten.

#### <a name="visibility"></a>Synlighet

När väl strukturerade, hjälper dig att informera personer om Azure-kostnader de är ansvarig för eller pengar de utgifter i Cost Management. Azure har tjänster som utformats för att ge dig insyn i *där* går åt till att dina pengar. Dra nytta av dessa verktyg. De kan hjälpa dig hitta resurser som inte är tillräckligt utnyttjade, ta bort spilltiden och maximera kostnadsbesparande möjligheter.

#### <a name="accountability"></a>Ansvarstagande

Attributet kostnader i din organisation för att se till att personer som ansvarar är ansvariga för sin grupps utgifter. För att helt förstå din organisations Azure utgifter, bör du organisera dina resurser för att maximera insikt i kostnad attribution. Det hjälper dig att hantera och minska kostnaderna och håller ned personer för effektiv utgifter i din organisation att hantera bra organisation.

#### <a name="optimization"></a>Optimering

Fungera för att minska dina utgifter. Få ut det mesta av den baserat på resultaten som samlats in via planerings- och ökar kostnaden synlighet. Du kan överväga att köpa och licensiering optimeringar tillsammans med distributionsändringar i infrastrukturen som beskrivs i detalj senare i det här dokumentet.

#### <a name="iteration"></a>Iteration

Alla i din organisation måste delta i livscykeln för hantering av kostnaden. De behöver för att hålla som ingår med jämna mellanrum att optimera kostnaderna. Att rigorösa om den här iterativ process och göra det en grundlärosats i ansvarig molnet styrning i din organisation.

![Viktiga principer](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Planera kostnad i åtanke

Innan du distribuerar molnresurser, utvärdera följande objekt:

- Azure-erbjudande som bäst uppfyller dina behov
- De resurser som du planerar att använda
- Hur mycket de kan kosta

Azure tillhandahåller verktyg som hjälper dig i utvärderingsprocessen för. Verktygen kan ge dig en god överblick av investeringen som krävs för att aktivera dina arbetsbelastningar. Du kan välja den bästa konfigurationen för din situation.

### <a name="azure-onboarding-options"></a>Azure-integreringsalternativ

Det första steget i att maximera din upplevelse i Cost Management är att undersöka och avgöra vilka Azure-erbjudande är bäst för dig. Tänk på hur du planerar att använda Azure i framtiden. Överväg också att du hur faktureringsmodellen konfigurerats. Tänk på följande frågor när du fattar ditt beslut:

- Hur länge planerar att använda Azure? Jag testar eller jag planerar att bygga långsiktiga infrastruktur?
- Hur vill jag betala för Azure? Bör jag Förskottsbetala för ett reducerat pris eller hämta faktureras i slutet av månaden?

Läs mer om de olika alternativen [hur du köper Azure](https://azure.microsoft.com/pricing/purchase-options/). Flera av de vanligaste faktureringsmodellerna identifieras nedan.

#### <a name="freehttpsazuremicrosoftcomfree"></a>[Kostnadsfri](https://azure.microsoft.com/free/)

- 12 månaders populära kostnadsfria tjänster
- 200 USD i kredit och utforska tjänster under 30 dagar
- Mer än 25 tjänster är alltid kostnadsfria

#### <a name="pay-as-you-gohttpsazuremicrosoftcomoffersms-azr-0003p"></a>[Betala](https://azure.microsoft.com/offers/ms-azr-0003p)

- Inga lägsta nivåer eller åtaganden
- Konkurrenskraftiga priser
- Betala bara för det du använder
- Avsluta när du vill

#### <a name="enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement"></a>[Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Alternativ för direkta monetära åtaganden
- Åtkomst till reducerade priser för Azure

## <a name="estimate-the-cost-of-your-solution"></a>Beräkna kostnaden för din lösning

Innan du distribuerar all infrastruktur måste du utvärdera hur mycket din lösning kostar. Utvärderingen kan du skapa en budget för din organisation för arbetsbelastningen startkostnader. Du kan sedan använda en budget över tid för att mäta giltigheten för din första uppskattning. Och du kan jämföra den med den faktiska kostnaden för den distribuerade lösningen.

### <a name="azure-pricing-calculator"></a>Priskalkylator för Azure

Priskalkylator för Azure kan du blanda och matcha olika kombinationer av Azure-tjänster att se en uppskattning av kostnaderna. Du kan implementera din lösning med hjälp av olika sätt i Azure – kan påverka dina totala utgifter. Tänka tidigt alla molndistributionen infrastrukturbehov kan du använda verktyget effektivt. Det kan hjälpa dig få en solid uppskattning av dina uppskattade utgifter i Azure.

Mer information finns i den [Azures priskalkylator](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate är en tjänst som utvärderar din organisations aktuella arbetsbelastningar i lokala datacenter. Det ger dig inblick i vad du behöver från en ersättning av Azure-lösning. Först analyserar migrera dina lokala datorer för att avgöra om migrering är möjligt. Den rekommenderar sedan VM-storlek i Azure för att maximera prestanda. Slutligen skapar det också en kostnadsberäkning för en Azure-baserad lösning.

Mer information finns i [Azure Migrate](../migrate/migrate-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Analysera och hantera dina kostnader

Håll dig informerad om hur organisationens kostnader utvecklas över tid. Använd följande tekniker för att förstå och hantera dina utgifter korrekt.

### <a name="organize-and-tag-your-resources"></a>Organisera och tagga dina resurser

Organisera dina resurser med kostnaden i åtanke. När du skapar prenumerationer och resursgrupper kan tänka på de team som ansvarar för kostnaderna. Kontrollera att dina rapporter ser till att din organisation i åtanke. Prenumerationer och resursgrupper ger bra buckets för att ordna och attributet utgifter i hela organisationen. Taggar är ett bra sätt att attributet kostnaden. Taggar kan användas som ett filter. Och du kan använda dem för att gruppera efter när du analysera data och undersöka kostnader. Enterprise Agreement-kunder kan också skapa avdelningar och placera prenumerationer därunder. Kostnadsbaserad organisation i Azure gör att relevanta personer i organisationen som är ansvariga för att minska sina team utgifter.

### <a name="use-cost-analysis"></a>Använda kostnadsanalys

Kostnadsanalys kan du analysera dina organisationens kostnader för djupgående med Finfördela dina kostnader med hjälp av standard resursegenskaper. Beakta följande vanliga frågor som en vägledning för din analys. Genom att besvara frågorna körs regelbundet hjälper dig att hålla mer välgrundade och aktivera mer kostnadsmedvetna beslut.

- **Beräknade kostnader för den aktuella månaden** – hur mycket har jag tillkommer hittills den här månaden? Ska jag vara kvar under Mina budget?
- **Undersöka avvikelser** – göra vanliga kontroller som säkerställer att kostnaderna stannar inom en rimlig mängd normal användning. Vilka är trenderna? Finns det några avvikare?
- **Fakturera avstämningen** -är min senaste fakturerad kostar mer än den föregående månaden? Hur ändrar dina köpvanor månad för månad?
- **Intern återbetalning** – nu när jag vet hur mycket jag debiteras, hur ska dessa tillägg kan delas upp för min organisation?

Mer information finns i [analys av kostnader](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Exportera faktureringsdata enligt ett schema

Behöver du importera din faktureringsinformation till ett externt system, t.ex. en instrumentpanel eller finansiella system? Ställ in automatiserad export till Azure Storage och undvika att manuellt överföra filer varje månad. Du kan enkelt ställa in automatisk integration med andra system att synkronisera din faktureringsinformation.

Läs mer om hur du exporterar faktureringsdata [skapa och hantera exporterade data](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Skapa budgetar

När du har identifierat och analyseras din utgiftsgräns mönster, är det viktigt att börja ange begränsningar för dig själv och dina team. Azure budgetar ger dig möjlighet att ange en kostnad eller användningsbaserad budget med många tröskelvärden och aviseringar. Se till att granska budgetar som du skapar regelbundet för att se förloppet för bränna ned din budget och göra ändringar efter behov. Azure budgetar kan du konfigurera en automation-utlösare när en viss budget-tröskelvärdet har uppnåtts. Du kan exempelvis konfigurera din tjänst att stänga av virtuella datorer. Eller du kan flytta din infrastruktur till en annan prisnivå som svar på en utlösare för budget.

Mer information finns i [Azure budgetar](tutorial-acm-create-budgets.md).

Läs mer om budget-baserade automation [Budget baserat Automation](../billing/billing-cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Agera för att optimera
Använd de följande sätten att optimera utgifter.

### <a name="cut-out-waste"></a>Klipp ut avfall

När du har distribuerat din infrastruktur i Azure, är det viktigt att se till att den används. Det enklaste sättet att börja spara omedelbart är att granska dina resurser och ta bort någon som inte används. Därifrån kan bestämma du om dina resurser används så effektivt som möjligt.

#### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor är en tjänst som bland annat identifierar virtuella datorer med låg användning baserat på en processor eller användning av. Därifrån kan välja du att antingen stänga av eller ändra storlek på den dator som är baserat på uppskattade kostnaden för att fortsätta att köra datorerna. Advisor innehåller även rekommendationer för köp av reserverad instans. Rekommendationerna baseras på de senaste 30 dagarna användning av virtuella datorer. När kördes på rekommendationer kan hjälpa dig att minska dina utgifter.

Mer information finns i [Azure Advisor](../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>Ändra storlek på dina virtuella datorer korrekt

VM-storlek har en betydande inverkan på den övergripande Azure kostnaden. Hur många virtuella datorer som behövs i Azure kan därmed inte till vad du för närvarande har distribuerats i ett lokalt datacenter. Kontrollera att din väljer rätt storlek för de arbetsbelastningar som du planerar att köra.

Mer information finns i [Azure IaaS: rätt storlek och kostnad](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/).

### <a name="use-purchase-discounts"></a>Använd köp rabatter

Azure har många rabatter som din organisation ska kunna utnyttja för att spara pengar.

#### <a name="azure-reservations"></a>Azure-reservationer

Azure reservationer kan du betala i förskott för ett år eller beräkningskapacitet för tre års av virtuell dator eller SQL-databas. Betala förväg kan du få rabatt på de resurser du använder. Azure reservationer kan avsevärt minska din virtuella datorn eller beräkningskostnaderna för SQL-databas – upp till 72% jämfört med användningsbaserad betalning med ett eller tre år i förskott. reservationer ger en rabatt på fakturering och påverkar inte körtiden för dina virtuella datorer eller en SQL-databaser.

Mer information finns i [vad är Azure reservationer?](../billing/billing-save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Använda Azure Hybrid-förmånen

Om du redan har Windows Server eller SQL Server-licenser i din lokala distributioner kan du använda Azure Hybrid-förmånen-programmet för att spara i Azure. Varje licens täcker kostnaden för Operativsystemet (upp till två virtuella datorer) med Windows Server-förmånen och du betalar bara för basberäkningskostnaderna. Du kan använda befintliga SQL Server-licenser för att spara upp till 55 procent på vCore-baserade SQL Database-alternativ. alternativ inkluderar SQL Server i Azure Virtual Machines och SQL Server Integration Services.

Mer information finns i [kalkylator för besparingar med Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Andra resurser

Azure har också en tjänst som gör det möjligt att skapa tjänster som utnyttjar överkapacitet i Azure för reducerade priser. Mer information finns i [använda lågprioriterade virtuella datorer med Batch](../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Nästa steg
- Om du inte har använt Cost Management kan du läsa [vad är Azure Cost Management?](overview-cost-mgt.md) att lära dig hur det hjälper att övervaka och kontrollera Azure utgifter och optimera Resursanvändning.
