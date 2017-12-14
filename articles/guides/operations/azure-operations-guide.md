---
title: "Kom igång med för Azure IT operatörer | Microsoft Docs"
description: "Komma igång-guide för Azure IT operatörer"
services: 
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: nepeters
ms.openlocfilehash: 4a913e188dd40b0306be375b016b9e8a3739ed72
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="introduction-to-cloud-computing-and-microsoft-azure"></a>Introduktion till molntjänster och Microsoft Azure

Den här guiden innehåller grundläggande begrepp som är relaterade till distribution och hantering av Microsoft Azure-infrastrukturen. Om du är nybörjare på cloud computing- eller Azure, den här guiden hjälper snabbt komma igång med begrepp, distribution och av hanteringsinformation. Många avsnitt i den här guiden beskrivs en åtgärd, till exempel distribuera en virtuell dator och ange sedan en länk för ingående tekniska detaljer.


## <a name="cloud-computing-overview"></a>Cloud Computing-lösningar: översikt

Molnet innehåller ett modernt alternativ till traditionella lokala datacenter. Leverantörer av offentliga molntjänster tillhandahåller och hantera alla IT-infrastruktur och underliggande programvaran för hantering. Dessa leverantörer erbjuder en mängd olika molntjänster. En molnbaserad tjänst kanske i det här fallet en virtuell dator, en webbserver eller molnbaserade databasmotorn. Som en provider kund molnet låna dessa molntjänster som det behövs. Gör det måste konvertera du kapital kostnaden för maskinvara Underhåll till en verksamhetsutgifter. En molnbaserad tjänst har också följande fördelar:

-   Snabb distribution av stora beräknings-miljöer

-   Snabb flyttningen av system som inte längre behövs

-   Enkel distribution av traditionellt komplexa system som belastningsutjämnare

-   Möjlighet att tillhandahålla flexibel beräkningskapacitet och skala vid behov

-   Mer kostnadseffektivt datormiljöer

-   Komma åt från var som helst med en webbaserad portal eller programmässiga automation

-   Molnbaserade tjänster för att uppfylla behoven för de flesta beräknings- och program

Du har fullständig kontroll över maskinvara och programvara som distribueras med lokal infrastruktur. Tidigare ledde detta till maskinvara inköp beslut som fokuserar på att skala upp. Ett exempel är att köpa en server med flera kärnor högsta prestanda behov. Den här infrastrukturen kan tyvärr underutnyttjade utanför ett fönster för begäran. Med Azure, kan du distribuera den infrastruktur som du behöver och justera detta uppåt eller nedåt när som helst. Detta leder till ett fokus på skala ut via distribution av ytterligare datornoderna att uppfylla behovet av prestanda. Skala ut molntjänster är mer kostnadseffektivt än att skala upp via dyr maskinvara.

Microsoft har distribuerat många Azure-datacenter i världen, med mer planerad. Dessutom ökar Microsoft suveräna moln i områden som Kina och Tyskland. Endast de största globala företagen distribuera Datacenter på detta sätt så att Azure gör det lättare för företag i alla storlekar för att distribuera sina tjänster nära sina kunder.

För små företag kan Azure för en startpunkt för låg kostnad, med möjlighet att skala snabbt som behovet av beräkning ökar. Detta förhindrar att en stor direkta investeringen i infrastrukturen och ger flexibilitet för att skapa och ändra arkitekturen system efter behov. Användning av molntjänster passar bra med start tillväxt skala fast och misslyckas fast modell.

