---
title: Komma igång guide för Azure IT-operatörer | Microsoft-dokument
description: Komma igång-guide för Azure IT-operatörer
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 4f9da6cbfe8d1e6b92c39148b275de193730c8f1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77623567"
---
# <a name="get-started-for-azure-it-operators"></a>Komma igång för Azure IT-operatörer

Den här guiden introducerar grundläggande begrepp som är relaterade till distribution och hantering av en Microsoft Azure-infrastruktur. Om du inte har något nytt för molnbaserad databehandling eller Azure självt hjälper den här guiden dig att snabbt komma igång med begrepp, distribution och hanteringsinformation. I många avsnitt i den här guiden beskrivs en åtgärd som att distribuera en virtuell dator och sedan tillhandahålla en länk för detaljerad teknisk information.

## <a name="cloud-computing-overview"></a>Översikt över molntjänster

Cloud computing är ett modernt alternativ till det traditionella lokala datacentret. Offentliga molnleverantörer tillhandahåller och hanterar all datorinfrastruktur och underliggande hanteringsprogramvara. Dessa leverantörer tillhandahåller ett brett utbud av molntjänster. En molntjänst i det här fallet kan vara en virtuell dator, en webbserver eller molnbaserad databasmotor. Som molnleverantörskund leasar du dessa molntjänster efter behov. På så sätt konverterar du kapitalkostnaden för maskinvaruunderhåll till en driftskostnad. En molntjänst ger också dessa fördelar:

- Snabb driftsättning av stora beräkningsmiljöer

- Snabb deallocation av system som inte längre behövs

- Enkel användning av traditionellt komplexa system som belastningsutjämnare

- Möjlighet att tillhandahålla flexibel beräkningskapacitet eller skala vid behov

- Mer kostnadseffektiva datormiljöer

- Åtkomst var som helst med en webbaserad portal eller programmatisk automatisering

- Molnbaserade tjänster för att uppfylla de flesta beräknings- och programbehov

Med lokal infrastruktur har du fullständig kontroll över den maskinvara och programvara som distribueras. Historiskt sett har detta lett till beslut om hårdvaruupphandling som fokuserar på att skala upp. Ett exempel är att köpa en server med fler kärnor för att uppfylla toppprestandabehov. Tyvärr kan denna infrastruktur vara underutnyttjad utanför ett efterfrågefönster. Med Azure kan du distribuera endast den infrastruktur som du behöver och justera den upp eller ned när som helst. Detta leder till fokus på att skala ut genom distributionen av ytterligare beräkningsnoder för att uppfylla ett prestandabehov. Att skala ut molntjänster är mer kostnadseffektivt än att skala upp genom dyr maskinvara.

Microsoft har distribuerat många Azure-datacenter runt om i världen, med mer planerade. Dessutom ökar Microsoft suveräna moln i regioner som Kina och Tyskland. Endast de största globala företagen kan distribuera datacenter på det här sättet, så med hjälp av Azure blir det enkelt för företag av alla storlekar att distribuera sina tjänster nära sina kunder.

För små företag möjliggör Azure en billig startpunkt, med möjlighet att skala snabbt när efterfrågan på beräkning ökar. Detta förhindrar stora kapitalinvesteringar i infrastruktur, och det ger flexibiliteten till arkitekt- och omarkitektsystem efter behov. Användningen av cloud computing passar bra med skala snabbt och misslyckas snabbt modell för start tillväxt.

Mer information om tillgängliga Azure-regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Cloud computing modell

