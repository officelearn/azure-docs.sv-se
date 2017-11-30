---
title: "Få igång-guide för utvecklare i Azure | Microsoft Docs"
description: "Det här avsnittet innehåller viktig information för utvecklare som vill komma igång med Microsoft Azure-plattformen för deras utvecklingsbehov."
services: 
cloud: 
documentationcenter: 
author: ggailey777
manager: erikre
ms.assetid: 
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: 37942b7aa2fa675e316f368e8c74cb2081ba4648
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="get-started-guide-for-azure-developers"></a>Introduktionsguide för Azure-utvecklare

## <a name="what-is-azure"></a>Vad är Azure?

Azure är en komplett molnplattform som kan vara värd för dina befintliga program, förenkla utvecklingen av nya program och även förbättra lokala program. Azure kan integreras med molntjänster som du behöver för att utveckla, testa, distribuera och hantera dina program – samtidigt dra nytta av effektivitet för cloud computing.

Som värd för dina program i Azure, kan du börja litet och enkelt skala ditt program när kundernas behov växer. Azure erbjuder också tillförlitligheten som behövs för program med hög tillgänglighet, även inklusive växling mellan olika regioner. Den [Azure-portalen](https://portal.azure.com) kan du enkelt hantera alla dina Azure-tjänster. Du kan också hantera dina tjänster via programmering med hjälp av service-API: er och mallar.

**Vem ska läsa det här**: den här guiden ger en introduktion till Azure-plattformen för programutvecklare. Det ger vägledning och riktning som behövs för att börja skapa nya program i Azure eller migrera befintliga program till Azure.

## <a name="where-do-i-start"></a>Vad ska jag börja med?

Det kan vara besvärligt att ta reda på vilka tjänster som behövs för att stödja din lösningsarkitektur med alla de tjänster som Azure erbjuder. Det här avsnittet visar Azure-tjänster som utvecklare ofta använder. En lista över alla Azure-tjänster, finns det [dokumentation för Azure](../../index.md).

Först måste du bestämma hur som värd för ditt program i Azure. Behöver du kunna hantera hela din infrastruktur som en virtuell dator (VM). Kan du använda hanteringsfunktioner för plattformen som Azure tillhandahåller? Du kanske behöver en serverlösa ram som värd kodkörning endast?

Programmet måste molnlagring som Azure erbjuder flera alternativ för. Du kan dra nytta av Azures enterprise-autentisering. Det finns också verktyg för molnbaserade utveckling och övervakning och de flesta värdtjänster erbjuder DevOps-integration.

Nu ska vi titta på några av de specifika tjänster som vi rekommenderar att undersöka för dina program.

### <a name="application-hosting"></a>Programvärd

Azure tillhandahåller flera molnbaserade compute erbjudanden om du vill köra programmet så att du inte behöver bekymra dig om infrastruktur. Du kan enkelt skala upp eller ut dina resurser som din programanvändning växer.

Azure erbjuder tjänster som stöder utveckling av program och vara värd för behov. Azure tillhandahåller infrastruktur-som-en-tjänst (IaaS) ger dig fullständig kontroll över din programvärd. Azures plattform som en tjänst (PaaS)-erbjudanden tillhandahålla fullständigt hanterade tjänster som behövs för att starta dina appar. Det finns även true serverlösa värd i Azure där allt du behöver göra är att skriva koden.

![Azure-program webbhotell](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

När du vill att den snabbaste sökvägen att publicera dina webbaserade projekt, Överväg Azure App Service. Apptjänst gör det enkelt att utöka dina webbprogram för att stödja dina mobila klienter och publicera enkelt förbrukade REST API: er. Den här plattformen ger autentisering via sociala providers, baserat på trafik autoskalning testa i produktion och kontinuerlig och behållaren-baserade distributioner.

Du kan skapa webbappar, mobilapp-servrar och API apps.

Eftersom alla tre apptyperna delar Apptjänst körning kan du vara värd för en webbplats, stöd för mobila klienter och exponera dina API: er i Azure, allt från samma projekt eller lösning. Mer information om App Service finns [vad är Azure Web Apps](../../app-service/app-service-web-overview.md).

Apptjänst har utformats med DevOps i åtanke. Det stöder olika verktyg för publicering och kontinuerlig integration distributioner, däribland GitHub webhooks, Jenkins, Visual Studio Team Services, TeamCity och andra.

Du kan migrera dina befintliga program till App Service med hjälp av den [online Migreringsverktyg](https://www.migratetoazure.net/).

>**När du ska använda**: använda Apptjänst när du migrerar befintliga webbprogram till Azure och du behöver en helt hanterad plattform som värd för dina webbprogram. Du kan också använda Apptjänst när du behöver stöd för mobila klienter eller använda REST API: er med din app.

>**Kom igång**: Apptjänst gör det enkelt att skapa och distribuera din första [webbapp](../../app-service/app-service-web-get-started-dotnet.md), [mobilappen](../../app-service-mobile/app-service-mobile-ios-get-started.md), eller [API-app](../../app-service/app-service-web-tutorial-rest-api.md).

>**Prova nu**: App Service kan du etablera en tillfällig app om du vill försöka plattformen utan att behöva registrera dig för ett Azure-konto. Försök plattformen och [skapa en app i Azure App Service](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Som en infrastruktur som en tjänst (IaaS)-provider kan du distribuera till eller migrera dina program till Windows eller Linux-datorer i Azure. Tillsammans med Azure Virtual Network stöder Azure virtuella datorer distribution av Windows eller Linux-datorer till Azure. Med virtuella datorer har du fullständig kontroll över konfigurationen för datorn. När du använder virtuella datorer som är du ansvarig för alla server installation, konfiguration, underhåll och operativsystemet programuppdateringar.

På grund av nivå av kontroll som du har med virtuella datorer, kan du köra en mängd olika serverarbetsbelastningar på Azure som inte får plats i en PaaS-modell. De här arbetsbelastningarna inkluderar databasservrar, Windows Server Active Directory och Microsoft SharePoint. Mer information finns i dokumentationen för virtuella datorer för antingen [Linux](/azure/virtual-machines/linux/) eller [Windows](/azure/virtual-machines/windows/).

>**När du ska använda**: använda virtuella datorer om du vill ha fullständig kontroll över din infrastruktur eller migrera lokala programarbetsbelastningar till Azure utan att behöva göra ändringar.

>**Kom igång**: skapa en [Linux VM](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) eller [Windows VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) från Azure-portalen.

#### <a name="azure-functions-serverless"></a>Azure Functions (serverlösa)

I stället för att bekymra dig om att bygga ut och hantera hela programmet eller infrastrukturen för att köra din kod. Vad händer om du bara skriva koden och ha den körs som svar på händelser eller enligt ett schema?  [Azure Functions](../../azure-functions/functions-overview.md) är en ”serverlösa”-format erbjudande där du kan skriva enbart koden som du behöver. Med funktioner utlöses kodkörning av HTTP-begäranden, webhooks, cloud service händelser eller enligt ett schema. Skriva kod i programmeringsspråk du föredrar, till exempel C\#, F\#, Node.js, Python eller PHP. Med förbrukningsbaserad fakturering betalar bara för den tid som koden körs och Azure skalas efter behov.

>**När du ska använda**: använda Azure Functions när du har kod som utlöses av andra Azure-tjänster genom webbaserade händelser, eller enligt ett schema. Du kan också använda funktioner när du inte behöver kostnader för ett fullständigt värdbaserade projekt eller när du bara vill betala för tiden koden körs. Läs mer i [översikt över Azure Functions](../../azure-functions/functions-overview.md).

>**Kom igång**: Följ funktioner Snabbstartsguide till [skapa din första funktion](../../azure-functions/functions-create-first-azure-function.md) från portalen.

>**Prova nu**: Azure Functions kan du köra din kod utan att behöva registrera dig för ett Azure-konto. Prova nu till och [skapa din första Azure-funktion](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric är en plattform för distribuerade system som gör det enkelt att skapa, paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Det ger också funktioner för hantering av omfattande program för etablering, distribution, övervakning, uppgradering/uppdatering och borttagning distribuerade program. Appar som körs på en delad pool av datorer, kan du börja litet och skala till hundratals eller tusentals datorer efter behov.

Service Fabric stöder WebAPI med Open Web Interface för .NET (OWIN) och ASP.NET Core. Det ger SDK: er för att skapa tjänster på Linux i både .NET Core och Java. Läs mer om Service Fabric i den [Service Fabric-Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/service-fabric/).

>**När du ska använda:** Service Fabric är bra när du skapar ett program eller skriva om ett befintligt program att använda en arkitektur för mikrotjänster. Använda Service Fabric när du behöver mer kontroll över eller direkt åtkomst till den underliggande infrastrukturen.

>**Komma igång:** [skapa ditt första Azure Service Fabric-program](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Förbättra dina program med Azure-tjänster

Förutom programvärd, tillhandahåller Azure Tjänsterbjudanden som kan förbättra funktioner, utveckling och underhåll av ditt program, både i molnet och lokalt.

#### <a name="hosted-storage-and-data-access"></a>Värdbaserade lagrings- och åtkomst

De flesta program måste lagra data, så Överväg att en eller flera av följande lagrings- och tjänster oavsett hur du väljer att vara värd för programmet i Azure.

-   **Azure Cosmos-DB**: en global och flera olika modeller databastjänst som du kan anpassa Elastiskt dataflöden och lagringsutrymmen till alla geografiska områden med en omfattande SLA. 
    >**När du ska använda:** när programmet måste dokumentet, tabell eller diagram databaser, inklusive MongoDB-databaser med flera väldefinierade konsekvenskontroll modeller. 

    >**Kom igång**: [skapa en webbapp i Azure Cosmos DB](../../cosmos-db/create-documentdb-dotnet.md). Om du utvecklar en MongoDB [bygga en MongoDB-webbapp med Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

-   **Azure Storage**: erbjuder beständig, högtillgänglig lagring för blobbar, köer, filer och andra typer av icke-relationella data. Lagring utgör lagringsgrunden för virtuella datorer.

    >**När du ska använda**: när en app lagrar icke-relationella data, till exempel nyckel-värdepar (tabeller), blobbar, filer resurser eller meddelanden (köer).

    >**Kom igång**: Välj något av dessa typer av lagring: [blobbar](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabeller](../../cosmos-db/table-storage-how-to-use-dotnet.md), [köer](../../storage/queues/storage-dotnet-how-to-use-queues.md), eller [filer](../../storage/files/storage-dotnet-how-to-use-files.md).

-   **Azure SQL Database**: Azure-baserad version av Microsoft SQL Server-databasmotorn för att lagra relationella tabelldata i molnet. SQL Database tillhandahåller förutsägbar prestanda, skalbarhet utan avbrott, verksamhetskontinuitet och dataskydd.

    >**När du ska använda**: när programmet kräver datalagring med referensintegritet, transaktionella stöd och stöd för TSQL-frågor.

    >**Kom igång**: [skapa en SQL-databas i minuter med hjälp av Azure portal](../../sql-database/sql-database-get-started.md).


Du kan använda [Azure Data Factory](../../data-factory/introduction.md) att flytta befintliga lokala data till Azure. Om du inte är redo att flytta data till molnet, [Hybridanslutningar](../../biztalk-services/integration-hybrid-connection-overview.md) i BizTalk-tjänst kan du ansluta din App Service finns app på lokala resurser. Du kan också ansluta till Azure data- och lagringsresurser tjänster från din lokala program.

#### <a name="docker-support"></a>Docker-support

Docker-behållare, en typ av OS-virtualisering, kan du distribuera program på ett mer effektivt och förutsägbart sätt. Ett container program fungerar i produktion på samma sätt som på dina system för utveckling och testning. Du kan hantera behållare med Docker standardverktyg. Du kan använda dina befintliga kunskaper och verktyg för populära öppen källkod för att distribuera och hantera behållare-baserade program på Azure.

Azure tillhandahåller flera olika sätt att använda behållare i dina program.

-   **Azure Docker VM-tillägget**: kan du konfigurera den virtuella datorn med Docker-verktyg för att fungera som en Docker-värd.

    >**När du ska använda**: när du vill generera konsekvent behållardistributionerna för dina program på en virtuell dator, eller när du vill använda [Docker Compose](https://docs.docker.com/compose/overview/).

    >**Kom igång**: [skapar en Docker-miljö i Azure genom att använda Docker VM-tillägget](../../virtual-machines/virtual-machines-linux-dockerextension.md).

-   **Azure Container Service**: gör att du kan skapa, konfigurera och hantera kluster för virtuella datorer som är förkonfigurerad för körning av program. Läs mer om Behållartjänsten i [Azure Container Service introduktion](../../container-service/container-service-intro.md).

    >**När du ska använda**: när du behöver skapa produktionsklara, skalbara miljöer som ger ytterligare schemaläggning och hanteringsverktygen eller när du distribuerar ett Docker Swarm-kluster.

    >**Kom igång**: [distribuera ett Container Service-kluster](../../container-service/dcos-swarm/container-service-deployment.md).

-   **Docker datorn**: kan du installera och hantera en Docker-motorn på virtuella värdar med kommandona för docker-datorn.

    >**När du ska använda**: när du behöver snabbt prototyp en app genom att skapa en enda Docker-värd.

-   **Anpassad Docker-avbildning för Apptjänst**: kan du använda Docker-behållare från en behållare registret eller en kund-behållare när du distribuerar ett webbprogram på Linux.

    >**När du ska använda**: när du distribuerar ett webbprogram på Linux till en Docker-bild.

    >**Kom igång**: [använder en anpassad Docker-avbildning för användning på Linux](../../app-service/containers/quickstart-custom-docker-image.md).

### <a name="authentication"></a>Autentisering

Det är viktigt att inte bara veta vem som använder dina program, utan även att förhindra obehörig åtkomst till resurser. Azure tillhandahåller flera olika sätt att autentisera klienter för din app.

-   **Azure Active Directory (AD Azure)**: Microsoft flera innehavare, molnbaserade identitets- och management-tjänsten. Du kan lägga till enkel inloggning (SSO) för dina program genom att integrera med Azure AD. Du kan komma åt egenskaper för katalog med hjälp av Azure AD Graph API direkt eller Microsoft Graph API. Du kan integrera med Azure AD-stöd för OAuth2.0 auktorisering framework och öppna ID Connect med hjälp av inbyggda HTTP-REST-slutpunkter och multiplatform Azure AD-autentiseringsbibliotek.

    >**När du ska använda**: när du vill få en SSO-upplevelse arbeta med Graph-baserade data eller autentisera domänbaserade användare.

    >**Kom igång**: Mer information finns i [Utvecklarhandbok för Azure Active Directory](../../active-directory/develop/active-directory-developers-guide.md).

-   **Autentiseringen av tjänsten App**: när du väljer Apptjänst som värd för din app kan du också få stöd för inbyggda autentisering för Azure AD, tillsammans med sociala identitetsleverantörer, inklusive Facebook, Google, Microsoft och Twitter.

    >**När du ska använda**: när du vill aktivera autentisering i en Apptjänst-app med Azure AD, sociala identitetsleverantörer eller båda.

    >**Kom igång**: Mer information om autentisering i App Service finns [autentisering och auktorisering i Azure App Service](../../app-service/app-service-authentication-overview.md).

Mer information om metodtips för säkerhet i Azure finns [Azure säkerhetsmetoder och mönster](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Övervakning

Med programmet in och körs i Azure, behöver du för att kunna övervaka prestanda, efter problem och se hur kunder använder din app. Azure erbjuder flera alternativ för övervakning.

-   **Visual Studio Application Insights**: ett Azure-baserad extensible analytics-tjänsten som kan integreras med Visual Studio för att övervaka webbprogrammens live. Den ger dig de data som behövs för att kontinuerligt förbättra prestanda och användbarhet dina appar, om de är finns på Azure eller inte.

    >**Kom igång**: följer den [Application Insights kursen](../../application-insights/app-insights-overview.md).

-   **Azure-Monitor**: en tjänst som hjälper dig att visualisera, fråga, vidarebefordra, arkivera och agera på mått och loggar som genereras av dina Azure-infrastrukturen och resurser. Övervakaren ger datavyer som du ser i Azure-portalen och är en enda källa för övervakning av Azure-resurser.
 
    >**Kom igång**: [Kom igång med Azure-Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>DevOps-integration

Om det är etablering av virtuella datorer eller publicera dina webbprogram med kontinuerlig integration, integreras Azure med de flesta populära DevOps-verktygen. Du kan arbeta med de verktyg som du redan har och maximera din befintliga upplevelse med stöd för verktyg som Jenkins GitHub, Puppet, Chef, TeamCity, Ansible, VSTS och andra.

>**Prova nu:** [testa flera DevOps-integreringar](https://azure.microsoft.com/try/devops/).

>**Kom igång**: DevOps-alternativ för en App Service-appen finns [kontinuerlig distribution till Azure App Service](../../app-service/app-service-continuous-deployment.md).


## <a name="azure-regions"></a>Azure-regioner

Azure är en global molnplattform som är allmänt tillgänglig i många regioner runtom i världen. När du etablerar en tjänst, program eller virtuell dator i Azure, uppmanas du att välja en region som representerar ett visst datacenter där programmet körs eller där dina data lagras. Dessa områden motsvarar specifika platser som är publicerade på den [Azure-regioner](https://azure.microsoft.com/regions/) sidan.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Välj den bästa regionen för dina program och data

En av fördelarna med att använda Azure är att du kan distribuera ditt program till olika Datacenter över hela världen. Den region som du väljer kan påverka prestanda för ditt program. Exempelvis är det bättre att välja en region som ligger närmast de flesta av dina kunder och minska svarstiden i nätverksbegäranden. Du kanske också vill Välj region till de juridiska krav för att distribuera din app i vissa länder. Det är alltid en bra idé att lagra tillämpningsdata i samma datacenter eller i ett datacenter som nära som möjligt det datacenter som är värd för ditt program.

### <a name="multi-region-apps"></a>Flera regioner appar

Även om det är osannolik, är det inte omöjligt för ett helt datacenter för att försättas i offlineläge på grund av en händelse, till exempel en naturkatastrof eller ett Internet-fel. Det är en bra idé att värden viktiga affärsprogram i flera datacenter ger högsta tillgänglighet. Med hjälp av flera regioner kan också minska svarstiden för globala användare och möjligheter ytterligare flexibilitet vid uppdatering av program.

Vissa tjänster, till exempel virtuella datorn och App-tjänster, använda [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) att aktivera stöd för flera regioner med växling mellan regioner som stöd för hög tillgänglighet företagsprogram. Ett exempel finns [Azure Referensarkitektur: webbprogrammet med hög tillgänglighet](../../guidance/guidance-web-apps-multi-region.md).

>**När du ska använda**: när du har enterprise och hög tillgänglighet program som har nytta av redundans och replikering.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hur hanterar jag mina program och projekt?

Azure tillhandahåller en omfattande uppsättning funktioner att skapa och hantera dina Azure-resurser, program och projekt – både programmässigt och i den [Azure-portalen](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Kommandoradsverktyget gränssnitt och PowerShell

Azure tillhandahåller två sätt att hantera dina program och tjänster från kommandoraden med hjälp av Bash Terminal, Kommandotolken eller din kommandoradsverktyget föredrar. Vanligtvis kan du utföra samma uppgifter från kommandoraden i Azure portal – till exempel skapa och konfigurera virtuella datorer, virtuella nätverk, webbprogram och andra tjänster.

-   [Azure-kommandoradsgränssnittet (CLI)](../../xplat-cli-install.md): låter dig ansluta till en Azure-prenumeration och program för olika aktiviteter mot Azure-resurser från kommandoraden.

-   [Azure PowerShell](../../powershell-install-configure.md): innehåller en uppsättning av moduler med cmdlets som hjälper dig att hantera Azure-resurser med hjälp av Windows PowerShell.

### <a name="azure-portal"></a>Azure Portal

Azure-portalen är ett webbaserat program som du kan använda för att skapa, hantera och ta bort Azure-resurser och tjänster. Azure-portalen finns i <https://portal.azure.com>. Den innehåller en anpassningsbar instrumentpanel verktyg för att hantera Azure-resurser och åtkomst till prenumerationsinställningar och faktureringsinformation. Mer information finns i [översikt över Azure portal](../../azure-portal-overview.md).

### <a name="rest-apis"></a>REST API:er

Azure bygger på en uppsättning REST API: er som har stöd för Azure-portalen Användargränssnittet. De flesta av dessa REST-API: er stöds även om du vill kan du etablera och hantera dina Azure-resurser och program från valfri Internet-aktiverad enhet programmässigt. En fullständig uppsättning av REST API-dokumentationen finns det [Azure SDK för REST-referens](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>API:er

Förutom REST API: er kan många Azure-tjänster du hantera programmässigt resurser från ditt program med hjälp av plattformsspecifika Azure SDK, inklusive SDK: er för följande utvecklingsplattformar:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](http://azure.github.io/azure-sdk-for-node/)
-   [Java](https://docs.microsoft.com/java/api/)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)

Tjänster som [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) och [Azure Media Services](../../media-services/media-services-dotnet-how-to-use.md) Ange klientens SDK: er så att du kan komma åt tjänster från webb- och mobile-klientprogram.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Kör appen på Azure sannolikt innebär att arbeta med flera Azure-tjänster, som följer samma livscykel och kan betraktas som en logisk enhet. Ett webbprogram kan till exempel använda Web Apps, SQL-databas, lagring, Azure Redis-Cache och Azure Content Delivery Network services. [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) kan du arbeta med resurserna i ditt program som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i en enda, samordnad åtgärd.

Förutom logiskt gruppera och hantera relaterade resurser, inkluderar Azure Resource Manager distribution funktioner som du kan anpassa distributionen och konfigurationen av relaterade resurser. Till exempel genom att använda Resource Manager kan du distribuera och konfigurera ett program som består av flera virtuella datorer, en belastningsutjämnare och Azure SQL-databas som en enda enhet.

Du kan utveckla dessa distributioner med en Azure Resource Manager-mall som är en JSON-formaterade dokument. Mallar kan du definiera en distribution och hantera dina program genom att använda deklarativa mallar, i stället för skript. Mallar kan användas i olika miljöer som testning, mellanlagring och produktion. Till exempel med hjälp av mallar du kan lägga till en knapp till en GitHub-lagringsplatsen som distribuerar koden i lagringsplatsen till en uppsättning med Azure-tjänster med en enda klickning.

>**När du ska använda**: Använd Resource Manager-mallar när du vill använda en mall-baserad distribution för din app som du kan hantera programmässigt med hjälp av REST API: er, Azure CLI och Azure PowerShell.

>**Kom igång**: Kom igång med hjälp av mallar, se [redigera Azure Resource Manager-mallar](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Förstå konton, prenumerationer och fakturering

Som utvecklare, som vi vill sätta igång direkt i koden och försök att komma igång så snabbt som möjligt med att vår programmen kan köras. Vi vill verkligen uppmanar dig att arbeta i Azure så enkelt som möjligt. För att göra det enkelt, Azure erbjuder en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/). Vissa tjänster även har en ”testa det gratis” funktionalitet som [Azure App Service](https://tryappservice.azure.com/), som inte kräver att du även skapa ett konto. Som roliga som det fördjupa dig i kodning och distribuera programmet till Azure, är det också viktigt att ta lite tid att förstå hur Azure fungerar från en synvinkel av användarkonton, prenumerationer och fakturering.

### <a name="what-is-an-azure-account"></a>Vad är ett Azure-konto?

För att kunna skapa eller arbeta med en Azure-prenumeration, måste du ha ett Azure-konto. Ett Azure-konto är helt enkelt en identitet i Azure AD eller i en katalog, t.ex en arbets- eller skolkonto organisation, som är betrodd av Azure AD. Om du inte tillhör sådan organisation, kan du alltid skapa en prenumeration med ditt Microsoft-Account som är betrott av Azure AD. Mer information om integreringen med lokala Windows Server Active Directory med Azure AD finns [integrera dina lokala identiteter med Azure Active Directory](../../active-directory/active-directory-aadconnect.md).

Alla Azure-prenumerationer har en förtroenderelation med en Azure AD-instans. Det innebär att den litar på den katalogen för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma katalog, men en prenumeration litar bara på en katalog. Läs mer i [hur Azure-prenumerationer är associerade med Azure Active Directory](../../active-directory/active-directory-how-subscriptions-associated-directory.md).

Förutom definierar enskilda Azure-konto identiteter, kallas även *användare*, du kan också definiera *grupper* i Azure AD. Att skapa användargrupper är ett bra sätt att hantera åtkomst till resurser i en prenumeration med hjälp av rollbaserad åtkomstkontroll (RBAC). Information om hur du skapar grupper finns [skapar en grupp i Azure Active Directory preview](../../active-directory/active-directory-groups-create-azure-portal.md). Du kan också skapa och hantera grupper av [med hjälp av PowerShell](../../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Hantera prenumerationer

En prenumeration är en logisk enhet i Azure-tjänster som är kopplad till ett Azure-konto. Varje associerade konto har en roll i en prenumeration. Fakturering för Azure-tjänster görs på grundval av per prenumeration. En lista över tillgängliga prenumerationserbjudanden efter typ, se [Erbjudandeinformationen för Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/).

#### <a name="administrator-roles"></a>Administratörsroller

En Azure-prenumeration har flera konto administratörsroller, som du kan tilldela när som helst.

-   **Kontoadministratör**: den här rollen har fullständig kontroll över prenumerationen och det konto som ansvarar för fakturering.

-   **Tjänstadministratör**: den här rollen har kontroll över alla tjänster i prenumerationen. Som standard är detta samma konto som kontoadministratör.

-   **Medadministratör**: den här rollen har samma åtkomst som tjänstadministratör, förutom att det går inte att ändra associering av prenumerationen till en Azure-katalog.

Läs mer om administratörsroller i [lägga till eller ändra Azure-administratörsroller](../../billing/billing-add-change-azure-subscription-administrator.md#add-an-admin-for-a-subscription).

#### <a name="resource-groups"></a>Resursgrupper

När du etablerar en ny Azure-tjänster måste göra du det i en viss prenumeration. Enskilda Azure-tjänster, som också kallas resurser skapas i samband med en resursgrupp. Resursgrupper gör det enklare att distribuera och hantera resurser i ditt program. En resursgrupp ska innehålla alla resurser för programmet som du vill arbeta med som en enhet. Du kan flytta resurser mellan resursgrupper och även till olika prenumerationer. Läs om hur du flyttar resurser i [flytta resurser till en ny resursgrupp eller prenumeration](../../resource-group-move-resources.md).

Resursutforskaren i Azure är ett bra verktyg för visualisering av de resurser som du redan har skapat i din prenumeration. Läs mer i [Använd resursutforskaren i Azure visa och ändra resurser](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Bevilja åtkomst till resurser

När du tillåter åtkomst till Azure-resurser, men det är alltid en bra idé att förse användare med det lägsta privilegium som krävs för att utföra en viss uppgift.

-   **Rollbaserad åtkomstkontroll (RBAC)**: I Azure, kan du bevilja åtkomst till användarkonton (säkerhetsobjekt) på ett angivet omfång: prenumeration, resursgrupp eller enskilda resurser. RBAC kan du distribuera en uppsättning resurser i en resursgrupp och ge behörighet till en specifik användare eller grupp. Du kan också begränsa åtkomsten till endast de resurser som hör till målresursgruppen. Du kan också ge åtkomst till en enskild resurs, till exempel en virtuell dator eller virtuella nätverk. Om du vill bevilja åtkomst, tilldela en roll till användare, grupp eller tjänstens huvudnamn. Det finns många fördefinierade roller och du kan också definiera dina egna anpassade roller.

    >**När du ska använda**: när du behöver detaljerad åtkomsthantering för användare och grupper.

    >**Kom igång**: Läs mer i [Kom igång med åtkomsthantering i Azure portal](../../active-directory/role-based-access-control-what-is.md).

-   **Service principal objekt**: förutom att tillhandahålla åtkomst till säkerhetsobjekt som användare och grupper du kan ge samma åtkomst till ett huvudnamn för tjänsten.

    > **När du ska använda**: när du programmässigt hantera Azure-resurser eller bevilja tillgång till program. Mer information finns i [Skapa Active Directory-program och tjänstens huvudnamn](../../resource-group-create-service-principal-portal.md).

#### <a name="tags"></a>Taggar

Azure Resource Manager kan du tilldela anpassade taggar till enskilda resurser. Taggar som nyckel / värde-par, kan vara användbart när du behöver ordna resurser för fakturering och övervakning. Taggar ger dig ett sätt att spåra resurser över flera resursgrupper. Du kan tilldela taggar i portalen i Azure Resource Manager-mall eller programmässigt med hjälp av REST-API, Azure CLI eller PowerShell. Du kan tilldela flera etiketter till varje resurs. Läs mer i [med taggar för att organisera dina Azure-resurser](../../resource-group-using-tags.md).

### <a name="billing"></a>Fakturering

Flytt från lokala datorer till molnbaserade tjänster, är spårning och utvärdering av användningen av tjänsten och relaterade kostnader betydande problem. Det är viktigt för att kunna beräkna vilka nya resurser kostnaden för att köra månadsvis. Du måste också kunna projektet hur faktureringen söker efter en viss månad baserat på aktuella utgifter.

#### <a name="get-resource-usage-data"></a>Hämta resursanvändningsdata

Azure tillhandahåller en uppsättning fakturering REST API: er som ger åtkomst till resursen användnings- och metadatainformation för Azure-prenumerationer. API: erna fakturering ger dig möjlighet att bättre förutsäga och hantera Azure kostnader. Du kan spåra och analyserar indelad i timmar, skapa utgiftsgränsen aviseringar och förutsäga framtida fakturering baserat på aktuella användningstrender.

>**Kom igång**: Mer information om hur du använder API: er fakturering finns [översikt över Azure Billing-användning och RateCard APIs](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Förutsäga framtida kostnader

Även om det är svårt för att uppskatta kostnader i förväg, Azure har en [prisnivå Kalkylatorn](https://azure.microsoft.com/pricing/calculator/) som du kan använda när du beräkna kostnaden för distribuerade resurser. Du kan också använda bladet fakturering i portalen och för fakturering REST API: er för att beräkna framtida kostnader, baserat på aktuella förbrukningen.

>**Kom igång**: se [översikt över Azure Billing-användning och RateCard APIs](../../billing-usage-rate-card-overview.md).

#### <a name="set-up-billing-alerts"></a>Ställa in faktureringsvarningar

När du har distribuerat programmet eller lösningen på Azure, kan du skapa aviseringar som skickar du e-post när du hanterar utgiftsgränser som definieras i aviseringen.

>**Kom igång**: Läs mer i [konfigurera fakturering aviseringar för Microsoft Azure-prenumerationer](../../billing-set-up-alerts.md).