Mer information om tillgängliga Azure-regioner finns [Azure-regioner](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-is-classified-into-three-categories-saas-paas-and-iaas"></a>Molntjänster klassificeras i tre kategorier: SaaS och PaaS IaaS.

#### <a name="saas-software-as-a-service"></a>SaaS: Programvara som en tjänst

SaaS är programvara som är på och hanteras centralt. Den bygger vanligtvis på en arkitektur med flera – en version av programmet används för alla kunder. Det går att skala ut till flera instanser säkerställa bästa prestanda på alla platser. SaaS-programvaran licensieras vanligtvis via en månad eller årlig prenumeration.

Microsoft Office 365 är ett bra exempel på en SaaS erbjudande. Prenumeranter betala en avgift per månad eller årlig prenumeration och de får Microsoft Exchange, Microsoft OneDrive och resten av Microsoft Office-paket som en tjänst. Prenumeranter få alltid den senaste versionen och Exchange-servern hanteras. Jämfört med installation och uppgradering av Office varje år, detta är billigare och kräver mindre ansträngning.

#### <a name="paas-platform-as-a-service"></a>PaaS: Plattform som en tjänst 

Med PaaS, kan du distribuera programmet till en miljö som cloud service-leverantören tillhandahåller. Leverantören har alla infrastrukturhanteringen så att du kan fokusera på programutveckling.

Azure tillhandahåller flera PaaS compute erbjudanden, inklusive funktionen Web Apps i Azure App Service och Azure-molntjänster (webb- och arbetsroller roller). I båda fallen kan har utvecklare flera olika sätt att distribuera sina program utan att känna till något om på detaljerna som stöder den. Utvecklare behöver skapa virtuella datorer (VM), använda Remote Desktop Protocol (RDP) för att logga in till var och en eller installera programmet. De bara nådde en knapp (eller Stäng den) och verktyg som tillhandahålls av Microsoft etablera virtuella datorer och distribuerar sedan och installera programmet på dem.

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastruktur som en tjänst

Ett IaaS-moln leverantör kör och hanterar alla fysiska beräkningsresurser och programvaran som krävs för att aktivera datorvirtualisering. En kund för den här tjänsten distribuerar virtuella datorer i dessa värdbaserade datacenter. Även om de virtuella datorerna finns i ett externt datacenter har kontroll över konfiguration och hantering av dem IaaS-användare.

Azure innehåller flera IaaS-lösningar inklusive virtuella datorer, virtuella datorer och relaterade nätverksinfrastrukturen. Virtuella datorer är ett populärt alternativ för att migrera ursprungligen services till Azure eftersom det låter en ”lyfta och flytta” migrering modell. Du kan konfigurera en virtuell dator som den infrastruktur som för närvarande körs dina tjänster i ditt datacenter och migrera sedan programvaran till den nya virtuella datorn. Du kan behöva göra configuration uppdateringar, t.ex URL: er för andra tjänster eller lagring, men du kan migrera många program på detta sätt.

Skaluppsättningar för den virtuella datorn är byggda på Azure Virtual Machines och ger ett enkelt sätt att distribuera kluster med identiska virtuella datorer. Skaluppsättningar för den virtuella datorn stöder också autoskalning så att nya virtuella datorer kan distribueras automatiskt vid behov. Detta gör skalningsuppsättningar i virtuella datorer en perfekt plattform till värden på högre nivå mikrotjänster beräkningskluster, till exempel Azure Service Fabric och Azure Container Service.

## <a name="azure-services"></a>Azure-tjänster

Azure erbjuder många tjänster i dess molnplattform. Dessa tjänster inkluderar följande.

### <a name="compute-services"></a>Beräkningstjänster

Tjänster för värd och kör programmet arbetsbelastning:

-   Virtuella Azure-datorer, Linux- och Windows

-   Apptjänster (Webbappar, Mobilappar, Logikappar, API Apps och funktionen appar)

-   Azure Batch (för storskaliga parallellt och batchjobb beräkning)

-   Azure Service Fabric

-   Azure Container Service

### <a name="data-services"></a>Datatjänster

Tjänster för att lagra och hantera data:

-   Azure-lagring (omfattar Azure Blob, kön, tabell och filen services)

-   Azure SQL Database

-   Azure Cosmos DB

-   Microsoft Azure StorSimple

-   Azure Redis Cache

### <a name="application-services"></a>Programtjänster

Tjänster för att skapa och använda program:

-   Azure Active Directory (AD Azure)

-   Azure Service Bus för att ansluta distribuerade system

-   Azure HDInsight för bearbetning av stordata

-   Azure Schemaläggaren

-   Azure Media Services

### <a name="network-services"></a>Nätverkstjänster

Tjänster för nätverk både i Azure och mellan Azure och lokala datacenter:

-   Azure Virtual Network

-   Azure ExpressRoute

-   Azure-tillhandahållna DNS

-   Azure Traffic Manager

-   Azure Content Delivery Network

Detaljerad dokumentation om Azure-tjänster finns [dokumentation för Azure tjänsten](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Azure viktiga begrepp

### <a name="datacenters-and-regions"></a>Datacenter och regioner


Azure är en global molnplattform som är allmänt tillgänglig i många regioner runtom i världen. När du etablerar en tjänst, program eller virtuell dator i Azure, uppmanas du att välja en region. Den valda regionen representerar ett speciﬁc datacenter där programmet körs. Mer information finns i [Azure-regioner](https://azure.microsoft.com/regions/).

En av beneﬁts med att använda Azure är att du kan distribuera program till olika Datacenter över hela världen. Den region som du väljer kan aﬀect prestanda för ditt program. Det är optimala för att välja en region som ligger närmast mest dina kunder, minska svarstiden i nätverksbegäranden. Du kan också välja en region för att uppfylla juridiska krav för att distribuera din app i vissa länder.

### <a name="azure-portal"></a>Azure Portal


Azure-portalen är ett webbaserat program som kan användas för att skapa, hantera och ta bort Azure-resurser och tjänster. Azure-portalen finns i https://portal.azure.com. Det innehåller en anpassningsbar instrumentpanel och tooling för att hantera Azure-resurser. Det ger också information om fakturering och prenumerationshantering. Mer information finns i [översikt för Microsoft Azure portal](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) och [hantera Azure-resurser via portalen](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Resurser

Azure-resurser är enskilda beräkning, nätverk, data eller app som är värdar för tjänster som har distribuerats till en Azure-prenumeration. Vissa vanliga resurser är virtuella datorer, lagringskonton eller SQL-databaser. Azure-tjänster består ofta av flera relaterade Azure-resurser. En virtuell Azure-dator kan exempelvis innehålla en VM, storage-konto, nätverkskort och offentliga IP-adressen. Dessa resourcres kan skapas, hanteras och tas bort separat eller som en grupp. Azure-resurser beskrivs i detalj längre fram i den här guiden.

### <a name="resource-groups"></a>Resursgrupper

En Azure-resursgrupp är en behållare som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller bara de resurser som du vill hantera som en grupp. Azure-resursgrupper beskrivs i detalj längre fram i den här guiden.

### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

En Azure Resource Manager-mallen är en JavaScript Object Notation (JSON)-fil som definierar en eller flera resurser som ska distribueras till en resursgrupp. Den definierar även beroenden mellan distribuerade resurser. Resource Manager-mallar beskrivs i detalj längre fram i den här guiden.

### <a name="automation"></a>Automation


Förutom att skapa, hantera och ta bort resurser med hjälp av Azure portal, kan du automatisera dessa aktiviteter med hjälp av PowerShell eller Azure-kommandoradsgränssnittet (CLI).

**Azure PowerShell**

Azure PowerShell är en uppsättning moduler med cmdletar för att hantera Azure. Du kan använda cmdlets för att skapa, hantera och ta bort Azure-tjänster. Cmdlets kan hjälpa dig att uppnå konsekvent, repeterbara och helautomatisk distributioner. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

**Azure-kommandoradsgränssnittet**

Azure-kommandoradsgränssnittet är ett verktyg som du kan använda för att skapa, hantera och ta bort Azure-resurser från kommandoraden. Azure CLI är tillgänglig för Linux, Mac OS X och Windows. Mer information och tekniska detaljer finns [installerar Azure CLI](/cli/azure/install-azure-cli.md).

**REST API: erna** Azure bygger på en uppsättning REST API: er som har stöd för Azure-portalen Användargränssnittet. De flesta av dessa REST-API: er stöds även om du vill kan du etablera och hantera dina Azure-resurser och appar från valfri Internet-aktiverad enhet programmässigt. Mer information finns i [Azure REST SDK referens](https://docs.microsoft.com/rest/api/index).


## <a name="azure-subscriptions"></a>Azure-prenumerationer


En prenumeration är en logisk gruppering av Azure-tjänster som är kopplad till ett Azure-konto. Ett enskilt Azure-konto kan innehålla flera prenumerationer. Fakturering för Azure-tjänster görs på grundval av per prenumeration. Azure-prenumerationer har ett kontoadministratör som har fullständig kontroll över prenumerationen och tjänstadministratör som har kontroll över alla tjänster i prenumerationen. Förutom administratörer, enskilda konton kan få detaljerad kontroll över Azure-resurser via RBAC.

### <a name="select-and-enable-an-azure-subscription"></a>Markera och aktivera en Azure-prenumeration

Innan du kan arbeta med Azure-tjänster, behöver du en prenumeration. Det finns flera prenumerationstyper.

**Kostnadsfria konton**: länken om du vill registrera dig för ett kostnadsfritt konto är på den [Azure-webbplatsen](https://azure.microsoft.com/). Detta ger dig ett kreditkort under 30 dagar försöka valfri kombination av resurser i Azure. Om du överskrider din kredit, har ditt konto pausats. I slutet av utvärderingsperioden kan dina tjänster är inaktiverade och inte längre att fungera. Du kan uppgradera till en prenumeration med användningsbaserad betalning när som helst.

**MSDN-prenumerationer**: Om du har en MSDN-prenumeration kan du hämta ett specifikt belopp i Azure-kredit varje månad. Till exempel om du har en Microsoft Visual Studio Enterprise med MSDN-prenumeration kan du hämta \$150 per månad i Azure-kredit.

Om du överskrider kreditbelopp tjänsten inaktiveras tills nästa månad startar. Du kan inaktivera utgiftsgränsen och lägga till ett kreditkort som ska användas för ytterligare kostnader. Vissa av dessa kostnader rabatterad för MSDN-konton. Till exempel du betalar Linux pris för virtuella datorer som kör Windows Server och det finns utan extra kostnad för Microsoft-servrar, till exempel Microsoft SQL Server. Detta gör att MSDN-konton är idealiskt för scenarier för utveckling och testning.

**BizSpark konton**: det Microsoft BizSpark programmet ger många fördelar för nystartade företag. En av dessa fördelar är åtkomst till alla Microsoft-programvara för utvecklings- och testmiljöer för upp till fem MSDN-konton. Du får 150 USD i Azure-kredit för var och en av de fem MSDN-kontona och du betalar nedsatta för flera Azure-tjänster, till exempel virtuella datorer.

**Betala per användning**: med den här prenumerationen du betalar du använder genom att ett kreditkort eller bankkort till kontot. Om du är en organisation kan du också godkännas för fakturering.

**Enterprise-avtal**: med ett enterprise-avtal som du gör med ett visst antal tjänster i Azure under nästa år och du betalar beloppet i förväg. Det åtagande som du gör används under året. Om du överskrider beloppet betalar du överförbrukning i resterande. Beroende på mängden åtagande få rabatt på tjänster i Azure.



### <a name="grant-administrative-access-to-an-azure-subscription"></a>Tilldela administrativ åtkomst till en Azure-prenumeration


Flera administratörsroller för kontot som är tillgängliga och kan ändras när som helst. Det finns två viktiga roller:

-   **Tjänstadministratör**: den här rollen har behörighet att hantera Azure-tjänster. Som standard beviljas åtkomst till samma konto som kontoadministratör.

-   **Medadministratör**: den här rollen har samma åtkomst som tjänstadministratören. Den här rollen kan inte ändra associationen mellan en prenumeration på Azure kataloger.

Mer information finns i [lägga till eller ändra Azure-administratörsroller](../../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Visa faktureringsinformation i Azure-portalen


En viktig komponent inom med Azure är möjligheten att visa faktureringsinformation. Azure-portalen ger detaljerad inblick i Azure faktureringsinformation.

Mer information finns i [hämta ditt Azure billing faktura och dagligen användningsdata](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Hämta faktureringsinformation från fakturering API: er


Förutom att visa faktureringen i portalen kan du komma åt faktureringsinformation genom att använda ett skript eller ett program via Azure-API: erna för fakturering REST:

-   Du kan använda Azure API för användning för att hämta användningsdata. Du kan finjustera faktureringsinformation för användning av taggning relaterade Azure-resurser. Du kan till exempel tagga varje resurs i en resursgrupp med en avdelningsnamn eller projektnamn och spåra kostnader för att en tagg.

-   Du kan använda Azure hastighet kort API för att lista alla tillgängliga resurser tillsammans med metadata och prisinformation om var och en av dessa resurser.

Mer information finns i [få insikter om dina Microsoft Azure-resursförbrukning](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognosen kostnad med prisnivå Kalkylatorn

Priser för varje tjänst i Azure är olika. Många tillhandahåller Azure nivåerna Basic, Standard och Premium. Varje nivå har vanligtvis flera nivåer för pris och prestanda. Med hjälp av den [online prisnivå Kalkylatorn](http://azure.microsoft.com/pricing/calculator), du kan skapa prisnivå uppskattningar. Kalkylatorn innehåller flexibilitet för att beräkna kostnaden på en enskild resurs eller en grupp av resurser.

### <a name="set-up-billing-alerts"></a>Ställa in faktureringsvarningar

När du har distribuerat programmet eller lösningen på Azure, kan du skapa aviseringar som skickar du e-post när metoden utgiftsgränser som definierats i aviseringen. Mer information finns i [konfigurera fakturering aviseringar för Microsoft Azure-prenumerationer](../../billing/billing-set-up-alerts.md).

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager är en distribution, hantering och organisation mekanism för Azure-resurser. Med hjälp av hanteraren för filserverresurser, kan du skapa många enskilda resurser i en resursgrupp.

Resource Manager innehåller också funktioner för distribution som gör det möjligt att anpassa distributionen och konfigurationen av relaterade resurser. Du kan exempelvis distribuera ett program som består av flera virtuella datorer, en belastningsutjämnare och en SQL-databas som en enhet med hjälp av hanteraren för filserverresurser. Du kan utveckla dessa distributioner med hjälp av en Resource Manager-mall.

Resource Manager har flera fördelar:

-   Du kan distribuera, hantera och övervaka alla resurserna för din lösning som en grupp i stället för att hantera resurserna separat.

-   Du kan upprepade gånger distribuera lösningen genom utvecklingslivscykeln och vara säker på att dina resurser distribueras i ett konsekvent tillstånd.

-   Du kan hantera infrastrukturen med hjälp av deklarativa mallar i stället för skript.

-   Du kan definiera beroenden mellan resurser så att de distribueras i rätt ordning.

-   Du kan använda åtkomstkontroll för alla tjänster i resursgruppen eftersom RBAC är inbyggt i hanteringsplattformen.

-   Du kan använda taggar för resurser och organisera dem logiskt alla resurser i din prenumeration.

-   Du kan tydliggöra fakturering för din organisation genom att visa kostnaderna för en grupp med resurser som delar samma tagg.

### <a name="tips-for-creating-resource-groups"></a>Tips för att skapa resursgrupper


Tänk dessa tips när du gör beslut om din resursgrupper:

-   Alla resurser i en resursgrupp ska ha samma livscykel.

-   Du kan tilldela en resurs bara en grupp i taget.

-   Du kan lägga till eller ta bort en resurs från en resursgrupp när som helst. Alla resurser måste tillhöra en resursgrupp. Så om du tar bort en resurs från en grupp måste du lägga till den till en annan.

-   Du kan flytta de flesta typer av resurser till en annan resursgrupp när som helst.

-   Resurser i en resursgrupp kan finnas i olika regioner.

-   Du kan använda en resursgrupp för åtkomstkontroll för resurser i den.

### <a name="building-resource-manager-templates"></a>Skapa Resource Manager-mallar

Resource Manager-mallar definiera deklarativt resurser och resurskonfigurationer som ska distribueras till en enskild resursgrupp. Du kan använda Resource Manager-mallar för att samordna komplexa distributioner utan att behöva överdriven skript eller manuell konfiguration. När du utvecklar en mall kan du distribuera den flera gånger, varje gång med identiska resultat.

Resource Manager-mallen består av fyra delar:

-   **Parametrarna**: dessa är indata för distributionen. Värden kan anges av en person eller en automatiserad process. En exempel-parameter kan vara en administratör användarnamn och lösenord för en virtuell Windows-dator. Parametervärdena som används i distributionen när de har angetts.

-   **Variabler**: de används för att registrera värden som används i distributionen. Till skillnad från parametrar anges inte ett variabelvärde vid tidpunkten för distribution. I stället är det svårt kodade eller genereras dynamiskt.

-   **Resurser**: det här avsnittet av mallen definierar resurserna som ska distribueras som virtuella datorer, lagringskonton och virtuella nätverk.

-   **Utdata**: när en distribution har slutförts Resource Manager kan returnera data, till exempel dynamiskt genererad anslutningssträngar.

Följande metoder är tillgängliga för automatisering av distribution:

-   **Funktioner**: du kan använda flera funktioner i Resource Manager-mallar. Dessa inkluderar åtgärder som till exempel konverterar en sträng till gemener distribuera flera instanser av en viss resurs och dynamiskt returnerar målresursgruppen. Hanteraren för filserverresurser funktioner hjälpa dig att skapa dynamiska distributioner.

-   **Resursberoenden**: när du distribuerar flera resurser, vissa resurser har ett beroende på andra. För att underlätta distributionen måste använda du en beroende-deklaration så att beroende resurser har distribuerats innan den andra.

-   **Mallen länka**: från inom en Resource Manager-mall kan du länka till en annan mall. Detta gör att distributionen uppdelning till en uppsättning riktade mallar för specifika ändamål.

Du kan skapa Resource Manager-mallar i en textredigerare. Dock innehåller Azure SDK för Visual Studio verktyg som hjälper dig att. Med hjälp av Visual Studio kan du lägga till resurser i mallen via en guide och sedan distribuera och felsöka mallen direkt från Visual Studio. Mer information finns i [redigera Azure Resource Manager-mallar](../../resource-group-authoring-templates.md).

Slutligen kan du konvertera befintliga resursgrupper till en återanvändbara mall från Azure-portalen. Detta kan vara användbart om du vill skapa en mall som distribueras av en befintlig resursgrupp, eller om du vill granska underliggande JSON. Om du vill exportera en resursgrupp, Välj den **Automatiseringsskriptet** knappen från inställningarna för den resursgrupp.

## <a name="security-of-azure-resources-rbac"></a>Säkerheten för Azure-resurser (RBAC)

Du kan bevilja operativa åtkomst till användarkonton i ett angivet omfång: prenumeration, resursgrupp eller enskild resurs. Det innebär att du kan distribuera en uppsättning resurser i en resursgrupp, till exempel en virtuell dator och alla relaterade resurser och ge behörighet till en specifik användare eller grupp. Den här metoden begränsar åtkomst till resurser som hör till målresursgruppen. Du kan också ge åtkomst till en enskild resurs, till exempel en virtuell dator eller ett virtuellt nätverk.

För att bevilja åtkomst, tilldela en roll till användaren eller användargruppen. Det finns många fördefinierade roller. Du kan också definiera dina egna anpassade roller.

Här följer några exempel roller som är inbyggda i Azure:

-   **Ägare**: en användare med den här rollen kan hantera allt, inklusive åtkomst.

-   **Läsaren**: en användare med den här rollen kan läsa resurser av alla typer (utom hemligheter) men det går inte att göra ändringar.

-   **Virtual machine-deltagare**: en användare med den här rollen kan hantera virtuella datorer men kan inte hantera det virtuella nätverket som som de är anslutna eller det lagringskonto där VHD-filen finns.

-   **SQL DB-deltagare**: en användare med den här rollen kan hantera SQL-databaser, men inte deras säkerhetsrelaterade principer.

-   **SQL-säkerhetsansvarig**: en användare med den här rollen kan hantera de säkerhetsrelaterade principerna för SQL-servrar och databaser.

-   **Storage-konto deltagare**: en användare med den här rollen kan hantera storage-konton, men det går inte att hantera åtkomst till storage-konton.

Mer information finns i [använda rolltilldelningar för att hantera åtkomst till resurserna i Azure-prenumeration](../../active-directory/role-based-access-control-configure.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Virtuella datorer i Azure är en central IaaS-tjänster i Azure. Virtuella Azure-datorer stöder distribution av Windows eller Linux virtuella datorer i ett Microsoft Azure-datacenter. Med Azure Virtual Machines har fullständig kontroll över VM-konfiguration och ansvarar för alla installation, konfiguration och underhåll.

När du distribuerar en Azure VM, kan du välja en bild från Azure Marketplace eller du kan ange egna generaliserad avbildning. Den här avbildningen för att tillämpa operativsystemet och den inledande konfigurationen. Under distributionen av hanterar Resource Manager vissa konfigurationsinställningar, till exempel tilldela datornamn, administrativa autentiseringsuppgifter och nätverkskonfiguration. Du kan använda Azure virtuella datortillägg för att automatisera konfigurationer som Programvaruinstallation, antivirus konfiguration och övervakning av lösningar.

Du kan skapa virtuella datorer på många olika storlekar. Storleken på den virtuella datorn avgör resursallokering, till exempel bearbetning, minne och lagringsutrymme kapacitet. I vissa fall är specifika funktioner, till exempel RDMA-aktiverade nätverkskort och SSD-diskar endast tillgängliga med vissa VM-storlekar. En fullständig lista över VM-storlekar och funktionerna finns i ”storlekar för virtuella datorer i Azure” [Windows](../../virtual-machines/windows/sizes.md) och [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Användningsfall

Eftersom virtuella datorer i Azure erbjuder fullständig kontroll över konfiguration, lämpar de sig för en mängd olika server-arbetsbelastningar som inte får plats i en PaaS-modell. Server-arbetsbelastningar, till exempel databasservrar (SQL Server, Oracle eller MongoDB), Windows Server Active Directory, Microsoft SharePoint och många mer blir möjligt att köra på Microsoft Azure-plattformen. Om du vill kan flytta du dessa arbetsbelastningar från ett lokalt datacenter till en eller flera Azure-regioner, utan en stor mängd omkonfiguration.

### <a name="deployment-of-virtual-machines"></a>Distribution av virtuella datorer

Du kan distribuera virtuella Azure-datorer med hjälp av Azure portal, med hjälp av automation med Azure PowerShell-modulen eller med hjälp av automation med plattformsoberoende CLI.

**Portal**

Distribuera en virtuell dator med hjälp av Azure portal kräver en aktiv Azure-prenumeration och åtkomst till en webbläsare. Du kan välja många olika operativsystemsavbildningar med olika konfigurationer. Alla lagrings- och nätverkskrav konfigureras under distributionen. Mer information finns i ”Skapa en virtuell dator i Azure portal” [Windows](../../virtual-machines/windows/quick-create-portal.md) och [Linux](../../virtual-machines/linux/quick-create-portal.md).

Förutom att distribuera en virtuell dator från Azure-portalen kan distribuera du en Azure Resource Manager-mall från portalen. Distribuerar och konfigurerar då alla resurser som har definierats i mallen. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure-portalen](../../azure-resource-manager/resource-group-template-deploy-portal.md).


**PowerShell**

Distribuera en virtuell Azure-dator med hjälp av PowerShell tillåter fullständig distribution automatisering av alla relaterade virtuella resurser, inklusive lagring och nätverk. Mer information finns i [skapa en virtuell Windows-dator med Resource Manager och PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Förutom att distribuera Azure-beräkningsresurser individuellt kan använda du Azure PowerShell-modulen för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Kommandoradsgränssnittet (CLI)**

Precis som med PowerShell-modulen kommandoradsgränssnittet i Azure tillhandahåller automatiserad distribution och kan användas för Windows-, OS X- eller Linux-datorer. När du använder Azure CLI **vm-Snabbregistrering** kommandot alla relaterade virtuella datorresurser (inklusive lagring och nätverk) och den virtuella datorn själva distribueras. Mer information finns i [skapa en Linux-VM i Azure med hjälp av CLI](../../virtual-machines/linux/quick-create-cli.md).

På samma sätt kan du använda Azure CLI för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Åtkomst och säkerhet för virtuella datorer

Åtkomst till en virtuell dator från Internet kräver det associerade nätverket gränssnitt eller belastningsutjämnare för konfigureras med en offentlig IP-adress. Den offentliga IP-adressen innehåller ett DNS-namn som ska motsvara den virtuella datorn eller belastningsutjämning. Mer information finns i [IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Du kan hantera åtkomst till den virtuella datorn via den offentliga IP-adressen med hjälp av en nätverksresurs security group (NSG). En NSG fungerar som en brandvägg och tillåter eller nekar trafik över nätverksgränssnitt eller undernät på en uppsättning definierade portar. Till exempel för att skapa en fjärrskrivbordssession med en Azure VM, måste du konfigurera NSG: N för att tillåta inkommande trafik på port 3389. Mer information finns i [öppna portar till en virtuell dator i Azure med hjälp av Azure portal](../../virtual-machines/windows/nsg-quickstart-portal.md).

Slutligen, precis som med hanteringen av alla datorsystem, bör du ge säkerhet för en virtuell Azure-dator med operativsystemet genom att använda säkerhetsreferenser och program.

## <a name="azure-storage"></a>Azure Storage

Azure Storage är en Microsoft-hanterad tjänst som tillhandahåller beständiga, skalbara och redundant lagring. Du kan lägga till ett Azure storage-konto som en resurs i en resursgrupp med hjälp av en distributionsmetod för resursen. Azure innehåller fyra lagringstyper: Blob storage, File Storage, Table storage och Queue storage. När du distribuerar ett lagringskonto kontotyperna är tillgängliga, generell och blob storage. Ett allmänt lagringskonto ger dig tillgång till alla fyra lagringstyper av. BLOB storage-konton liknar allmänna konton, men innehåller särskilda blobbar som innehåller varm eller kall åtkomstnivåer. Mer information om blob-lagring finns [Azure Blob storage](../../storage/blobs/storage-blob-storage-tiers.md).

Azure storage-konton kan konfigureras med olika nivåer av redundans:

-   **Lokalt redundant lagring** ger hög tillgänglighet genom att säkerställa att tre kopior av alla data görs synkront innan en skrivning bedöms lyckas. Dessa kopior som lagras i en anläggning i en enskild region. Replikerna lagras i separata feldomäner och uppgradera domäner. Det innebär att data är tillgängliga även om en lagringsnod som innehåller data misslyckas eller tas offline som ska uppdateras.

-   **GEO-redundant lagring** gör tre synkrona kopior av data i den primära regionen för hög tillgänglighet, och sedan asynkront gör tre repliker i en parad region för katastrofåterställning.

-   **Geo-redundant lagring med läsbehörighet** är geo-redundant lagring plus möjlighet att läsa data i den sekundära regionen. Den här möjligheten gör den lämplig för partiellt katastrofåterställning. Om det finns ett problem med den primära regionen, kan du ändra ditt program har skrivskyddad åtkomst till parad region.

### <a name="use-cases"></a>Användningsfall 

Varje lagringstyp har olika användningsfall.

**Blob Storage** 

Ordet *blob* är en förkortning *binärt stort objekt*. Blobbar är Ostrukturerade filer som de som du lagrar på datorn. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring. Azure Blob storage innehåller också datadiskar för virtuella datorer i Azure.

Azure Storage stöder tre typer av blobbar:

-   **Blockblobbar** används för att lagra normala filer upp till 195 GB i storlek (4 MB × 50 000 block). Det primära användningsfallet för blockblobbar är lagring av filer som läses från början till slut som mediafiler eller bildfiler för webbplatser. De har namngetts blockblobbar eftersom filer som är större än 64 MB måste överföras som mindre block. Dessa är sedan konsoliderade (eller allokerat) till slutlig blob.

-   **Sidblobbar** används för att lagra direktåtkomst filer upp till 1 TB i storlek. Sidblobbar används främst som datalagret för de virtuella hårddiskar som tillhandahåller beständiga diskar för virtuella datorer i Azure, compute IaaS-tjänst i Azure. De har namngetts sidblobbar eftersom ger slumpmässig läsning/skrivning till 512 byte-sidor.

-   **Tilläggsblobbar** består av block som blockblobbar, men de är optimerade för tilläggsåtgärder. Dessa används ofta för att logga information från en eller flera källor till samma blob. Du kan till exempel skriva alla dina spårningsloggning till samma tilläggsblobb för ett program som körs på flera virtuella datorer. En enda tilläggsblobb kan vara upp till 195 GB.

Mer information finns i [komma igång med Azure Blob storage med hjälp av .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

**Fillagring**

Azure File storage är en tjänst som erbjuder filresurser i molnet med hjälp av standardprotokoll för Server Message Block (SMB). Tjänsten stöder både SMB 2.1 och SMB 3.0. Du kan migrera program som förlitar sig på filresurser till Azure snabbt och utan kostsamma omskrivningar med Azure File storage. Program som körs på virtuella Azure-datorer kan i molntjänster eller från lokala klienter montera en filresurs i molnet. Detta liknar hur ett skrivbordsprogram monterar en typisk SMB-resurs. Ett obegränsat antal programkomponenter kan sedan montera och komma åt fillagringsresursen samtidigt.

Eftersom en File storage-resurs är en vanlig SMB-filresurs kan program som körs i Azure kan komma åt data i resursen via filsystemet I/O APIs. Utvecklare kan därför använda befintlig kod och kunskaper för att migrera befintliga program. IT-proffs kan använda PowerShell-cmdlets för att skapa, montera och hantera fillagringsresurser som en del av administrationen av Azure-program.

Mer information finns i [Kom igång med Azure File storage i Windows](../../storage/files/storage-how-to-use-files-windows.md) eller [använda Azure File storage med Linux](../../storage/files/storage-how-to-use-files-linux.md).

**Table Storage**

Azure Table Storage är en tjänst som lagrar strukturerade NoSQL-data i molnet. Table storage är en nyckel-och attributdatabas med en mindre schema-design. Eftersom Table storage är schema-mindre, är det enkelt att anpassa dina data som behov utvecklas. Åtkomsten till data är snabb och kostnadseffektiv för alla typer av program. Kostnaden för Table Storage är normalt sett betydligt lägre än för motsvarande volymer med traditionell SQL.

Du kan använda Table Storage för att lagra flexibla datauppsättningar, till exempel användardata för webbprogram, adressböcker, enhetsinformation och andra typer av metadata som din tjänst kräver. Du kan lagra valfritt antal enheter i en tabell. Ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

Mer information finns i [komma igång med Azure Table storage](../../cosmos-db/table-storage-how-to-use-dotnet.md).

**Queue Storage**

Azure Queue Storage innehåller molnmeddelandehantering mellan programkomponenter. När program utformas för skalning, är programkomponenterna ofta fristående så att de kan skalas oberoende av varandra. Queue Storage är en asynkron meddelandelösning för kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

Mer information finns i [komma igång med Azure Queue storage](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Distribuera ett lagringskonto

Det finns flera alternativ för att distribuera ett lagringskonto.

**Portal**

Distribuera ett lagringskonto med hjälp av Azure portal kräver en aktiv Azure-prenumeration och åtkomst till en webbläsare. Du kan distribuera ett nytt lagringskonto i en ny eller befintlig resursgrupp. När du har skapat lagringskontot kan skapa du en blob-behållare eller filresurs med hjälp av portalen. Du kan skapa tabell och kö lagring entiteter programmässigt. Mer information finns i [skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

Förutom att distribuera ett lagringskonto från Azure-portalen kan distribuera du en Azure Resource Manager-mall från portalen. Distribuerar och konfigurerar då alla resurser som har definierats i mallen, inklusive alla lagringskonton. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure-portalen](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**PowerShell**

Distribuera ett Azure storage-konto med hjälp av PowerShell tillåter fullständig distribution automatisering av lagringskontot. Mer information finns i [med hjälp av Azure PowerShell med Azure Storage](../../storage/common/storage-powershell-guide-full.md).

Förutom att distribuera Azure-resurser individuellt kan använda du Azure PowerShell-modulen för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Kommandoradsgränssnittet (CLI)**

Precis som med PowerShell-modulen kommandoradsgränssnittet i Azure tillhandahåller automatiserad distribution och kan användas för Windows-, OS X- eller Linux-datorer. Du kan använda Azure CLI **lagringskonto skapa** kommando för att skapa ett lagringskonto. Mer information finns i [med hjälp av Azure CLI med Azure Storage.](../../storage/common/storage-azure-cli.md)

På samma sätt kan du använda Azure CLI för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Åtkomst och säkerhet för Azure Storage

Azure Storage finns på olika sätt, inklusive under Skapa en virtuell dator och åtgärden och från lagringsklientbiblioteken om Azure-portalen. 

**Virtuella diskar**

När du distribuerar en virtuell dator, måste du också skapa ett lagringskonto för den virtuella disken för operativsystemet och eventuella ytterligare hårddiskar. Du kan välja ett befintligt lagringskonto eller skapa en ny. Eftersom den maximala storleken för en blobb är 1 024 GB, har en maximal storlek på 1,023 GB i en virtuell disk. Om du vill konfigurera en större datadisk du presentera flera datadiskar i till den virtuella datorn och poolen tillsammans som en enskild logisk disk. Mer information finns i ”Hantera Azure-diskar” för [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) och [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

**Storage-verktyg**

Azure storage-konton kan nås via många olika lagringsutforskare, till exempel för Visual Studio Cloud Explorer. Dessa verktyg kan du bläddra igenom storage-konton och data. Mer information och en lista över tillgängliga lagringsutforskare finns [Azure Storage-klientverktyg](../../storage/common/storage-explorers.md).

**Storage-API**

Storage-resurser kan nås av alla språk som kan göra HTTP/HTTPS-förfrågningar. Dessutom erbjuder Azure Storage programmeringsbibliotek för flera populära språk. Dessa bibliotek förenklar arbeta med Azure Storage genom att hantera information, till exempel synkrona och asynkrona anrop, massbearbetning av åtgärder, hantering av undantag och automatiska omförsök. Mer information finns i [REST API-referens för Azure Storage service](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

**Åtkomstnycklar för lagring**

Varje lagringskonto har två autentiseringsnycklar, en primär och en sekundär. Antingen kan användas för åtkomst lagringsåtgärder. Nycklarna lagring används för att skydda ett lagringskonto och krävs för att komma åt data. Det finns två nycklar för att tillåta tillfällig förnyelsen av för att förbättra säkerheten. Det är viktigt att skydda nycklar eftersom innehar tillsammans med namnet på kontot tillåter obegränsad tillgång till data i lagringskontot.

**Signaturer för delad åtkomst**

Om du vill tillåta användare att ha kontrollerad åtkomst till dina lagringsresurser kan du skapa en signatur för delad åtkomst. En signatur för delad åtkomst är en token som kan läggas till en URL som ger delegerad åtkomst till en lagringsresurs. Alla som har denna token kan komma åt den resurs som pekar på behörigheter som anger den för tidsperioden som den giltiga. Mer information finns i [använder signaturer för delad åtkomst](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network


Virtuella nätverken är nödvändigt för att stödja kommunikation mellan virtuella datorer. Du kan definiera undernät, anpassad IP-adress, DNS-inställningar, säkerhetsfiltrering och belastningsutjämning. Med en VPN-gateway eller en ExpressRoute-krets, kan du ansluta virtuella Azure-nätverk till ditt lokala nätverk.

### <a name="use-cases"></a>Användningsfall

Det finns olika användningsfall för Azure-nätverk.

**Endast molnbaserad virtuella nätverk**

Azure-nätverk, som standard är bara tillgängliga för resurser som lagras i Azure. Resurser som är anslutna till samma virtuella nätverk kan kommunicera med varandra. Du kan associera nätverksgränssnitt för virtuella datorer och belastningsutjämnare med en offentlig IP-adress så att den virtuella datorn tillgänglig via Internet. Du kan ge säker åtkomst till offentligt exponerade resurser med hjälp av en nätverkssäkerhetsgrupp.

**Anslutningar mellan lokala virtuella nätverk**

Du kan ansluta till ett lokalt nätverk till Azure-nätverk med hjälp av ExpressRoute eller en plats-till-plats VPN-anslutning. I den här konfigurationen är virtuella Azure-nätverket i stort sett en molnbaserad tillägg till ditt lokala nätverk.

Eftersom virtuella Azure-nätverket är anslutet till det lokala nätverket mellan lokala virtuella nätverk måste använda en unik delen av adressutrymmet som används i din organisation. På samma sätt som andra företagets platser tilldelas ett specifikt IP-undernät, blir Azure en annan plats som du utökar ditt nätverk.

###<a name="deploying-a-virtual-network"></a>Distribuera ett virtuellt nätverk

Det finns flera alternativ för att distribuera ett virtuellt nätverk.

**Portal**

Distribuera ett virtuellt Azure-nätverk med hjälp av Azure portal kräver en aktiv Azure-prenumeration och åtkomst till en webbläsare. Du kan distribuera ett nytt virtuellt nätverk i en ny eller befintlig resursgrupp. När du skapar en ny virtuell dator från portalen kan du välja ett befintligt virtuellt nätverk eller skapa en ny. Mer information finns i [skapa ett virtuellt nätverk med Azure-portalen](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

Förutom att distribuera Azure-nätverk från Azure-portalen kan distribuera du en Azure Resource Manager-mall från portalen. Distribuerar och konfigurerar då alla resurser som har definierats i mallen, inklusive några resurser för virtuellt nätverk. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure-portalen](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**PowerShell**

Distribuera ett virtuellt Azure-nätverk med hjälp av PowerShell tillåter fullständig distribution automatisering av lagringskontot. Mer information finns i [skapa ett virtuellt nätverk med hjälp av PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md).

Förutom att distribuera Azure-resurser individuellt kan använda du Azure PowerShell-modulen för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Kommandoradsgränssnittet (CLI)**

Precis som med PowerShell-modulen kommandoradsgränssnittet i Azure tillhandahåller automatiserad distribution och kan användas för Windows-, OS X- eller Linux-datorer. Du kan använda Azure CLI **network vnet skapa** kommando för att skapa ett virtuellt nätverk. Mer information finns i [skapa ett virtuellt nätverk med hjälp av Azure CLI](../../virtual-network/virtual-networks-create-vnet-arm-cli.md).

På samma sätt kan du använda Azure CLI för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-networks"></a>Åtkomst och säkerhet för virtuella nätverk

Du kan hjälpa säker virtuella Azure-nätverk med hjälp av en nätverkssäkerhetsgrupp. NSG: er innehåller en lista över åtkomstkontrollistan (ACL) regler för åtkomstkontroll som tillåter eller nekar nätverkstrafik till VM-instanser i ett virtuellt nätverk. Du kan koppla NSG: er till undernät eller individuella VM-instanser inom det undernätet. När du kopplar en NSG till ett undernät gäller ACL-regler för alla VM-instanser i det undernätet. Du kan dessutom ytterligare begränsa trafik till en enskild VM genom att koppla en NSG direkt med den virtuella datorn. Mer information finns i [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](../../virtual-network/virtual-networks-nsg.md).

## <a name="next-steps"></a>Nästa steg

- [Skapa en Windows VM](/virtual-machines/windows/quick-create-portal.md)
- [Skapa en virtuell Linux-dator](../../virtual-machines/linux/quick-create-portal.md)