Azure använder en cloud computing-modell baserat på kategorier av tjänster som tillhandahålls kunderna. De tre kategorierna av tjänster inkluderar Infrastruktur som en tjänst (IaaS), Plattform som en tjänst (PaaS) och Software as a Service (SaaS). Leverantörer delar en del av eller hela ansvaret för komponenter i datorstacken i var och en av dessa kategorier. Låt oss ta en titt på var och en av kategorierna för cloud computing.
![Cloud Computing Stack Jämförelse](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastruktur som en tjänst

En IaaS-molnleverantör kör och hanterar alla fysiska beräkningsresurser och den programvara som krävs för att möjliggöra datorvirtualisering. En kund till den här tjänsten distribuerar virtuella datorer i dessa värdbaserade datacenter. Även om de virtuella datorerna finns i ett datacenter utanför platsen har IaaS-konsumenten kontroll över konfigurationen och hanteringen av operativsystemet som lämnar den underliggande infrastrukturen till molnleverantören.

Azure innehåller flera IaaS-lösningar, inklusive virtuella datorer, skaluppsättningar för virtuella datorer och tillhörande nätverksinfrastruktur. Virtuella datorer är ett populärt val för att först migrera tjänster till Azure eftersom det möjliggör en "lyft och flytta" migreringsmodell. Du kan konfigurera en virtuell dator som den infrastruktur som för närvarande kör dina tjänster i ditt datacenter och sedan migrera programvaran till den nya virtuella datorn. Du kan behöva göra konfigurationsuppdateringar, till exempel webbadresser till andra tjänster eller lagring, men du kan migrera många program på det här sättet.

Skalningsuppsättningar för virtuella datorer är byggda ovanpå virtuella Azure-datorer och är ett enkelt sätt att distribuera kluster med identiska virtuella datorer. Skala uppsättningar för virtuella datorer stöder också automatisk skalning så att nya virtuella datorer kan distribueras automatiskt när det behövs. Detta gör att skala virtuella datorer är en idealisk plattform för att vara värd för beräkningskluster på mikrotjänst på högre nivå, till exempel Azure Service Fabric och Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: Plattform som en tjänst

Med PaaS distribuerar du ditt program till en miljö som molntjänstleverantören tillhandahåller. Leverantören gör all infrastrukturhantering så att du kan fokusera på programutveckling och datahantering.

Azure tillhandahåller flera PaaS-beräkningserbjudanden, inklusive webbappfunktionen i Azure App Service och Azure Cloud Services (webb- och arbetsroller). I båda fallen har utvecklare flera sätt att distribuera sina program utan att veta något om muttrar och bultar som stöder det. Utvecklare behöver inte skapa virtuella datorer , använda Remote Desktop Protocol (RDP) för att logga in på var och en, eller installera programmet. De trycker bara på en knapp (eller nära den), och de verktyg som tillhandahålls av Microsoft etablera de virtuella datorerna och sedan distribuera och installera programmet på dem.

#### <a name="saas-software-as-a-service"></a>SaaS: Programvara som en tjänst

SaaS är programvara som är centralt värd och hanteras. Den är vanligtvis baserad på en arkitektur med flera trogna – en enda version av programmet används för alla kunder. Det kan skalas ut till flera instanser för att säkerställa bästa prestanda på alla platser. SaaS-programvara licensieras vanligtvis genom en månads- eller årsprenumeration. SaaS programvaruleverantörer är ansvariga för alla komponenter i programvarustacken så allt du hanterar är de tjänster som tillhandahålls.

Microsoft Office 365 är ett bra exempel på ett SaaS-erbjudande. Prenumeranter betalar en månads- eller årsprenumerationsavgift och de får Microsoft Exchange, Microsoft OneDrive och resten av Microsoft Office-paketet som en tjänst. Prenumeranter får alltid den senaste versionen och Exchange-servern hanteras åt dig. Jämfört med att installera och uppgradera Office varje år är detta billigare och kräver mindre ansträngning.

## <a name="azure-services"></a>Azure-tjänster

Azure erbjuder många tjänster i sin cloud computing-plattform. Dessa tjänster omfattar följande.

### <a name="compute-services"></a>Beräkningstjänster

Tjänster för värd- och programarbetsbelastning:

- Virtuella Azure-datorer – både Linux och Windows

- AppTjänster (webbappar, mobilappar, logikappar, API-appar och funktionsappar)

- Azure Batch (för storskaliga parallell- och batchberäkningsjobb)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Datatjänster

Tjänster för lagring och hantering av data:

- Azure Storage (består av Azure Blob, Queue, Table och File services)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache for Redis

### <a name="application-services"></a>Applikationstjänster

Tjänster för att bygga och driva applikationer:

- Azure Active Directory (Azure AD)

- Azure Service Bus för anslutning av distribuerade system

- Azure HDInsight för bearbetning av stordata

- Azure Logic Apps för integrations- och orkestreringsarbetsflöden

- Azure Media Services

### <a name="network-services"></a>Nätverkstjänster

Tjänster för nätverk både inom Azure och mellan Azure och lokala datacenter:

- Azure Virtual Network

- Azure ExpressRoute

- Azure-förutsatt DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Detaljerad dokumentation om Azure-tjänster finns i [Azure-tjänstdokumentation](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Azure-nyckelbegrepp

### <a name="datacenters-and-regions"></a>Datacenter och regioner

Azure är en global molnplattform som är allmänt tillgänglig i många regioner runt om i världen. När du etablerar en tjänst, ett program eller en virtuell dator i Azure uppmanas du att välja en region. Den valda regionen representerar ett specifikt datacenter där ditt program körs. Mer information finns i [Azure-regioner](https://azure.microsoft.com/regions/).

En av fördelarna med att använda Azure är att du kan distribuera dina program till olika datacenter runt om i världen. Den region du väljer kan påverka programmets prestanda. Det är optimalt att välja en region som ligger närmare de flesta av dina kunder för att minska svarstiden i nätverksbegäranden. Du kan också välja en region för att uppfylla de juridiska kraven för att distribuera din app i vissa länder/regioner.

### <a name="azure-portal"></a>Azure Portal

Azure-portalen är ett webbaserat program som kan användas för att skapa, hantera och ta bort Azure-resurser och -tjänster. Azure-portalen finns på [portal.azure.com](https://portal.azure.com). Den innehåller en anpassningsbar instrumentpanel och verktyg för hantering av Azure-resurser. Det ger också fakturerings- och prenumerationsinformation. Mer information finns i [Översikt över Microsoft Azure-portalen](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) och [Hantera Azure-resurser via portalen](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Resurser

Azure-resurser är enskilda beräknings-, nätverks-, data- eller appvärdtjänster som har distribuerats till en Azure-prenumeration. Några vanliga resurser är virtuella datorer, lagringskonton eller SQL-databaser. Azure-tjänster består ofta av flera relaterade Azure-resurser. En virtuell Azure-dator kan till exempel innehålla en virtuell dator, ett lagringskonto, nätverkskort och offentlig IP-adress. Dessa resurser kan skapas, hanteras och tas bort individuellt eller som en grupp. Azure-resurser beskrivs mer i detalj senare i den här guiden.

### <a name="resource-groups"></a>Resursgrupper

En Azure-resursgrupp är en behållare som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller bara resurser som du vill hantera som en grupp. Azure-resursgrupper beskrivs mer i detalj senare i den här guiden.

### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

En Azure Resource Manager-mall är en JSON-fil (JavaScript Object Notation) som definierar en eller flera resurser som ska distribueras till en resursgrupp. Den definierar också beroenden mellan distribuerade resurser. Resource Manager-mallar beskrivs mer i detalj senare i den här guiden.

### <a name="automation"></a>Automation

Förutom att skapa, hantera och ta bort resurser med hjälp av Azure-portalen kan du automatisera dessa aktiviteter med hjälp av PowerShell eller Azure command-line interface (CLI).

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell är en uppsättning moduler som tillhandahåller cmdlets för hantering av Azure. Du kan använda cmdlets för att skapa, hantera och ta bort Azure-tjänster. Cmdlets kan hjälpa dig att uppnå konsekventa, repeterbara och hands-off-distributioner. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="azure-command-line-interface"></a>Azure-kommandoradsgränssnittet

Azure-kommandoradsgränssnittet är ett verktyg som du kan använda för att skapa, hantera och ta bort Azure-resurser från kommandoraden. Azure CLI är tillgängligt för Linux, Mac OS X och Windows. Mer information och teknisk information finns [i Installera Azure CLI](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>REST API:er

Azure bygger på en uppsättning REST API:er som stöder Azure-portalens användargränssnitt. De flesta av dessa REST-API:er stöds också för att låta dig programmera och hantera dina Azure-resurser och -appar från alla Internetaktiverade enheter. Mer information finns i [Azure REST SDK Reference](https://docs.microsoft.com/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Administratörer kan komma åt Azure PowerShell och Azure CLI via en webbläsaranpassad upplevelse som kallas Azure Cloud Shell. Det här interaktiva gränssnittet är ett flexibelt verktyg för Linux- och Windows-administratörer att använda sitt kommandoradsgränssnitt, antingen Bash eller PowerShell. Azure Cloud Shell kan kommas åt via portalen ,som ett fristående webbgränssnitt på [shell.azure.com](https://shell.azure.com)eller från ett antal andra åtkomstpunkter. Mer information finns i [Översikt över Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Azure-prenumerationer

En prenumeration är en logisk gruppering av Azure-tjänster som är kopplad till ett Azure-konto. Ett enda Azure-konto kan innehålla flera prenumerationer. Fakturering för Azure-tjänster sker per prenumeration. Azure-prenumerationer har en kontoadministratör som har full kontroll över prenumerationen och en tjänstadministratör som har kontroll över alla tjänster i prenumerationen. Information om klassiska prenumerationsadministratörer finns i [Lägga till eller ändra Azure-prenumerationsadministratörer](../../cost-management-billing/manage/add-change-subscription-administrator.md). Förutom administratörer kan enskilda konton beviljas detaljerad kontroll över Azure-resurser med hjälp av [rollbaserad åtkomstkontroll (RBAC).](../../role-based-access-control/overview.md)

### <a name="select-and-enable-an-azure-subscription"></a>Välja och aktivera en Azure-prenumeration

Innan du kan arbeta med Azure-tjänster behöver du en prenumeration. Det finns flera prenumerationstyper.

**Kostnadsfria konton:** Länken för att registrera dig för ett kostnadsfritt konto finns på [Azures webbplats](https://azure.microsoft.com/). Detta ger dig en kredit under loppet av 30 dagar för att prova en kombination av resurser i Azure. Om du överskrider ditt kreditbelopp stängs ditt konto av. I slutet av utvärderingsversionen avvecklas dina tjänster och kommer inte längre att fungera. Du kan uppgradera till en prenumeration som gäller för användning när som helst.

**MSDN-prenumerationer**: Om du har en MSDN-prenumeration får du ett visst belopp i Azure-kredit varje månad. Om du till exempel har en Microsoft Visual Studio Enterprise \$med MSDN-prenumeration får du 150 per månad i Azure-kredit.

Om du överskrider kreditbeloppet inaktiveras tjänsten tills nästa månad börjar. Du kan stänga av utgiftsgränsen och lägga till ett kreditkort som ska användas för de extra kostnaderna. En del av dessa kostnader är diskonterade för MSDN-konton. Du betalar till exempel Linux-priset för virtuella datorer som kör Windows Server, och det finns ingen extra kostnad för Microsoft-servrar som Microsoft SQL Server. Detta gör MSDN-konton idealiska för utvecklings- och testscenarier.

**BizSpark-konton:** Microsoft BizSpark-programmet ger många fördelar för startups. En av dessa fördelar är tillgång till alla Microsoft-program för utvecklings- och testmiljöer för upp till fem MSDN-konton. Du får 150 USD i Azure-kredit för vart och ett av dessa fem MSDN-konton och du betalar reducerade priser för flera av Azure-tjänsterna, till exempel virtuella datorer.

**Betala per användning**: Med den här prenumerationen betalar du för det du använder genom att koppla ett kreditkort eller betalkort till kontot. Om du är en organisation kan du också godkännas för fakturering.

**Företagsavtal**: Med ett företagsavtal åtar du dig att använda ett visst antal tjänster i Azure under nästa år och du betalar det beloppet i förväg. Det engagemang som du gör förbrukas under hela året. Om du överskrider åtagandebeloppet kan du betala övertaget i efterskott. Beroende på åtagandebeloppet får du rabatt på tjänsterna i Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Bevilja administrativ åtkomst till en Azure-prenumeration

RBAC har flera inbyggda roller som du kan använda för att tilldela behörigheter. Om du vill göra en användare till administratör för en Azure-prenumeration tilldelar du dem [ägarrollen](../../role-based-access-control/built-in-roles.md#owner) i prenumerationsomfånget. Rollen ägare ger fullständig åtkomst till alla resurser i prenumerationen, inklusive rätten att ge åtkomst till andra.

Mer information finns i [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Visa faktureringsinformation i Azure-portalen

En viktig komponent för att använda Azure är möjligheten att visa faktureringsinformation. Azure-portalen ger detaljerad inblick i Azure-faktureringsinformation.

Mer information finns i [Så här hämtar du din Azure-faktura och dagliga användningsdata](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Hämta faktureringsinformation från fakturerings-API:er

Förutom att visa faktureringen i portalen kan du komma åt faktureringsinformationen med hjälp av ett skript eller program via Azure Billing REST API:er:

- Du kan använda Azure Usage API för att hämta dina användningsdata. Du kan finjustera faktureringsanvändningsinformationen genom att tagga relaterade Azure-resurser. Du kan till exempel tagga var och en av resurserna i en resursgrupp med ett avdelningsnamn eller projektnamn och sedan spåra kostnaderna specifikt för den taggen.

- Du kan använda Azure Rate Card API för att lista alla tillgängliga resurser, tillsammans med metadata och prisinformation om var och en av dessa resurser.

Mer information finns i [Få insikter om din resursförbrukning i Microsoft Azure](../../cost-management-billing/manage/usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognoskostnad med priskalkylatorn

Priserna för varje tjänst i Azure är olika. Många Azure-tjänster tillhandahåller basic-, standard- och premiumnivåer. Vanligtvis har varje nivå flera pris- och prestandanivåer. Genom att använda [onlinepriskalkylatorn](https://azure.microsoft.com/pricing/calculator)kan du skapa prisuppskattningar. Kalkylatorn innehåller flexibilitet att uppskatta kostnaden för en enskild resurs eller en grupp av resurser.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager är en distributions-, hanterings- och organisationsmekanism för Azure-resurser. Med Hjälp av Resource Manager kan du samla många enskilda resurser i en resursgrupp.

Resource Manager innehåller också distributionsfunktioner som möjliggör anpassningsbar distribution och konfiguration av relaterade resurser. Genom att använda Resource Manager kan du till exempel distribuera ett program som består av flera virtuella datorer, en belastningsutjämnare och en SQL-databas som en enda enhet. Du kan utveckla dessa distributioner med hjälp av en Resource Manager-mall.

Resource Manager har flera fördelar:

- Du kan distribuera, hantera och övervaka alla resurserna för din lösning som en grupp i stället för att hantera resurserna separat.

- Du kan distribuera din lösning flera gånger under hela utvecklingslivscykeln och ha förtroende för att dina resurser distribueras i ett konsekvent tillstånd.

- Du kan hantera infrastrukturen med hjälp av deklarativa mallar i stället för skript.

- Du kan definiera beroenden mellan resurser så att de distribueras i rätt ordning.

- Du kan använda åtkomstkontroll på alla tjänster i resursgruppen eftersom RBAC är inbyggt integrerat i hanteringsplattformen.

- Du kan använda taggar på resurser för att logiskt ordna alla resurser i prenumerationen.

- Du kan förtydliga organisationens fakturering genom att visa kostnader för en grupp resurser som delar samma tagg.

### <a name="tips-for-creating-resource-groups"></a>Tips för att skapa resursgrupper

När du fattar beslut om resursgrupperna bör du tänka på följande:

- Alla resurser i en resursgrupp ska ha samma livscykel.

- Du kan tilldela en resurs till endast en grupp i taget.

- Du kan när som helst lägga till eller ta bort en resurs från en resursgrupp. Varje resurs måste tillhöra en resursgrupp. Så om du tar bort en resurs från en grupp måste du lägga till den i en annan.

- Du kan flytta de flesta typer av resurser till en annan resursgrupp när som helst.

- Resurserna i en resursgrupp kan finnas i olika regioner.

- Du kan använda en resursgrupp för att styra åtkomsten för resurserna i den.

### <a name="building-resource-manager-templates"></a>Mallar för Building Resource Manager

Resource Manager-mallar definierar de resurser och resurskonfigurationer som ska distribueras till en enda resursgrupp. Du kan använda Resource Manager-mallar för att dirigera komplexa distributioner utan att behöva utföra överflödiga skript eller manuell konfiguration. När du har utvecklat en mall kan du distribuera den flera gånger – varje gång med ett identiskt resultat.

En Resource Manager-mall består av fyra avsnitt:

- **Parametrar**: Dessa är indata till distributionen. Parametervärden kan tillhandahållas av en människa eller en automatiserad process. An example parameter might be an admin user name and password for a Windows VM. Parametervärdena används i hela distributionen när de anges.

- **Variabler**: Dessa används för att hålla upp värden som används i hela distributionen. Till skillnad från parametrar anges inte ett variabelvärde vid distributionen. I stället är det hårt kodat eller dynamiskt genererat.

- **Resurser**: Det här avsnittet i mallen definierar de resurser som ska distribueras, till exempel virtuella datorer, lagringskonton och virtuella nätverk.

- **Utdata**: När en distribution har slutförts kan Resource Manager returnera data som dynamiskt genererade anslutningssträngar.

Följande mekanismer är tillgängliga för automatisering av distribution:

- **Funktioner**: Du kan använda flera funktioner i Resource Manager-mallar. Dessa inkluderar åtgärder som att konvertera en sträng till gemener, distribuera flera instanser av en definierad resurs och dynamiskt returnera målresursgruppen. Resource Manager-funktioner hjälper till att skapa dynamiska distributioner.

- **Resursberoenden**: När du distribuerar flera resurser kommer vissa resurser att vara beroende av andra. För att underlätta distributionen kan du använda en beroendedeklaration så att beroenderesurser distribueras före de andra.

- **Malllänkning**: Från en Resource Manager-mall kan du länka till en annan mall. Detta möjliggör distributionsdeposition i en uppsättning riktade, syftesspecifika mallar.

Du kan skapa Resource Manager-mallar i valfri textredigerare. Azure SDK för Visual Studio innehåller dock verktyg som hjälper dig. Genom att använda Visual Studio kan du lägga till resurser i mallen via en guide och sedan distribuera och felsöka mallen direkt från Visual Studio. Mer information finns i [Skapa Azure Resource Manager-mallar](../../resource-group-authoring-templates.md).

Slutligen kan du konvertera befintliga resursgrupper till en återanvändbar mall från Azure-portalen. Detta kan vara användbart om du vill skapa en distributionsmall för en befintlig resursgrupp, eller om du bara vill undersöka underliggande JSON. Om du vill exportera en resursgrupp väljer du knappen **Automation Script** i resursgruppens inställningar.

## <a name="security-of-azure-resources-rbac"></a>Säkerhet för Azure-resurser (RBAC)

Du kan bevilja driftåtkomst till användarkonton med ett angivet scope: prenumeration, resursgrupp eller enskild resurs. Det innebär att du kan distribuera en uppsättning resurser till en resursgrupp, till exempel en virtuell dator och alla relaterade resurser, och bevilja behörigheter till en viss användare eller grupp. Den här metoden begränsar åtkomsten till endast de resurser som tillhör målgruppen. Du kan också bevilja åtkomst till en enskild resurs, till exempel en virtuell dator eller ett virtuellt nätverk.

Om du vill bevilja åtkomst tilldelar du en roll till användaren eller användargruppen. Det finns många fördefinierade roller. Du kan också definiera dina egna anpassade roller.

Här är några exempel [på inbyggda roller i Azure:](../../role-based-access-control/built-in-roles.md)

- **Ägare**: En användare med den här rollen kan hantera allt, inklusive åtkomst.

- **Läsare**: En användare med den här rollen kan läsa resurser av alla slag (utom hemligheter) men kan inte göra ändringar.

- **Deltagare för virtuell dator:** En användare med den här rollen kan hantera virtuella datorer men kan inte hantera det virtuella nätverk som de är anslutna till eller lagringskontot där VHD-filen finns.

- **SQL DB Contributor**: En användare med den här rollen kan hantera SQL-databaser men inte deras säkerhetsrelaterade principer.

- **SQL Security Manager**: En användare med den här rollen kan hantera säkerhetsrelaterade principer för SQL-servrar och databaser.

- **Storage Account Contributor**: En användare med den här rollen kan hantera lagringskonton men kan inte hantera åtkomsten till lagringskontona.

Mer information finns i [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Virtuella Azure-datorer är en av de centrala IaaS-tjänsterna i Azure. Virtuella Azure-datorer stöder distributionen av virtuella Windows- eller Linux-datorer i ett Microsoft Azure-datacenter. Med Virtuella Azure-datorer har du total kontroll över vm-konfigurationen och ansvarar för all programvaruinstallation, konfiguration och underhåll.

När du distribuerar en Virtuell Azure-dator kan du välja en avbildning från Azure Marketplace eller ge dig en egen allmän avbildning. Den här avbildningen används för att tillämpa operativsystemet och den första konfigurationen. Under distributionen hanterar Resource Manager vissa konfigurationsinställningar, till exempel tilldelning av datornamn, administratörsautentiseringsuppgifter och nätverkskonfiguration. Du kan använda Azure-tillägg för virtuella datorer för att ytterligare automatisera konfigurationer som programvaruinstallation, antiviruskonfiguration och övervakningslösningar.

Du kan skapa virtuella datorer i många olika storlekar. Storleken på den virtuella datorn dikterar resursallokering, till exempel bearbetning, minne och lagringskapacitet. I vissa fall är specifika funktioner som RDMA-aktiverade nätverkskort och SSD-diskar endast tillgängliga med vissa vm-storlekar. En fullständig lista över vm-storlekar och funktioner finns i "Storlekar för virtuella datorer i Azure" för [Windows](../../virtual-machines/windows/sizes.md) och [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Användningsfall

Eftersom virtuella Azure-datorer ger fullständig kontroll över konfigurationen är de idealiska för ett brett spektrum av serverarbetsbelastningar som inte passar in i en PaaS-modell. Serverarbetsbelastningar som databasservrar (SQL Server, Oracle eller MongoDB), Windows Server Active Directory, Microsoft SharePoint och många fler blir möjliga att köra på Microsoft Azure-plattformen. Om så önskas kan du flytta sådana arbetsbelastningar från ett lokalt datacenter till en eller flera Azure-regioner, utan en stor mängd omkonfiguration.

### <a name="deployment-of-virtual-machines"></a>Distribution av virtuella datorer

Du kan distribuera virtuella Azure-datorer med hjälp av Azure-portalen, genom automatisering med Azure PowerShell-modulen eller genom att använda automatisering med plattformsoberoende CLI.

#### <a name="portal"></a>Portalen

Distribuera en virtuell dator med hjälp av Azure-portalen kräver endast en aktiv Azure-prenumeration och tillgång till en webbläsare. Du kan välja många olika operativsystemavbildningar med olika konfigurationer. Alla lagrings- och nätverkskrav konfigureras under distributionen. Mer information finns i "Skapa en virtuell dator i Azure-portalen" för [Windows](../../virtual-machines/windows/quick-create-portal.md) och [Linux](../../virtual-machines/linux/quick-create-portal.md).

Förutom att distribuera en virtuell dator från Azure-portalen kan du distribuera en Azure Resource Manager-mall från portalen. Detta distribuerar och konfigurerar alla resurser enligt definitionen i mallen. Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure-portal](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Distribuera en Virtuell Azure-dator med hjälp av PowerShell möjliggör fullständig distribution automatisering av alla relaterade virtuella datorresurser, inklusive lagring och nätverk. Mer information finns i [Skapa en Virtuell Windows-dator med Resurshanteraren och PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Förutom att distribuera Azure-beräkningsresurser individuellt kan du använda Azure PowerShell-modulen för att distribuera en Azure Resource Manager-mall. Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Kommandoradsgränssnittet (CLI)

Precis som med PowerShell-modulen tillhandahåller Azure-kommandoradsgränssnittet automatisering av distribution och kan användas på Windows-, OS X- eller Linux-system. När du använder **snabbskapningen** av Azure CLI vm distribueras alla relaterade virtuella datorresurser (inklusive lagring och nätverk) och själva virtuella datorn. Mer information finns i [Skapa en virtuell Linux-dator i Azure med hjälp av CLI](../../virtual-machines/linux/quick-create-cli.md).

På samma sätt kan du använda Azure CLI för att distribuera en Azure Resource Manager-mall. Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Åtkomst och säkerhet för virtuella datorer

Åtkomst till en virtuell dator från Internet kräver att det associerade nätverksgränssnittet, eller belastningsutjämnaren, om tillämpligt, konfigureras med en offentlig IP-adress. Den offentliga IP-adressen innehåller ett DNS-namn som ska matchas till den virtuella datorn eller belastningsutjämnaren. Mer information finns [i IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Du hanterar åtkomst till den virtuella datorn via den offentliga IP-adressen med hjälp av en NSG-resurs (Network Security Group). En NSG fungerar som en brandvägg och tillåter eller nekar trafik över nätverksgränssnittet eller undernätet på en uppsättning definierade portar. Om du till exempel vill skapa en fjärrskrivbordssession med en Virtuell Azure-dator måste du konfigurera NSG för att tillåta inkommande trafik på port 3389. Mer information finns i [Öppna portar till en virtuell dator i Azure med Azure-portalen](../../virtual-machines/windows/nsg-quickstart-portal.md).

Slutligen, som med hanteringen av alla datorsystem, bör du tillhandahålla säkerhet för en virtuell Azure-dator i operativsystemet med hjälp av säkerhetsautentiseringsuppgifter och programvarubrandvägger.

## <a name="azure-storage"></a>Azure Storage

Azure Storage är en Microsoft-hanterad tjänst som tillhandahåller hållbar, skalbar och redundant lagring. Du kan lägga till ett Azure-lagringskonto som en resurs till valfri resursgrupp med hjälp av någon resursdistributionsmetod. Azure innehåller fyra lagringstyper: Blob storage, Fillagring, Tabelllagring och Kölagring. När du distribuerar ett lagringskonto är två kontotyper tillgängliga, allmänt ändamål och blob-lagring. Ett allmänt lagringskonto ger dig åtkomst till alla fyra lagringstyperna. Blob-lagringskonton liknar allmänna konton, men innehåller specialiserade blobbar som innehåller nivåer för varm och kall åtkomst. Mer information om blob-lagring finns i [Azure Blob storage](../../storage/blobs/storage-blob-storage-tiers.md).

Azure-lagringskonton kan konfigureras med olika redundansnivåer:

- **Lokalt redundant lagring** ger hög tillgänglighet genom att se till att tre kopior av alla data görs synkront innan en skrivning anses vara framgångsrik. Dessa kopior lagras i en enda anläggning i en enda region. Replikerna finns i separata feldomäner och uppgraderingsdomäner. Det innebär att data är tillgängliga även om en lagringsnod som innehåller dina data misslyckas eller tas offline för att uppdateras.

- **Geo-redundant lagring** gör tre synkrona kopior av data i den primära regionen för hög tillgänglighet och sedan asynkront gör tre repliker i en parkopplad region för haveriberedskap.

- **Geo redundant lagring** med läsåtkomst är geoupptundret lagring plus möjligheten att läsa data i den sekundära regionen. Denna förmåga gör den lämplig för partiell katastrofåterställning. Om det finns ett problem med den primära regionen kan du ändra programmet så att det har skrivskyddad åtkomst till den parade regionen.

### <a name="use-cases"></a>Användningsfall

Varje lagringstyp har olika användningsfall.

#### <a name="blob-storage"></a>Blob Storage

Ordet *blob* är en akronym för *binära stora objekt*. Blobbar är ostrukturerade filer som de som du lagrar på datorn. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring. Azure Blob storage innehåller även Azure Virtual Machines-datadiskar.

Azure Storage stöder tre typer av blobar:

- **Blockblobar** används för att hålla vanliga filer upp till 195 GB i storlek (4 MB × 50 000 block). I första hand används blockblobar för lagring av filer som läses från början till slut, som mediefiler eller bildfiler för webbplatser. De heter blockblobar eftersom filer som är större än 64 MB måste överföras som små block. Dessa block konsolideras sedan (eller bekräftas) i den slutliga blobben.

- **Sidblobar** används för att lagra slumpmässiga åtkomstfiler upp till 1 TB i storlek. Sidblobar används främst som stödlagring för de virtuella hårddiskar som tillhandahåller varaktiga diskar för Virtuella Azure-datorer, IaaS-beräkningstjänsten i Azure. De kallas för sidblobar eftersom de erbjuder slumpmässig läs-/skrivåtkomst till sidor på 512 bytes.

- **Lägg till blobbar** består av block som blockblobar, men de är optimerade för tilläggsåtgärder. Dessa används ofta för att logga information från en eller flera källor till samma blob. Du kan till exempel skriva all spårningsloggning till samma tilläggsblob för ett program som körs på flera virtuella datorer. En enda tilläggsblob kan ha upp till 195 GB.

Mer information finns i [Komma igång med Azure Blob-lagring med .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>File Storage

Azure File storage är en tjänst som erbjuder filresurser i molnet med hjälp av standardprotokollet För servermeddelandeblock (SMB). Tjänsten stöder både SMB 2.1 och SMB 3.0. Med Azure File Storage kan du migrera program som förlitar sig på filresurser till Azure snabbt och utan kostsamma omskrivningar. Program som körs på virtuella Azure-datorer, i molntjänster eller från lokala klienter kan montera en filresurs i molnet. Detta liknar hur ett skrivbordsprogram monterar en typisk SMB-resurs. Ett obegränsat antal programkomponenter kan sedan montera och komma åt fillagringsresursen samtidigt.

Eftersom en fillagringsresurs är en standard-SMB-filresurs kan program som körs i Azure komma åt data i resursen via filsystem-I/O-API:er. Utvecklare kan därför använda sin befintliga kod och sina kunskaper för att migrera befintliga program. IT-proffs kan använda PowerShell-cmdlets för att skapa, montera och hantera fillagringsresurser som en del av administrationen av Azure-program.

Mer information finns i [Komma igång med Azure File storage på Windows](../../storage/files/storage-how-to-use-files-windows.md) eller Så här använder du Azure File storage med [Linux](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Table Storage

Azure Table Storage är en tjänst som lagrar strukturerade NoSQL-data i molnet. Tabelllagring är ett nyckel-/attributarkiv med en schemalös design. Eftersom tabelllagring är schemalös är det enkelt att anpassa dina data när behoven i ditt program utvecklas. Åtkomsten till data är snabb och kostnadseffektiv för alla typer av program. Kostnaden för Table Storage är normalt sett betydligt lägre än för motsvarande volymer med traditionell SQL.

Du kan använda Table Storage för att lagra flexibla datauppsättningar, till exempel användardata för webbprogram, adressböcker, enhetsinformation och andra typer av metadata som din tjänst kräver. Du kan lagra valfritt antal entiteter i en tabell. Ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

Mer information finns i [Komma igång med Azure Table Storage](../../cosmos-db/table-storage-how-to-use-dotnet.md).

#### <a name="queue-storage"></a>Queue Storage

Azure Queue Storage innehåller molnmeddelandehantering mellan programkomponenter. Vid utformningen av program för skalning frikopplas programkomponenter ofta så att de kan skalas oberoende av sig. Queue Storage är en asynkron meddelandelösning för kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

Mer information finns i [Komma igång med Azure Queue storage](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Distribuera ett lagringskonto

Det finns flera alternativ för att distribuera ett lagringskonto.

#### <a name="portal"></a>Portalen

Distribuera ett lagringskonto med hjälp av Azure-portalen kräver bara en aktiv Azure-prenumeration och åtkomst till en webbläsare. Du kan distribuera ett nytt lagringskonto till en ny eller befintlig resursgrupp. När du har skapat lagringskontot kan du skapa en blob-behållare eller filresurs med hjälp av portalen. Du kan skapa tabell- och kölagringsentiteter programmässigt. Mer information finns i [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).

Förutom att distribuera ett lagringskonto från Azure-portalen kan du distribuera en Azure Resource Manager-mall från portalen. Detta distribuerar och konfigurerar alla resurser enligt definitionen i mallen, inklusive alla lagringskonton. Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure-portal](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Distribuera ett Azure-lagringskonto med hjälp av PowerShell möjliggör fullständig distribution automatisering av lagringskontot. Mer information finns i [Använda Azure PowerShell med Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Förutom att distribuera Azure-resurser individuellt kan du använda Azure PowerShell-modulen för att distribuera en Azure Resource Manager-mall. Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Kommandoradsgränssnittet (CLI)

Precis som med PowerShell-modulen tillhandahåller Azure-kommandoradsgränssnittet automatisering av distribution och kan användas på Windows-, OS X- eller Linux-system. Du kan använda kommandot Skapa Azure **CLI-lagringskonto** för att skapa ett lagringskonto. Mer information finns [i Använda Azure CLI med Azure Storage.](../../storage/common/storage-azure-cli.md)

På samma sätt kan du använda Azure CLI för att distribuera en Azure Resource Manager-mall. Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Åtkomst och säkerhet för Azure Storage

Azure Storage nås på olika sätt, inklusive azure-portalen, under skapande och drift av den virtuella datorn och från lagringsklientbibliotek.

#### <a name="virtual-machine-disks"></a>Diskar med virtuella datorer

När du distribuerar en virtuell dator måste du också skapa ett lagringskonto för att lagra operativsystemets hårddisk för den virtuella datorn och eventuella ytterligare datadiskar. Du kan välja ett befintligt lagringskonto eller skapa ett nytt. Eftersom den maximala storleken på en blob är 1 024 GB har en enda virtuell disk en maximal storlek på 1 023 GB. Om du vill konfigurera en större datadisk kan du presentera flera datadiskar för den virtuella datorn och slå samman dem som en enda logisk disk. Mer information finns i "Hantera Azure-diskar" för [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) och [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Lagringsverktyg

Azure-lagringskonton kan nås via många olika lagringsutforskare, till exempel Visual Studio Cloud Explorer. Med de här verktygen kan du bläddra igenom lagringskonton och data. Mer information och en lista över tillgängliga lagringsutforskare finns i [Azure Storage-klientverktyg](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>Api för lagring

Lagringsresurser kan nås av alla språk som kan göra HTTP/HTTPS-begäranden. Dessutom erbjuder Azure Storage programmeringsbibliotek för flera populära språk. Dessa bibliotek förenklar arbetet med Azure Storage genom att hantera information som synkron och asynkron anrop, batchbearbetning av åtgärder, undantagshantering och automatiska återförsök. Mer information finns i [AZURE Storage-tjänst REST API-referens](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Åtkomstnycklar för lagring

Varje lagringskonto har två autentiseringsnycklar, en primär och en sekundär. Antingen kan användas för lagringsåtkomståtgärder. Dessa lagringsnycklar används för att skydda ett lagringskonto och krävs för att programmässigt komma åt data. Det finns två nycklar för att tillåta tillfällig överrullning av nycklarna för att öka säkerheten. Det är viktigt att skydda nycklarna eftersom deras innehav, tillsammans med kontonamnet, ger obegränsad åtkomst till data i lagringskontot.

#### <a name="shared-access-signatures"></a>Signaturer för delad åtkomst

Om du behöver tillåta användare att ha kontrollerad åtkomst till dina lagringsresurser kan du skapa en signatur för delad åtkomst. En signatur för delad åtkomst är en token som kan läggas till i en URL som möjliggör delegerad åtkomst till en lagringsresurs. Alla som har token kan komma åt den resurs som den pekar på med de behörigheter som den anger, under den tidsperiod som den är giltig. Mer information finns i [Använda signaturer för delad åtkomst](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network

Virtuella nätverk är nödvändiga för att stödja kommunikation mellan virtuella datorer. Du kan definiera undernät, anpassad IP-adress, DNS-inställningar, säkerhetsfiltrering och belastningsutjämning. Azure stöder olika användningsområden: molnbaserade nätverk eller hybridvirtiska nätverk.

### <a name="cloud-only-virtual-networks"></a>Virtuella nätverk endast i molnet

Ett virtuellt Azure-nätverk är som standard endast tillgängligt för resurser som lagras i Azure. Resurser som är anslutna till samma virtuella nätverk kan kommunicera med varandra. Du kan associera nätverksgränssnitt för virtuella datorer och belastningsutjämnare med en offentlig IP-adress för att göra den virtuella datorn tillgänglig via Internet. Du kan skydda åtkomsten till de offentligt exponerade resurserna med hjälp av en nätverkssäkerhetsgrupp.

![Azure Virtual Network för ett webbprogram på två nivåer](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hybrid virtuella nätverk

Du kan ansluta ett lokalt nätverk till ett virtuellt Azure-nätverk med hjälp av ExpressRoute eller en VPN-anslutning från plats till plats. I den här konfigurationen är det virtuella Azure-nätverket i huvudsak ett molnbaserat tillägg av ditt lokala nätverk.

Eftersom det virtuella Azure-nätverket är anslutet till ditt lokala nätverk måste lokala virtuella nätverk använda en unik del av adressutrymmet som din organisation använder. På samma sätt som olika företagsplatser tilldelas ett visst IP-undernät blir Azure en annan plats när du utökar nätverket.
Det finns flera alternativ för att distribuera ett virtuellt nätverk.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [Powershell](../../virtual-network/quick-create-powershell.md)

- [Kommandoradsgränssnittet (CLI)](../../virtual-network/quick-create-cli.md)

- Azure Resource Manager-mallar

> **När du ska använda:** När du arbetar med virtuella datorer i Azure arbetar du med virtuella nätverk. Detta gör det möjligt att segmentera dina virtuella datorer i offentliga och privata undernät liknande lokala datacenter.
>
> **Kom igång:** Distribuera ett virtuellt Azure-nätverk med hjälp av Azure-portalen kräver bara en aktiv Azure-prenumeration och åtkomst till en webbläsare. Du kan distribuera ett nytt virtuellt nätverk till en ny eller befintlig resursgrupp. När du skapar en ny virtuell dator från portalen kan du välja ett befintligt virtuellt nätverk eller skapa ett nytt. Komma igång och [skapa ett virtuellt nätverk med Azure-portalen](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Åtkomst och säkerhet för virtuella nätverk

Du kan skydda virtuella Azure-nätverk med hjälp av en nätverkssäkerhetsgrupp. NSG innehåller en lista över ACL-regler (Access Control List) som tillåter eller nekar nätverkstrafik till vm-instanser i ett virtuellt nätverk. Du kan associera NSG med antingen undernät eller enskilda VM-instanser i det undernätet. När du associerar en NSG med ett undernät gäller ACL-reglerna för alla VM-instanser i det undernätet. Dessutom kan du ytterligare begränsa trafiken till en enskild virtuell dator genom att associera en NSG direkt med den virtuella datorn. Mer information finns i [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Nästa steg

- [Skapa en virtuell Windows-dator](../../virtual-machines/windows/quick-create-portal.md)
- [Skapa en virtuell Linux-dator](../../virtual-machines/linux/quick-create-portal.md)
