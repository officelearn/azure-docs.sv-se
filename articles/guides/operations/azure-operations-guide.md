---
title: Startguide för Azure IT-operatörer | Microsoft Docs
description: Kom igång-guide för Azure IT-operatörer
services: ''
documentationcenter: ''
author: themichaelbender-ms
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 08/24/2018
ms.author: mibender
ms.openlocfilehash: 3ae912eb88972246577954cb2ff103ba607f18bf
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43110505"
---
# <a name="get-started-for-azure-it-operators"></a>Kom igång för Azure IT-operatörer

Den här guiden beskriver grundläggande begrepp för distribution och hantering av en Microsoft Azure-infrastrukturen. Om du är nybörjare till molnet, eller på Azure, den här guiden hjälper dig att snabbt komma igång med begrepp, distribution och av hanteringsinformation. Många avsnitt i den här guiden beskrivs en åtgärd, till exempel distribuerar en virtuell dator och ange sedan en länk för mer detaljerad teknisk information.

## <a name="cloud-computing-overview"></a>Översikt över molntjänster

Molnbaserad databehandling är en modern alternativ till traditionella lokala datacenter. Offentliga molnleverantörer tillhandahålla och hantera alla datorinfrastrukturen och underliggande programvaran för hantering. Dessa leverantörer erbjuder en mängd olika molntjänster. En molnbaserad tjänst kanske i det här fallet en virtuell dator, en webbserver eller molnbaserade databasmotorn. Som kund cloud-providern eftersom dessa molntjänster som det behövs. På så sätt kan konvertera du de stora kapitalinvesteringar maskinvaruunderhåll till en driftskostnad. En molnbaserad tjänst har också följande fördelar:

- Snabb distribution av stora beräkningsmiljöer

- Snabb frigörs för system som inte längre behövs

- Enkel distribution av traditionellt komplexa system som belastningsutjämnare

- Möjligheten att tillhandahålla flexibla beräkningskapacitet eller skala vid behov

- Mer kostnadseffektiv datormiljöer

- Komma åt från var som helst med en webbaserad portal eller programmässiga automation

- Molnbaserade tjänster som uppfyller de flesta behov av beräkning och program

Du har fullständig kontroll över maskinvara och programvara som distribueras med den lokala infrastrukturen. Historiskt sett har leder detta till maskinvara inköp beslut som fokuserar på att skala upp. Ett exempel är att köpa en server med flera kärnor efter behov för högsta prestanda. Den här infrastrukturen kan tyvärr underutnyttjade utanför ett fönster för begäran. Med Azure kan du distribuera den infrastruktur som du behöver och justera detta uppåt eller nedåt när som helst. Detta leder till fokus på att skala ut till distribution av ytterligare beräkningsnoder att uppfylla ett behov av prestanda. Skala ut molntjänster är mer kostnadseffektivt än att skala upp via dyr maskinvara.

Microsoft har distribuerat många Azure-Datacenter runtom i världen med mer planerat. Dessutom ökar Microsoft suveräna moln i områden som Kina och Tyskland. Endast de största globala företag kan distribuera datacenter i det här sättet, så att använda Azure gör det enkelt för företag i alla storlekar och distribuera sina tjänster nära sina kunder.

För små företag kan Azure för en låg kostnad startpunkt möjlighet att skala snabbt som behovet av beräkning ökar. Detta förhindrar en direkta göra stora investeringar i infrastruktur och ger dig flexibilitet att skapa och ändra arkitekturen system efter behov. Användning av molnbaserad databehandling passar bra med skala snabbt och snabbavstängning modellen med start tillväxt.

Mer information om tillgängliga Azure-regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Datamodell för molnet

Azure använder en modell som baseras på kategorier av tjänsten som tillhandahålls till kunder för molntjänster. Tre kategorier av tjänsten innehåller infrastruktur som en tjänst (IaaS), plattform som en tjänst (PaaS) och programvara som en tjänst (SaaS). Leverantörer delar vissa eller alla ansvaret för komponenterna i stapeln databehandling i var och en av dessa kategorier. Låt oss ta en titt på var och en av olika kategorier av cloud computing.
![Molnbaserad databehandling Stack jämförelse](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastruktur som en tjänst

En IaaS-leverantör för molnet kör och hanterar alla fysiska beräkningsresurser och programvaran som krävs för att aktivera datorvirtualisering. En kund för den här tjänsten distribuerar virtuella datorer i de här värdbaserade datacenter. Även om de virtuella datorerna finns i ett datacenter på annan plats, har IaaS-användare kontroll över konfiguration och hantering av operativsystemet som lämnar den underliggande infrastrukturen till molnet-leverantören.

Azure innehåller flera IaaS-lösningar inklusive virtuella datorer, skalningsuppsättningar för virtuella datorer och relaterade nätverksinfrastrukturen. Virtuella datorer är ett populärt alternativ för att migrera inledningsvis tjänster till Azure eftersom det gör det möjligt för en ”lift and shift” migration-modellen. Du kan konfigurera en virtuell dator som den infrastruktur som för närvarande körs dina tjänster i ditt datacenter och migrera sedan programvaran till den nya virtuella datorn. Du kan behöva göra uppdateringar, till exempel URL: er till andra tjänster eller lagring, men du kan migrera många program i det här sättet.

VM-skalningsuppsättningar är byggda ovanpå Azure Virtual Machines och ger ett enkelt sätt att distribuera kluster av identiska virtuella datorer. VM-skalningsuppsättningar stöder även automatisk skalning så att nya virtuella datorer kan distribueras automatiskt vid behov. Detta gör VM-skalningsuppsättningar en utmärkt plattform för att på högre nivå mikrotjänst beräkning värdkluster, till exempel Azure Service Fabric och Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: Plattform som en tjänst

Med PaaS kan distribuera du programmet till en miljö som cloud service-leverantör har. Leverantören har alla infrastrukturhantering så att du kan fokusera på programutveckling och datahantering.

Azure tillhandahåller flera PaaS-beräkning erbjudanden, inklusive Web Apps-funktionen i Azure App Service och Azure Cloud Services (webb- och worker-roller). I båda fallen har utvecklare flera sätt att distribuera sina program utan att veta något om på detaljerna som stöder den. Utvecklare behöver inte skapa virtuella datorer (VM), Använd Remote Desktop Protocol (RDP) för att logga in på var och en eller installera programmet. De bara trycker på en knapp (eller Stäng till den) och de verktyg som tillhandahålls av Microsoft etablera de virtuella datorerna och distribuerar sedan och installera programmet på dem.

#### <a name="saas-software-as-a-service"></a>SaaS: Programvara som en tjänst

SaaS är programvara som är hyst och hanterad centralt. Det är vanligtvis baserat på en arkitektur med flera innehavare, en version av programmet används för alla kunder. Det kan skaländras ut till flera instanser säkerställa bästa prestanda på alla platser. SaaS-programvaran licensieras vanligtvis via en månatligt eller årligt betalning. SaaS-programvaran licensieras vanligtvis via en månatligt eller årligt betalning. SaaS-programvaruleverantörer ansvarar för alla komponenter i programvara-stacken, så allt du hanterar är de tjänster som tillhandahålls.

Microsoft Office 365 är ett bra exempel på en SaaS erbjuder. Prenumeranter betala en prenumerationsavgift för månatligt eller årligt och de får Microsoft Exchange, Microsoft OneDrive och resten av Microsoft Office-paket som en tjänst. Prenumeranter får alltid den senaste versionen och Exchange-servern hanteras åt dig. Installera och uppgradera Office varje år jämfört med, detta är billigare och kräver mindre arbete.

## <a name="azure-services"></a>Azure-tjänster

Azure erbjuder många tjänster i dess plattform för molnbaserad databearbetning. Dessa tjänster inkluderar följande.

### <a name="compute-services"></a>Beräkningstjänster

Tjänster för som är värd för och köra arbetsbelastning:

- Azure-datorer – både Linux och Windows

- App Services (Web Apps, Mobile Apps, Logic Apps, API-appar och Funktionsappar)

- Azure Batch (för storskaliga parallelljobb eller batchjobb)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Datatjänster

Tjänster för att lagra och hantera data:

- Azure Storage (består av tjänsten Azure Blob, kö, tabell och fil)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Redis Cache

### <a name="application-services"></a>Programtjänster

Tjänster för att bygga och driva program:

- Azure Active Directory (Azure AD)

- Azure Service Bus för att ansluta distribuerade system

- Azure HDInsight för bearbetning av stordata

- Azure Scheduler

- Azure Media Services

### <a name="network-services"></a>Nätverkstjänster

Tjänster för nätverk både i Azure och mellan Azure och lokala datacenter:

- Azure Virtual Network

- Azure ExpressRoute

- Azure-tillhandahållna DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Detaljerad dokumentation om Azure-tjänster finns [dokumentation om Azure service](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Azure viktiga begrepp

### <a name="datacenters-and-regions"></a>Datacenter och regioner

Azure är en global molnplattform som är allmänt tillgängligt i många regioner runt om i världen. När du etablerar ett program, en tjänst eller en virtuell dator i Azure kan uppmanas du att välja en region. Den valda regionen representerar ett speciﬁc datacenter där programmet körs. Mer information finns i [Azure-regioner](https://azure.microsoft.com/regions/).

En av beneﬁts med att använda Azure är att du kan distribuera dina program i olika Datacenter runtom i världen. Den region som du väljer kan aﬀect programmets prestanda. Det är optimala för att välja en region som ligger närmast mest kunderna genom att minska svarstiden i nätverksbegäranden. Du kan också välja en region för att uppfylla juridiska krav för att distribuera din app i vissa länder.

### <a name="azure-portal"></a>Azure Portal

Azure-portalen är ett webbaserat program som kan användas för att skapa, hantera och ta bort Azure-resurser och tjänster. Azure-portalen finns i [Shell.Azure.com](https://portal.azure.com). Den innehåller en anpassningsbar instrumentpanel och verktyg för hantering av Azure-resurser. Den innehåller också information om fakturering och prenumeration. Mer information finns i [översikt över Microsoft Azure portal](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) och [hantera Azure-resurser via portalen](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Resurser

Azure-resurser är enskilda beräkning, nätverk, data eller app som är värd för tjänster som har distribuerats till en Azure-prenumeration. Vissa vanliga resurser är virtuella datorer, lagringskonton eller SQL-databaser. Azure-tjänster består ofta av flera relaterade Azure-resurser. En Azure virtuell dator kan exempelvis innehålla en virtuell dator, storage-konto, nätverkskort och offentlig IP-adress. De här resurserna kan skapas, hanteras och tas bort separat eller som en grupp. Azure-resurser beskrivs mer utförligt senare i den här guiden.

### <a name="resource-groups"></a>Resursgrupper

En Azure-resursgrupp är en behållare som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen eller endast de resurser som du vill hantera som en grupp. Azure-resursgrupper beskrivs mer utförligt senare i den här guiden.

### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

En Azure Resource Manager-mallen är en JavaScript Object Notation (JSON)-fil som definierar en eller flera resurser som ska distribueras till en resursgrupp. Den definierar även beroenden mellan distribuerade resurser. Resource Manager-mallar beskrivs mer utförligt senare i den här guiden.

### <a name="automation"></a>Automation

Förutom att skapa, hantera och ta bort resurser med hjälp av Azure-portalen, kan du automatisera dessa aktiviteter med hjälp av PowerShell eller kommandoradsgränssnittet (CLI).

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell är en uppsättning moduler med cmdletar för hantering av Azure. Du kan använda cmdlets för att skapa, hantera och ta bort Azure-tjänster. Cmdletarna som kan hjälpa dig att kan uppnå konsekvent, upprepningsbart och helautomatisk distributioner. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

#### <a name="azure-command-line-interface"></a>Azure-kommandoradsgränssnittet

Azure-kommandoradsgränssnittet är ett verktyg som du kan använda för att skapa, hantera och ta bort Azure-resurser från kommandoraden. Azure CLI är tillgängligt för Linux, Mac OS X och Windows. Mer information och teknisk information finns i [installera Azure CLI](/cli/azure/install-azure-cli.md).

#### <a name="rest-apis"></a>REST API:er

Azure bygger på en uppsättning REST API: er som har stöd för Azure-portalens användargränssnitt. De flesta av de här REST-API: er stöds även om du vill kan du etablera och hantera dina Azure-resurser och appar från valfri Internet-aktiverad enhet programmässigt. Mer information finns i den [Azure REST SDK-referens](https://docs.microsoft.com/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Administratörer kan använda Azure PowerShell och Azure CLI via en webbläsare-tillgänglig upplevelse som kallas Azure Cloud Shell. Det här interaktiva gränssnittet ger ett flexibelt verktyg för Linux och Windows-administratörer använda sina kommandoradsgränssnittet föredrar, Bash eller PowerShell. Azure Cloud Shell kan vara åtkomst via portalen, som ett fristående webbgränssnitt på [shell.azure.com](https://shell.azure.com), eller från ett antal andra åtkomstpunkter. Mer information finns i [översikt av Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Azure-prenumerationer

En prenumeration är en logisk gruppering av Azure-tjänster som är länkad till ett Azure-konto. Ett enskilt Azure-konto kan innehålla flera prenumerationer. Fakturering för Azure-tjänster som görs på basis av per prenumeration. Azure-prenumerationer har en kontoadministratör som har fullständig kontroll över prenumerationen och en tjänstadministratör kan vem som har kontroll över alla tjänster i prenumerationen. Förutom administratörer, enskilda konton kan få detaljerad kontroll över Azure-resurser via rollbaserad åtkomstkontroll (RBAC).

### <a name="select-and-enable-an-azure-subscription"></a>Välja och aktivera en Azure-prenumeration

Innan du kan arbeta med Azure-tjänster behöver du en prenumeration. Det finns flera typer av prenumerationer.

#### <a name="free-accounts-the-link-to-sign-up-for-a-free-account-is-on-the-azure-websitehttpsazuremicrosoftcom-this-gives-you-a-credit-over-the-course-of-30-days-to-try-any-combination-of-resources-in-azure-if-you-exceed-your-credit-amount-your-account-is-suspended-at-the-end-of-the-trial-your-services-are-decommissioned-and-will-no-longer-work-you-can-upgrade-to-a-pay-as-you-go-subscription-at-any-time"></a>Kostnadsfria konton: länken för att registrera dig för ett kostnadsfritt konto finns på den [Azure-webbplatsen](https://azure.microsoft.com/). Detta ger dig en kredit under loppet av 30 dagar att prova valfri kombination av resurser i Azure. Om du överskrider din kredit är har ditt konto inaktiverats. I slutet av utvärderingsperioden dina tjänster tas ur drift och fungerar inte längre. Du kan uppgradera till en användningsbaserad prenumeration när som helst.

#### <a name="msdn-subscriptions-if-you-have-an-msdn-subscription-you-get-a-specific-amount-in-azure-credit-each-month-for-example-if-you-have-a-microsoft-visual-studio-enterprise-with-msdn-subscription-you-get-150-per-month-in-azure-credit"></a>MSDN-prenumerationer: Om du har en MSDN-prenumeration får du ett specifikt belopp i Azure-kredit varje månad. Till exempel, om du har en Microsoft Visual Studio Enterprise med MSDN-prenumeration kan du få \$150 per månad i Azure-kredit.

Om du överstiger mängden som kredit inaktiveras tjänsten förrän nästa månad startar. Du kan inaktivera utgiftsgränsen och lägga till ett kreditkort som ska användas för ytterligare kostnader. Vissa av dessa kostnader är rabatterade för MSDN-konton. Exempelvis kan du betalar bara Linux-priset för virtuella datorer som kör Windows Server och det finns ingen extra kostnad för Microsoft-servrar, till exempel Microsoft SQL Server. Detta gör att MSDN-konton är perfekt för utveckling och testscenarion.

#### <a name="bizspark-accounts-the-microsoft-bizspark-program-provides-many-benefits-to-startups-one-of-those-benefits-is-access-to-all-the-microsoft-software-for-development-and-test-environments-for-up-to-five-msdn-accounts-you-get-150-in-azure-credit-for-each-of-those-five-msdn-accounts-and-you-pay-reduced-rates-for-several-of-the-azure-services-such-as-virtual-machines"></a>BizSpark-konton: The Microsoft BizSpark-programmet ger många fördelar för nystartade företag. En av dessa fördelar är åtkomst till alla Microsoft-programvara för utvecklings- och testmiljöer för upp till fem MSDN-konton. Du får 150 USD i Azure-kredit för var och en av dessa fem MSDN-konton och du betalar reducerade priser för flera av de Azure-tjänsterna, till exempel virtuella datorer.

#### <a name="pay-as-you-go-with-this-subscription-you-pay-for-what-you-use-by-attaching-a-credit-card-or-debit-card-to-the-account-if-you-are-an-organization-you-can-also-be-approved-for-invoicing"></a>Betala per användning: Med den här prenumerationen betalar du för det du använder genom att koppla ett kreditkort eller bankkort till kontot. Om du är en organisation kan godkännas du också för fakturering.

#### <a name="enterprise-agreements-with-an-enterprise-agreement-you-commit-to-using-a-certain-number-of-services-in-azure-over-the-next-year-and-you-pay-that-amount-ahead-of-time-the-commitment-that-you-make-is-consumed-throughout-the-year-if-you-exceed-the-commitment-amount-you-can-pay-the-overage-in-arrears-depending-on-the-amount-of-the-commitment-you-get-a-discount-on-the-services-in-azure"></a>Enterprise-avtal: med ett enterprise-avtal du åtaganden att använda ett visst antal tjänster i Azure under nästa år, och du betalar det beloppet förbereds i förväg. Du gör åtagandet förbrukas sedan under året. Om du överstiger åtagandet, kan du betala överanvändning i efterhand. Beroende på mängden åtagandet få rabatt på tjänster i Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Tilldela administrativ åtkomst till en Azure-prenumeration

Flera administratörsroller för kontot är tillgängliga och kan ändras när som helst. Det finns två viktiga roller:

- **Tjänstadministratör**: den här rollen har behörighet att hantera Azure-tjänster. Som standard beviljas åtkomst till samma konto som kontoadministratör.

- **Medadministratör**: den här rollen har samma åtkomst som tjänstadministratör. Den här rollen kan inte ändra associationen mellan en prenumeration och Azure-kataloger.

Mer information finns i [lägga till eller ändra Azure-administratörsroller](../../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Visa faktureringsinformation i Azure portal

En viktig komponent för att använda Azure är möjligheten att visa faktureringsinformation. Azure-portalen ger detaljerad inblick i faktureringsinformation.

Mer information finns i [hur du laddar ned din Azure-fakturering och daglig användningsdata](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Hämta faktureringsinformation från billing API: er

Förutom att visa faktureringen i portalen kan komma du åt faktureringsinformation genom att använda ett skript eller program via ett Azure-fakturering REST-API:

- Du kan använda API för användning i Azure för att hämta användningsdata. Du kan finjustera faktureringsinformation för användning genom att tagga relaterade Azure-resurser. Du kan till exempel tagga resurser i en resursgrupp med en avdelningsnamn eller projektnamn och sedan spåra kostnader specifikt för en taggen.

- Du kan använda API: et för Azure Rate-kort för att lista alla tillgängliga resurser, tillsammans med metadata och information om var och en av dessa resurser om priser.

Mer information finns i [insyn i din Microsoft Azure-resursförbrukning](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognoskostnad med priskalkylatorn

Priser för varje tjänst i Azure är olika. Många Azure-tjänster ge nivåerna Basic, Standard och Premium. Varje nivå har vanligtvis flera nivåer för pris och prestanda. Med hjälp av den [priskalkylator](http://azure.microsoft.com/pricing/calculator), du kan skapa prissättning uppskattningar. Kalkylatorn omfattar flexibilitet för att beräkna kostnaden på en enskild resurs eller en grupp med resurser.

### <a name="set-up-billing-alerts"></a>Ställa in faktureringsvarningar

När du har distribuerat programmet eller lösningen på Azure, kan du skapa aviseringar som skickar du e-post när metoden utgiftsgränser som definierats i aviseringen. Mer information finns i [ställa in faktureringsvarningar för dina Microsoft Azure-prenumerationer](../../billing/billing-set-up-alerts.md).

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager är en mekanism för distribution, hantering och organisation för Azure-resurser. Genom att använda Resource Manager kan placera du många enskilda resurser tillsammans i en resursgrupp.

Resource Manager innehåller också distributionsfunktioner för som gör det anpassningsbara distributionen och konfigurationen av relaterade resurser. Du kan till exempel distribuera ett program som består av flera virtuella datorer, en belastningsutjämnare och en SQL-databas som en enhet med hjälp av Resource Manager. Du kan utveckla dessa distributioner med hjälp av Resource Manager-mall.

Resource Manager har flera fördelar:

- Du kan distribuera, hantera och övervaka alla resurserna för din lösning som en grupp i stället för att hantera resurserna separat.

- Du kan distribuera lösningen genom hela livscykeln flera gånger och alltid vara säker på att resurserna distribueras i ett konsekvent tillstånd.

- Du kan hantera infrastrukturen med hjälp av deklarativa mallar i stället för skript.

- Du kan definiera beroenden mellan resurser så att de distribueras i rätt ordning.

- Du kan använda åtkomstkontroll för alla tjänster i resursgruppen eftersom RBAC är inbyggt i hanteringsplattformen.

- Du kan lägga till taggar för resurser och organisera dem logiskt alla resurser i din prenumeration.

- Du kan tydliggöra din organisations fakturering genom att visa kostnaderna för en grupp med resurser som delar samma tagg.

### <a name="tips-for-creating-resource-groups"></a>Tips för att skapa resursgrupper

När du gör beslut om dina resursgrupper, Överväg dessa tips:

- Alla resurser i en resursgrupp bör ha samma livscykel.

- Du kan tilldela en resurs till endast en grupp i taget.

- Du kan lägga till eller ta bort en resurs från en resursgrupp när som helst. Alla resurser måste tillhöra en resursgrupp. Så om du tar bort en resurs från en grupp, måste du lägga till den till en annan.

- Du kan flytta de flesta typer av resurser till en annan resursgrupp när som helst.

- Resurserna i en resursgrupp kan finnas i olika regioner.

- Du kan använda en resursgrupp för att kontrollera åtkomsten för resurser i den.

### <a name="building-resource-manager-templates"></a>Skapa Resource Manager-mallar

Resource Manager-mallar definiera deklarativt resurser och resurskonfigurationer som ska distribueras i en enda resursgrupp. Du kan använda Resource Manager-mallar för att dirigera komplexa distributioner utan att behöva ytterligare skript eller manuell konfiguration. När du utvecklar en mall kan du distribuera det flera gånger, varje gång med identiska resultat.

Resource Manager-mall består av fyra delar:

- **Parametrar**: det här är indata för distributionen. Parametervärden kan anges av en människa eller en automatiserad process. En exempel-parameter kan vara ett administratörsanvändarnamn och lösenord för en virtuell Windows-dator. Parametervärdena som används i distributionen när de har angetts.

- **Variabler**: de används för att registrera värden som används i distributionen. Till skillnad från parametrar anges inte ett variabelvärde vid tidpunkten för distribution. Det är svårt kodade eller genereras dynamiskt.

- **Resurser**: det här avsnittet i mallen definierar resurserna som ska distribueras, till exempel virtuella datorer, lagringskonton och virtuella nätverk.

- **Utdata**: när en distribution har slutförts Resource Manager kan returnera data, till exempel dynamiskt skapade anslutningssträngar.

Följande metoder är tillgängliga för distribution:

- **Functions**: du kan använda flera funktioner i Resource Manager-mallar. Dessa omfattar åtgärder som att konvertera en sträng till gemena bokstäver, distribuera flera instanser av en viss resurs och dynamiskt returnerar målresursgruppen. Resource Manager-funktioner hjälpa dig att skapa dynamiska distributioner.

- **Resursberoenden**: när du distribuerar flera resurser, vissa resurser har ett beroende på andra. Du kan använda en beroendedeklaration så att beroende resurser distribueras innan de andra för att underlätta distributionen.

- **Mallen länka**: från inom en Resource Manager-mall, kan du länka till en annan mall. På så sätt kan distributionen uppdelning i en uppsättning riktade mallar för specifika ändamål.

Du kan skapa Resource Manager-mallar i en textredigerare. Men innehåller Azure SDK för Visual Studio verktyg som hjälper dig att. Med hjälp av Visual Studio kan du lägga till resurser i mallen via en guide, och sedan distribuera och felsöka mallen direkt från Visual Studio. Mer information finns i [redigera Azure Resource Manager-mallar](../../resource-group-authoring-templates.md).

Slutligen kan du konvertera befintliga resursgrupper i en återanvändningsbara mallar från Azure-portalen. Det kan vara användbart om du vill skapa en distribuerbar mall med en befintlig resursgrupp, eller om du bara vill undersöka den underliggande JSON. Om du vill exportera en resursgrupp, Välj den **Automationsskript** knappen Inställningar i den resursgruppen.

## <a name="security-of-azure-resources-rbac"></a>Säkerheten i Azure-resurser (RBAC)

Du kan bevilja operativa åtkomst till användarkonton i ett angivet omfång: prenumerationen, resursgruppen eller enskilda resursen. Det innebär att du kan distribuera en uppsättning resurser i en resursgrupp, till exempel en virtuell dator och alla relaterade resurser och ge behörigheter till en specifik användare eller grupp. Den här metoden begränsar åtkomsten till endast de resurser som tillhör målresursgruppen. Du kan också ge åtkomst till en enda resurs, till exempel en virtuell dator eller ett virtuellt nätverk.

Om du vill bevilja åtkomst, tilldela en roll till användaren eller användargruppen. Det finns många fördefinierade roller. Du kan även definiera dina egna anpassade roller.

Här följer några exempel roller är inbyggda i Azure:

- **Ägare**: en användare med den här rollen kan hantera allt, inklusive åtkomst.

- **Läsare**: en användare med den här rollen kan läsa resurser av alla typer (utom hemligheter) men inte göra några ändringar.

- **Virtuell datordeltagare**: en användare med den här rollen kan hantera virtuella datorer men kan inte hantera det virtuella nätverket som de är anslutna eller storage-konto där VHD-filen finns.

- **SQL DB-deltagare**: en användare med den här rollen kan hantera SQL-databaser, men inte deras säkerhetsrelaterade principer.

- **SQL-säkerhetsansvarig**: en användare med den här rollen kan hantera säkerhetsrelaterade principer för SQL-servrar och databaser.

- **Lagringskontodeltagare**: en användare med den här rollen kan hantera lagringskonton, men det går inte att hantera åtkomst till storage-konton.

Mer information finns i [använda rolltilldelningar för att hantera åtkomst till din Azure-prenumerationsresurser](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure Virtual Machines är en av de centrala IaaS-tjänsterna i Azure. Azure-datorer stöder distribution av Windows eller Linux-datorer i ett Microsoft Azure-datacenter. Med Azure virtuella datorer som du har fullständig kontroll över VM-konfigurationen och ansvarar för alla installation, konfiguration och underhåll.

När du distribuerar en Azure-dator, du kan välja en avbildning från Azure Marketplace eller du kan ange egna generaliserad avbildning. Den här bilden används för att tillämpa operativsystemet och inledande konfiguration. Under distributionen hanterar Resource Manager vissa konfigurationsinställningar, till exempel tilldela det datornamn, administrativa autentiseringsuppgifter och nätverkskonfiguration. Du kan använda Azure virtual machine-tillägg för att automatisera konfigurationer, till exempel Programvaruinstallation, antivirus konfiguration och övervakning av lösningar.

Du kan skapa virtuella datorer i många olika storlekar. Storleken på den virtuella datorn avgör allokering, till exempel bearbetning, minne och lagringskapacitet. I vissa fall kan är specifika funktioner, till exempel RDMA-aktiverade nätverkskort och SSD-diskar endast tillgängliga med vissa storlekar för Virtuella datorer. En fullständig lista över funktioner och VM-storlekar finns i ”storlekar för virtuella datorer i Azure” för [Windows](../../virtual-machines/windows/sizes.md) och [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Användningsfall

Eftersom Azure-datorer ger fullständig kontroll över konfiguration, är de perfekta för en mängd olika server-arbetsbelastningar som inte passar i en PaaS-modellen. Server-arbetsbelastningar, till exempel database-servrar (SQL Server, Oracle eller MongoDB), Windows Server Active Directory, Microsoft SharePoint och många mer blir möjligt att köra på Microsoft Azure-plattformen. Om du vill kan flytta du dessa arbetsbelastningar från ett lokalt datacenter till en eller flera Azure-regioner, utan att en stor del av omkonfiguration.

### <a name="deployment-of-virtual-machines"></a>Distribution av virtuella datorer

Du kan distribuera virtuella Azure-datorer med hjälp av Azure portal, med hjälp av automation med Azure PowerShell-modulen eller med hjälp av automation med plattformsoberoende CLI.

#### <a name="portal"></a>Portalen

Distribuera en virtuell dator med hjälp av Azure-portalen kräver en aktiv Azure-prenumeration och åtkomst till en webbläsare. Du kan välja avbildningar för många olika operativsystem med olika konfigurationer. Alla lagrings- och nätverkskrav konfigureras under distributionen. Mer information finns i ”Skapa en virtuell dator i Azure portal” för [Windows](../../virtual-machines/windows/quick-create-portal.md) och [Linux](../../virtual-machines/linux/quick-create-portal.md).

Förutom att distribuera en virtuell dator från Azure-portalen kan distribuera du en Azure Resource Manager-mall från portalen. Detta distribuerar och konfigurerar alla resurser som definierats i mallen. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure-portalen](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Distribuera en Azure virtuell dator med hjälp av PowerShell tillåter fullständig distributionsautomatisering för alla relaterade virtuella resurser, inklusive lagring och nätverk. Mer information finns i [skapa en Windows virtuell dator med Resource Manager och PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Förutom att distribuera Azure-beräkningsresurser individuellt kan använda du Azure PowerShell-modulen för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Kommandoradsgränssnittet (CLI)

Precis som med PowerShell-modulen Azure-kommandoradsgränssnittet ger distributionsautomatisering och kan användas på Windows, OS X eller Linux-datorer. När du använder Azure CLI **vm-Snabbregistrering** kommandot alla relaterade resurser för virtuell dator (inklusive lagring och nätverk) och den virtuella datorn har distribuerats. Mer information finns i [skapa en Linux VM i Azure med hjälp av CLI](../../virtual-machines/linux/quick-create-cli.md).

På samma sätt kan du använda Azure CLI för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Åtkomst och säkerhet för virtuella datorer

Åtkomst till en virtuell dator från Internet kräver det associerade nätverket gränssnitt eller belastningsutjämnare om så är tillämpligt, som ska konfigureras med en offentlig IP-adress. Den offentliga IP-adressen innehåller ett DNS-namn som ska matchas till den virtuella datorn eller belastningsutjämnare. Mer information finns i [IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Du kan hantera åtkomst till den virtuella datorn över offentliga IP-adress med hjälp av en nätverksresurs security nätverkssäkerhetsgrupper (NSG). En NSG fungerar som en brandvägg och tillåter eller nekar trafik över nätverksgränssnitt eller undernät på en uppsättning definierade portar. Till exempel för att skapa en fjärrskrivbordssession med en Azure-dator, måste du konfigurera NSG tillåter inkommande trafik på port 3389. Mer information finns i [öppna portar till en virtuell dator i Azure med Azure portal](../../virtual-machines/windows/nsg-quickstart-portal.md).

Slutligen precis som med hanteringen av alla datorsystem, bör du ge säkerhet för virtuella Azure-datorer i operativsystemet med hjälp av autentiseringsuppgifter och program.

## <a name="azure-storage"></a>Azure Storage

Azure Storage är en Microsoft-hanterad tjänst som tillhandahåller beständig, skalbar och redundant lagring. Du kan lägga till ett Azure storage-konto som en resurs till valfri resursgrupp genom att använda valfri metod för distribution av resursen. Azure innehåller fyra lagringstyper: Blob storage, File Storage, Table storage och Queue storage. När du distribuerar ett lagringskonto, två typer av konton är tillgängliga, generell och blob-lagring. Ett allmänt lagringskonto ger dig tillgång till alla fyra lagringstyper. BLOB storage-konton liknar allmänna konton, men innehåller särskilda BLOB-objekt som innehåller heta och kalla åtkomstnivåer. Mer information om blob-lagring finns i [Azure Blob storage](../../storage/blobs/storage-blob-storage-tiers.md).

Azure storage-konton kan konfigureras med olika nivåer av redundans:

- **Lokalt redundant lagring** ger hög tillgänglighet genom att se till att tre kopior av alla data görs synkront innan en skrivning anses lyckas. Dessa kopior lagras i en anläggning i en enda region. Replikerna lagras i separata feldomäner och uppgraderingsdomäner. Det innebär att data är tillgängliga även om en lagringsnod som håller dina data misslyckas eller tas offline som ska uppdateras.

- **GEO-redundant lagring** gör tre synkrona kopior av data i den primära regionen för hög tillgänglighet, och sedan asynkront gör tre repliker i en parad region för haveriberedskap.

- **Läsåtkomst till geografiskt redundant lagring** är geo-redundant lagring plus möjligheten att läsa data i den sekundära regionen. Den här möjligheten gör den lämplig för partiella katastrofåterställning. Om det finns ett problem med den primära regionen, kan du ändra ditt program har skrivskyddad åtkomst till den parade regionen.

### <a name="use-cases"></a>Användningsfall

Varje lagringstyp av har olika användningsfall.

#### <a name="blob-storage"></a>Blob Storage

Ordet *blob* är en förkortning *binärt stort objekt*. BLOB-lagring är Ostrukturerade filer som de som du lagrar på datorn. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring. Azure Blob storage har också datadiskar för virtuella datorer i Azure.

Azure Storage stöder tre typer av blobbar:

- **Blockblobbar** används för att lagra vanliga filer upp till 195 GB i storlek (4 MB × 50 000 block). I första hand för blockblob-objekt är lagring av filer som läses från början till slut som mediafiler eller bildfiler för webbplatser. De har namngetts blockblobar eftersom filer som är större än 64 MB måste laddas upp som små block. Dessa är sedan konsoliderad (eller allokerat) till sista blob.

- **Sidblob-objekt** används för att lagra direktåtkomst filer på upp till 1 TB i storlek. Sidblobar används främst som datalagret för de virtuella hårddiskar som tillhandahåller beständiga diskar för Azure Virtual Machines, IaaS beräkningstjänst i Azure. De har namngetts sidblobar eftersom de ger slumpmässig läsning/skrivning åtkomst till 512 byte-sidor.

- **Tilläggsblobbar** består av block som blockblobbar, men de är optimerade för tilläggsåtgärder. De används ofta för att logga information från en eller flera källor till samma blob. Du kan till exempel skriva alla dina spårningsloggning till samma tilläggsblobb för ett program som körs på flera virtuella datorer. En enda tilläggsblobb kan vara upp till 195 GB.

Mer information finns i [komma igång med Azure Blob storage med hjälp av .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>File Storage

Azure File storage är en tjänst som erbjuder filresurser i molnet med hjälp av standardprotokoll för Server Message Block (SMB). Tjänsten stöder både SMB 2.1 och SMB 3.0. Du kan migrera program som förlitar sig på filresurser till Azure snabbt och utan kostsamma omskrivningar med Azure File storage. Program som körs på virtuella Azure-datorer kan i molntjänster eller från lokala klienter montera en filresurs i molnet. Detta liknar hur ett skrivbordsprogram monterar en typisk SMB-resurs. Ett obegränsat antal programkomponenter kan sedan montera och komma åt fillagringsresursen samtidigt.

Eftersom en fillagringsresurs är en vanlig SMB-filresurs kan program som körs i Azure kan komma åt data i resursen via filsystemets I/O APIs. Utvecklare kan därför använda befintlig kod och färdigheter för att migrera befintliga program. IT-proffs kan använda PowerShell-cmdletar för att skapa, montera och hantera fillagringsresurser som en del av administrationen av Azure-program.

Mer information finns i [Kom igång med Azure File storage i Windows](../../storage/files/storage-how-to-use-files-windows.md) eller [hur du använder Azure File storage med Linux](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Table Storage

Azure Table Storage är en tjänst som lagrar strukturerade NoSQL-data i molnet. Table storage är en nyckel-och attributdatabas med en design utan schema. Eftersom Table storage är utan schema, är det enkelt att anpassa dina data efter behov av utvecklas. Åtkomsten till data är snabb och kostnadseffektiv för alla typer av program. Kostnaden för Table Storage är normalt sett betydligt lägre än för motsvarande volymer med traditionell SQL.

Du kan använda Table Storage för att lagra flexibla datauppsättningar, till exempel användardata för webbprogram, adressböcker, enhetsinformation och andra typer av metadata som din tjänst kräver. Du kan lagra valfritt antal enheter i en tabell. Ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

Mer information finns i [komma igång med Azure Table storage](../../cosmos-db/table-storage-how-to-use-dotnet.md).

#### <a name="queue-storage"></a>Queue Storage

Azure Queue Storage innehåller molnmeddelandehantering mellan programkomponenter. Utforma program för skalning är programkomponenterna ofta fristående så att de kan skalas oberoende av varandra. Queue Storage är en asynkron meddelandelösning för kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

Mer information finns i [Kom igång med Azure Queue storage](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Distribuera ett lagringskonto

Det finns flera alternativ för att distribuera ett lagringskonto.

#### <a name="portal"></a>Portalen

Distribuera ett lagringskonto med hjälp av Azure-portalen kräver en aktiv Azure-prenumeration och åtkomst till en webbläsare. Du kan distribuera ett nytt lagringskonto i en ny eller befintlig resursgrupp. När du har skapat lagringskontot kan skapa du en blob-behållare eller filresurs med hjälp av portalen. Du kan skapa tabell och kö lagringsentiteter programmässigt. Mer information finns i [skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md).

Förutom att distribuera ett lagringskonto från Azure-portalen kan distribuera du en Azure Resource Manager-mall från portalen. Detta distribuerar och konfigurerar alla resurser som definierats i mallen, inklusive eventuella lagringskonton. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure-portalen](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Distribuera ett Azure storage-konto med hjälp av PowerShell tillåter fullständig distributionsautomatisering för storage-konto. Mer information finns i [med hjälp av Azure PowerShell med Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Förutom att distribuera Azure-resurser individuellt kan använda du Azure PowerShell-modulen för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Kommandoradsgränssnittet (CLI)

Precis som med PowerShell-modulen Azure-kommandoradsgränssnittet ger distributionsautomatisering och kan användas på Windows, OS X eller Linux-datorer. Du kan använda Azure CLI **lagringskonto skapa** kommando för att skapa ett lagringskonto. Mer information finns i [med hjälp av Azure CLI med Azure Storage.](../../storage/common/storage-azure-cli.md)

På samma sätt kan du använda Azure CLI för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Åtkomst och säkerhet för Azure Storage

Azure Storage används på olika sätt, inklusive genom Azure-portalen under Skapa en virtuell dator och driften och från Storage-klientbibliotek.

#### <a name="virtual-machine-disks"></a>Virtuella diskar

När du distribuerar en virtuell dator, måste du också skapa ett lagringskonto för att lagra den virtuella disken för operativsystemet och eventuella ytterligare hårddiskar. Du kan välja ett befintligt lagringskonto eller skapa en ny. Eftersom den maximala storleken för en blob är 1 024 GB, har en enskild VM-disk en maximal storlek på 1,023 GB. Om du vill konfigurera en större datadisk kan du presentera flera datadiskar till den virtuella datorn och lagringspoolen dem tillsammans som en enskild logisk disk. Mer information finns i ”Hantera Azure-diskar” för [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) och [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Storage-verktyg

Azure storage-konton kan nås via många olika lagringsutforskare, till exempel Visual Studio Cloud Explorer. Dessa verktyg kan du bläddra igenom storage-konton och data. Mer information och en lista över tillgängliga lagringsutforskare finns i [Azure Storage-klientverktyg](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>API: et Storage

Storage-resurser kan nås av alla språk som kan göra HTTP/HTTPS-förfrågningar. Dessutom erbjuder Azure Storage programmeringsbibliotek för flera populära språk. Dessa bibliotek förenklar arbetet med Azure Storage genom att hantera information om till exempel synkrona och asynkrona anrop, massbearbetning av åtgärder, hantering av undantag och automatiska nya försök. Mer information finns i [Azure Storage service REST API-referens](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Åtkomstnycklar för lagring

Varje lagringskonto har två autentiseringsnycklar, en primär och en sekundär. Antingen kan användas för åtkomst lagringsåtgärder. Nycklarna lagring som används för att skydda ett lagringskonto och krävs för att komma åt data. Det finns två nycklar så att då och då förnyelse av nycklar till att förbättra säkerheten. Det är viktigt för att skydda nycklar eftersom tillgång till dem, tillsammans med kontonamnet ger obegränsad tillgång till alla data i lagringskontot.

#### <a name="shared-access-signatures"></a>Signaturer för delad åtkomst

Om du vill tillåta användare att ha kontrollerad åtkomst till dina lagringsresurser kan skapa du en signatur för delad åtkomst. En signatur för delad åtkomst är en token som kan läggas till en URL som ger delegerad åtkomst till en lagringsresurs. Alla som har denna token kan komma åt den resurs som den pekar på med behörigheterna som den beviljar, för tidsperioden som den är giltig. Mer information finns i [använda signaturer för delad åtkomst](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network

Virtuella nätverk är nödvändiga för att ha stöd för kommunikation mellan virtuella datorer. Du kan definiera undernät, anpassad IP-adress, DNS-inställningar, säkerhetsfiltrering och belastningsutjämning. Azure har stöd för olika områden: endast molnbaserad nätverk eller hybrid virtuella nätverk. 

### <a name="cloud-only-virtual-networks"></a>Endast molnbaserad virtuella nätverk

Ett Azure-nätverk som standard är bara tillgängliga för resurser som lagras i Azure. Resurser som är anslutna till samma virtuella nätverk kan kommunicera med varandra. Du kan associera virtuella datorers nätverksgränssnitt och belastningsutjämnare med en offentlig IP-adress för att göra den virtuella datorn nås via Internet. Du kan att skydda åtkomsten till de offentliga resurserna med hjälp av en nätverkssäkerhetsgrupp.

![Azure-nätverk för ett webbprogram på nivå 2](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hybrid virtuella nätverk

Du kan ansluta ett lokalt nätverk till ett Azure-nätverk med hjälp av ExpressRoute eller en plats-till-plats VPN-anslutning. I den här konfigurationen är Azure-nätverket i stort sett en molnbaserad förlängning av det lokala nätverket.
![Hybrid-nätverk med VPN](https://docs.microsoft.com/azure/architecture/reference-architectures/_images/blueprints/hybrid-network-vpn.png)

Eftersom Azure-nätverket är anslutet till ditt lokala nätverk, mellan lokala virtuella nätverk måste använda ett unikt delen av adressutrymmet som används i din organisation. På samma sätt som olika företagsplatser har tilldelats ett specifikt IP-undernät, blir Azure en annan plats när du utökar ditt nätverk.
Det finns flera alternativ för att distribuera ett virtuellt nätverk.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Kommandoradsgränssnittet (CLI)](../../virtual-network/quick-create-cli.md)

- Azure Resource Manager-mallar

>**När du ska använda**: när du arbetar med virtuella datorer i Azure kan du arbetar med virtuella nätverk. Det möjliggör segmentera dina virtuella datorer i offentliga och privata undernät liknande lokala datacenter. 

>**Kom igång**: distribuera ett Azure-nätverk med hjälp av Azure-portalen kräver en aktiv Azure-prenumeration och åtkomst till en webbläsare. Du kan distribuera ett nytt virtuellt nätverk i en ny eller befintlig resursgrupp. När du skapar en ny virtuell dator från portalen, kan du välja ett befintligt virtuellt nätverk eller skapa en ny. Kom igång och [skapa ett virtuellt nätverk med Azure portal](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Åtkomst och säkerhet för virtuella nätverk

Du kan hjälpa att säkra Azure-nätverk med hjälp av en nätverkssäkerhetsgrupp. Nätverkssäkerhetsgrupper innehåller en lista över åtkomstkontrollistan (ACL) åtkomstkontrollregler som tillåter eller nekar nätverkstrafik till dina VM-instanser i ett virtuellt nätverk. Du kan associera NSG: er med undernät eller individuella VM-instanser inom det undernätet. När du kopplar en NSG till ett undernät, tillämpas ACL-reglerna på alla VM-instanser i det undernätet. Du kan dessutom ytterligare begränsa trafik till en enskild virtuell dator genom att koppla en NSG direkt med den virtuella datorn. Mer information finns i [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Nästa steg

- [Skapa en Windows VM](/virtual-machines/windows/quick-create-portal.md)
- [Skapa en virtuell Linux-dator](../../virtual-machines/linux/quick-create-portal.md)
