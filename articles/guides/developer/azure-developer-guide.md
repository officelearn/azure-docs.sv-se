---
title: Startguide för utvecklare på Azure | Microsoft Docs
description: Det här avsnittet innehåller viktig information för utvecklare som vill komma igång med Microsoft Azure-plattformen för deras utvecklingsbehov ska kunna uppfyllas.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: f8aa37dbc2d3fb5f560d899626a84a2ab86ff223
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294585"
---
# <a name="get-started-guide-for-azure-developers"></a>Introduktionsguide för Azure-utvecklare

## <a name="what-is-azure"></a>Vad är Azure?

Azure är en komplett molnplattform som kan vara värd för dina befintliga program, effektivisera utvecklingen av nya program och även förbättra dina lokala program. Azure integrerar molntjänster som du behöver utveckla, testa, distribuera och hantera dina program – och dra samtidigt fördel av effektiviteten med molnbaserad databehandling.

Du kan börja i liten skala och enkelt skala ditt program när kundernas efterfrågan växer av som är värd för dina program i Azure. Azure erbjuder också den tillförlitlighet som behövs för program med hög tillgänglighet, även inklusive redundans mellan olika regioner. Den [Azure-portalen](https://portal.azure.com) kan du enkelt hantera alla dina Azure-tjänster. Du kan också hantera dina tjänster via programmering med hjälp av tjänstspecifika API: er och mallar.

**Vem som bör du läsa följande**: den här guiden ger en introduktion till Azure-plattformen för programutvecklare. Det ger vägledning och riktning som du behöver för att börja bygga nya program i Azure eller migrera befintliga program till Azure.

## <a name="where-do-i-start"></a>Vad ska jag börja med?

Med alla tjänster som Azure erbjuder, kan det vara besvärligt att ta reda på vilka tjänster du behöver för att stödja din arkitektur. Det här avsnittet beskrivs de Azure-tjänster som utvecklare använder ofta. En lista över alla Azure-tjänster finns i den [dokumentation om Azure](../../index.md).

Först måste du bestämma om hur du vara värd för programmet i Azure. Du måste hantera hela infrastrukturen som en virtuell dator (VM). Kan du använda hanteringsfunktioner för plattform som Azure tillhandahåller? Du kanske behöver en serverlös ramverk för att endast kodkörning för värden?

Ditt program behöver molnlagring som Azure tillhandahåller flera alternativ för. Du kan dra nytta av Azures enterprise-autentisering. Det finns också verktyg för molnbaserad utveckling och övervakning och de flesta värdtjänster erbjuder DevOps-integration.

Nu ska vi titta på några av de specifika tjänster som vi rekommenderar att undersöka för dina program.

### <a name="application-hosting"></a>Programvärd

Azure innehåller flera molnbaserade olika erbjudanden om du vill köra ditt program så att du inte behöver bekymra dig om infrastrukturen. Du kan enkelt skala upp eller skala ut dina resurser när programanvändningen ökar.

Azure erbjuder tjänster som stöder utveckling och program som är värd för behov. Azure erbjuder infrastruktur som en tjänst (IaaS) ger dig fullständig kontroll över dina som är programvärd för. Azures plattform som en tjänst (PaaS)-erbjudanden tillhandahåller de fullständigt hanterade tjänster som krävs för att köra dina appar. Det finns även sant serverlösa värdtjänster i Azure där allt du behöver göra är att skriva din kod.

![Azure-program som är värd för alternativ](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

Om du vill att den snabbaste vägen att publicera dina webbaserade projekt kan du överväga att Azure App Service. App Service gör det enkelt att utöka dina webbprogram för att stödja dina mobila klienter och publicera enkelt förbrukade REST API: er. Den här plattformen ger autentisering med sociala leverantörer, trafik-baserad automatisk skalning och testa i produktion och kontinuerliga och behållarbaserade distributioner.

Du kan skapa webbappar, serverdelar för mobilappar och API apps.

Eftersom alla tre apptyperna delar en App Service runtime kan du vara värd för en webbplats, stöd för mobila klienter och exponera dina API: er i Azure, allt från samma projekt eller lösning. Läs mer om App Service i [vad är Azure Web Apps](../../app-service/app-service-web-overview.md).

App Service har utformats med DevOps i åtanke. Det stöder olika verktyg för publicering och kontinuerlig integration distributioner, däribland GitHub webhooks, Jenkins, Azure DevOps, TeamCity och andra.

Du kan migrera dina befintliga program till App Service med hjälp av den [onlinemigreringsverktyg](https://www.migratetoazure.net/).

>**När du ska använda**: använda App Service när du migrerar befintliga webbprogram till Azure och när du behöver en helt hanterad plattform för som värd för dina webbprogram. Du kan också använda App Service när du behöver stöd för mobila klienter eller REST API: er visas med din app.

>**Kom igång**: App Service gör det enkelt att skapa och distribuera din första [webbapp](../../app-service/app-service-web-get-started-dotnet.md), [mobilappen](../../app-service-mobile/app-service-mobile-ios-get-started.md), eller [API-app](../../app-service/app-service-web-tutorial-rest-api.md).

>**Prova nu**: App Service kan du etablera en tillfällig app om du vill prova plattformen utan att behöva registrera dig för ett Azure-konto. Försök plattformen och [skapa din Azure App Service-app](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Som en infrastruktur som en tjänst (IaaS)-provider kan du distribuera till eller migrera ditt program till antingen Windows eller Linux-datorer i Azure. Tillsammans med Azure Virtual Network stöder Azure virtuella datorer distribution av Windows eller Linux-datorer till Azure. Med virtuella datorer har du fullständig kontroll över konfigurationen för datorn. När du använder virtuella datorer, är du ansvarig för alla server installation, konfiguration, underhåll och operativsystemet programuppdateringar.

På grund av nivån av kontroll som du har med virtuella datorer kan du köra en mängd olika server-arbetsbelastningar på Azure som inte passar in i en PaaS-modell. De här arbetsbelastningarna inkluderar database-servrar, Windows Server Active Directory och Microsoft SharePoint. Mer information finns i dokumentationen för virtuella datorer för antingen [Linux](/azure/virtual-machines/linux/) eller [Windows](/azure/virtual-machines/windows/).

>**När du ska använda**: använda virtuella datorer när du vill ha fullständig kontroll över din appinfrastruktur eller att migrera lokala programarbetsbelastningar till Azure utan att behöva göra ändringar.

>**Kom igång**: skapa en [Linux VM](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) eller [Windows VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) från Azure-portalen.

#### <a name="azure-functions-serverless"></a>Azure Functions (utan Server)

I stället för att behöva bekymra dig om att bygga ut och hantera hela programmet eller infrastrukturen för att köra din kod. Vad händer om du kunde bara skriver koden så den köra som svar på händelser eller enligt ett schema?  [Azure Functions](../../azure-functions/functions-overview.md) är en ”serverlös”-style-erbjudande som låter dig skriva enbart koden som du behöver. Med Functions utlöses kod utförandet av HTTP-begäranden, webhooks, cloud service-händelser eller enligt ett schema. Du kan koda i programmeringsspråk du föredrar, till exempel C\#, F\#, Node.js, Python eller PHP. Med konsumtionsbaserad fakturering betalar du endast för den tid som koden körs och Azure kan skalas efter behov.

>**När du ska använda**: Använd Azure Functions när du har kod som utlöses av andra Azure-tjänster av webbaserade händelser eller enligt ett schema. Du kan också använda Functions om du inte behöver overhead för ett fullständigt värdbaserade projekt eller när du bara vill betala för den tid som koden körs. Mer information finns i [översikt över Azure Functions](../../azure-functions/functions-overview.md).

>**Kom igång**: Följ självstudiekursen för Functions-Snabbstart och [skapa din första funktion](../../azure-functions/functions-create-first-azure-function.md) från portalen.

>**Prova nu**: Azure Functions kan du köra din kod utan att behöva registrera dig för ett Azure-konto. Prova nu till och [skapa din första Azure-funktion](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att bygga, paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Det ger också funktioner för hantering av omfattande program för etablering, distribution, övervakning, uppgradering/uppdatering och tar bort distribuerade program. Appar som körs på en delad pool med datorer, kan börja i liten skala och skalar till hundratals eller tusentals datorer efter behov.

Service Fabric stöder WebAPI med Open Web Interface för .NET (OWIN) och ASP.NET Core. Det tillhandahåller SDK: er för att skapa tjänster på Linux i både .NET Core och Java. Läs mer om Service Fabric i den [utbildningsvägen för Service Fabric](https://azure.microsoft.com/documentation/learning-paths/service-fabric/).

>**När du ska använda:** Service Fabric är ett bra val när du skapar ett program eller skriva om ett befintligt program till att använda en mikrotjänstarkitektur. Använda Service Fabric när du behöver mer kontroll över, eller direkt åtkomst till den underliggande infrastrukturen.

>**Kom igång:** [skapa ditt första Azure Service Fabric-program](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Förbättra dina program med Azure-tjänster

Förutom programvärd, tillhandahåller Azure service-erbjudanden som kan förbättra funktioner, utveckling och underhåll av dina program, både i molnet och lokalt.

#### <a name="hosted-storage-and-data-access"></a>Värdbaserad lagring och åtkomst

De flesta program som måste lagra data, så oavsett hur du väljer att vara värd för programmet i Azure, Överväg en eller flera av följande lagrings- och tjänster.

-   **Azure Cosmos DB**: en globalt distribuerad databastjänst som gör att du kan Elastiskt skala dataflöde och lagring över valfritt antal geografiska regioner med ett omfattande SLA. 
    >**När du ska använda:** när ditt program behöver dokument-, tabell- eller grafdatabaser, inklusive MongoDB-databaser med flera väldefinierade konsekvensmodeller. 

    >**Kom igång**: [skapa en webbapp i Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Om du är en MongoDB-utvecklare, se [skapa en MongoDB-webbapp med Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

-   **Azure Storage**: erbjuder hållbar, högtillgänglig lagring för blobbar, köer, filer och andra typer av icke-relationella data. Lagring utgör grunden för lagring för virtuella datorer.

    >**När du ska använda**: när din app lagrar icke-relationella data, till exempel nyckel / värde-par (tabeller), blobbar, filer resurser eller meddelanden (köer).

    >**Kom igång**: Välj något av dessa typer av lagring: [blobar](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabeller](../../cosmos-db/table-storage-how-to-use-dotnet.md), [köer](../../storage/queues/storage-dotnet-how-to-use-queues.md), eller [filer](../../storage/files/storage-dotnet-how-to-use-files.md).

-   **Azure SQL Database**: en Azure-baserad version av Microsoft SQL Server-motorn för att lagra relationsdata tabelldata i molnet. SQL Database ger förutsägbar prestanda, skalbarhet utan avbrott, verksamhetskontinuitet och dataskydd.

    >**När du ska använda**: när ditt program kräver datalagring med referensintegritet, transaktionellt stöd och stöd för TSQL-frågor.

    >**Kom igång**: [skapa en SQL-databas på några minuter med hjälp av Azure portal](../../sql-database/sql-database-get-started.md).


Du kan använda [Azure Data Factory](../../data-factory/introduction.md) att flytta befintliga lokala data till Azure. Om du inte är redo att flytta data till molnet, [Hybridanslutningar](../../biztalk-services/integration-hybrid-connection-overview.md) i BizTalk Services kan du ansluta din App Service finns appen till lokala resurser. Du kan också ansluta till Azure data- och tjänster från din lokala program.

#### <a name="docker-support"></a>Docker-stöd

Docker-behållare, en form av OS-virtualisering, kan du distribuera program på ett mer effektivt och förutsägbart sätt. Ett program i behållare fungerar i produktion på samma sätt som på dina utvecklings- och system. Du kan hantera behållare med hjälp av Docker-verktyg som standard. Du kan använda dina befintliga kunskaper och populära verktyg för öppen källkod för att distribuera och hantera behållarbaserade program i Azure.

Azure tillhandahåller flera olika sätt att använda behållare i dina program.

-   **Azure Docker VM-tillägget**: du kan konfigurera den virtuella datorn med Docker-verktyg som fungerar som en Docker-värd.

    >**När du ska använda**: när du vill generera konsekvent behållardistributioner för dina program på en virtuell dator, eller när du vill använda [Docker Compose](https://docs.docker.com/compose/overview/).

    >**Kom igång**: [skapa en Docker-miljö i Azure med hjälp av Docker VM-tillägget](../../virtual-machines/virtual-machines-linux-dockerextension.md).

-   **Azure Container Service**: låter dig skapa, konfigurera och hantera ett kluster med virtuella datorer som är förkonfigurerade för att köra program i behållare. Läs mer om Container Service i [introduktion till Azure Container Service](../../container-service/container-service-intro.md).

    >**När du ska använda**: när du behöver för att bygga produktionsklara, skalbara miljöer som ger ytterligare schemaläggning och hanteringsverktyg eller när du distribuerar ett Docker Swarm-kluster.

    >**Kom igång**: [distribuera ett behållartjänstkluster](../../container-service/dcos-swarm/container-service-deployment.md).

-   **Docker Machine**: kan du installera och hantera en Docker-motor på virtuella värdar med hjälp av docker-dator kommandon.

    >**När du ska använda**: när du behöver snabbt skapa prototyper en app genom att skapa en enkel Docker-värd.

-   **Anpassad Docker-avbildning för App Service**: du kan använda Docker-behållare från ett behållarregister eller en kund behållare när du distribuerar en webbapp i Linux.

    >**När du ska använda**: när du distribuerar en webbapp i Linux till en Docker-avbildning.

    >**Kom igång**: [använder en anpassad Docker-avbildning för App Service i Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Autentisering

Det är viktigt att inte bara veta vem som använder dina program utan också för att förhindra obehörig åtkomst till dina resurser. Azure tillhandahåller flera olika sätt att autentisera din appklienter.

-   **Azure Active Directory (Azure AD)**: Microsoft med flera klienter, molnbaserade identitets- och management-tjänsten. Du kan lägga till enkel inloggning (SSO) till dina program genom att integrera med Azure AD. Du kan komma åt katalogegenskaper för med hjälp av Azure AD Graph API: et direkt eller Microsoft Graph API. Du kan integrera med Azure AD-stöd för OAuth2.0 auktorisering framework och öppna ID Connect med hjälp av inbyggda HTTP/REST-slutpunkter och multiplatform Azure AD-autentiseringsbibliotek.

    >**När du ska använda**: Om du vill att tillhandahålla enkel inloggning kan arbeta med graf-baserade data eller autentisera domänbaserade användare.

    >**Kom igång**: Mer information finns i den [Utvecklarhandbok för Azure Active Directory](../../active-directory/develop/azure-ad-developers-guide.md).

-   **App Service-autentisering**: när du väljer App Service som värd för din app kan du också hämta inbyggda autentiseringsstöd för Azure AD, tillsammans med sociala identitetsleverantörer, inklusive Facebook, Google, Microsoft och Twitter.

    >**När du ska använda**: när du vill aktivera autentisering i en App Service-app med hjälp av Azure AD, sociala identitetsleverantörer, eller båda.

    >**Kom igång**: Läs mer om autentisering i App Service i [autentisering och auktorisering i Azure App Service](../../app-service/app-service-authentication-overview.md).

Mer information om metodtips för säkerhet i Azure finns [säkerhet i Azure-metodtips och mönster](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Övervakning

Med upp ditt program och som körs i Azure kan behöver du för att kunna övervaka prestanda, håll utkik efter problem och se hur kunderna använder din app. Azure tillhandahåller flera alternativ för övervakning.

-   **Visual Studio Application Insights**: en Azure-värdbaserade utökningsbar analystjänst som kan integreras med Visual Studio för att övervaka dina live-webbprogram. Det ger dig de data som du behöver att kontinuerligt förbättra prestanda och användbarhet dina appar, oavsett om de är på Azure eller inte.

    >**Kom igång**: Följ den [Application Insights-självstudien](../../application-insights/app-insights-overview.md).

-   **Azure Monitor**: en tjänst som hjälper dig att visualisera, fråga, vidarebefordra, arkivera och vidta åtgärder för mått och loggar som genereras av Azure-infrastrukturen och resurser. Monitor innehåller datavyer att du ser i Azure-portalen och är en enda källa för övervakning av Azure-resurser.
 
    >**Kom igång**: [Kom igång med Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>DevOps-integration

Om den är etablera virtuella datorer eller publicera dina webbprogram med kontinuerlig integrering, integrerar Azure med de flesta av de populära DevOps-verktyg. Du kan arbeta med de verktyg du redan har och maximera dina befintliga upplevelse med stöd för verktyg som Jenkins, GitHub, Puppet, Chef, TeamCity, Ansible, Azure DevOps, och andra.

>**Prova nu:** [kan prova att använda flera av integrering av DevOps](https://azure.microsoft.com/try/devops/).

>**Kom igång**: DevOps-alternativ för en App Service-app finns i [kontinuerlig distribution till Azure App Service](../../app-service/app-service-continuous-deployment.md).


## <a name="azure-regions"></a>Azure-regioner

Azure är en global molnplattform som är allmänt tillgängligt i många regioner runt om i världen. När du etablerar ett program, en tjänst eller en virtuell dator i Azure kan uppmanas du att välja en region som representerar en specifik datacenter där programmet körs eller där dina data lagras. Dessa regioner som motsvarar specifika platser som är publicerade på den [Azure-regioner](https://azure.microsoft.com/regions/) sidan.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Välj den bästa regionen för dina program och data

En av fördelarna med att använda Azure är att du kan distribuera program till olika Datacenter runtom i världen. Den region som du väljer kan påverka programmets prestanda. Exempelvis är det bättre att välja en region som ligger närmast de flesta av dina kunder att minska svarstiden i nätverksbegäranden. Du kanske också vill välja din region för att uppfylla juridiska krav för att distribuera din app i vissa länder. Det är alltid en bra idé att lagra programdata i samma datacenter eller i ett datacenter som nära som möjligt i datacentret som är värd för ditt program.

### <a name="multi-region-apps"></a>Appar för flera regioner

Även om det är osannolikt, är det inte omöjligt för ett helt datacenter i offlineläge på grund av en händelse, till exempel en naturkatastrof eller ett Internet-fel. Det är en bra idé att värden viktiga affärsprogram i flera datacenter att ge högsta tillgänglighet. Med hjälp av flera regioner kan också minska svarstiden för globala användare och ge ytterligare möjligheter för flexibilitet när du uppdaterar program.

Vissa tjänster, till exempel virtuella datorer och Apptjänster, som använder [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) att aktivera stöd för flera regioner med redundans mellan regioner för hög tillgänglighet företagsprogram. Ett exempel finns i [Azure-Referensarkitektur: köra ett webbprogram i flera regioner](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**När du ska använda**: när du har enterprise och hög tillgänglighet program som har nytta av redundans och replikering.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hur hanterar jag mina program och projekt?

Azure tillhandahåller en omfattande uppsättning upplevelser som du kan skapa och hantera dina Azure-resurser, program och projekt – både programmässigt och i den [Azure-portalen](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Kommandoradsverktyget gränssnitt och PowerShell

Azure tillhandahåller två sätt att hantera dina program och tjänster från kommandoraden med hjälp av Bash, Terminal, Kommandotolken eller din kommandoradsverktyget val. Vanligtvis kan du utföra samma uppgifter från kommandoraden som i Azure portal – till exempel skapa och konfigurera virtuella datorer, virtuella nätverk, webbappar och andra tjänster.

-   [Azure kommandoradsgränssnitt (CLI)](../../xplat-cli-install.md): kan du ansluta till en Azure-prenumeration och program för olika uppgifter mot Azure-resurser från kommandoraden.

-   [Azure PowerShell](../../powershell-install-configure.md): innehåller en uppsättning moduler med cmdlets som hjälper dig att hantera Azure-resurser med hjälp av Windows PowerShell.

### <a name="azure-portal"></a>Azure Portal

Azure-portalen är ett webbaserat program som du kan använda för att skapa, hantera och ta bort Azure-resurser och tjänster. Azure-portalen finns i <https://portal.azure.com>. Den innehåller en anpassningsbar instrumentpanel, verktyg för att hantera Azure-resurser, och åtkomst till prenumerationsinställningar och faktureringsinformation. Mer information finns i den [översikt över Azure portal](../../azure-portal-overview.md).

### <a name="rest-apis"></a>REST API:er

Azure bygger på en uppsättning REST API: er som har stöd för Azure-portalens användargränssnitt. De flesta av de här REST-API: er stöds även om du vill kan du etablera och hantera dina Azure-resurser och program från valfri Internet-aktiverad enhet programmässigt. En fullständig uppsättning med REST API-dokumentation finns i den [Azure REST SDK-referensen](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>API:er

Förutom REST API: er kan många Azure-tjänster du även programmässigt hantera resurser från dina program med hjälp av plattformsspecifika Azure SDK: er, inklusive SDK: er för följande plattformar för utveckling:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/go/azure)

Tjänster som [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) och [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) tillhandahåller klientside-SDK: er så att du kan få åtkomst till tjänster från webb- och mobilklienten appar.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Köra ditt program på Azure som är sannolikt innebär att arbeta med flera Azure-tjänster, som följer samma livscykel och kan betraktas som en logisk enhet. En webbapp kan till exempel använda Web Apps, SQL Database, Storage, Azure Redis Cache och Azure Content Delivery Network-tjänster. [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) kan du arbeta med resurserna i ditt program som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i en enda, samordnad åtgärd.

Utöver att logiskt gruppera och hantera relaterade resurser, innehåller Azure Resource Manager distributionsfunktioner som du kan anpassa distributionen och konfigurationen av relaterade resurser. Till exempel genom att använda Resource Manager kan du distribuera och konfigurera ett program som består av flera virtuella datorer, en belastningsutjämnare och en Azure SQL database som en enda enhet.

Du kan utveckla dessa distributioner med en Azure Resource Manager-mall som är ett JSON-formaterade dokument. Mallar kan du definiera en distribution och hantera dina program med hjälp av deklarativa mallar i stället för skript. Dina mallar kan användas i olika miljöer, till exempel testning, mellanlagring och produktion. Med mallar kan du till exempel lägga till en knapp till en GitHub-lagringsplats som distribuerar koden i lagringsplatsen till en uppsättning Azure-tjänster med ett enda klick.

>**När du ska använda**: använda Resource Manager-mallar när du vill ha en mallbaserad distribution för din app som du kan hantera programmässigt med hjälp av REST API: er, Azure CLI och Azure PowerShell.

>**Kom igång**: Kom igång med hjälp av mallar, se [redigera Azure Resource Manager-mallar](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Förstå konton, prenumerationer och fakturering

Som utvecklare som vi att gå direkt till koden och försök att komma igång så snabbt som möjligt med att vår programmen kan köras. Vi vill verkligen uppmuntrar dig att börja arbeta i Azure så enkelt som möjligt. För att göra det enkelt, Azure erbjuder en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/). Vissa tjänster har även en ”prova kostnadsfritt”-funktion som [Azure App Service](https://tryappservice.azure.com/), som inte kräver att du även skapa ett konto. Som roligt eftersom det är att ta itu kodning och distribuera program till Azure, är det också viktigt att ta lite tid att förstå hur Azure fungerar från synpunkt av konton, prenumerationer och fakturering.

### <a name="what-is-an-azure-account"></a>Vad är ett Azure-konto?

För att kunna skapa eller arbeta med en Azure-prenumeration, måste du ha ett Azure-konto. Ett Azure-konto är helt enkelt en identitet i Azure AD eller i en katalog, t.ex en arbets- eller skolkonto organisation, som är betrodd av Azure AD. Om du inte tillhör sådan organisation, kan du alltid skapa en prenumeration med hjälp av ditt Microsoft-Account som är betrott av Azure AD. Mer information om hur du integrerar lokala Windows Server Active Directory med Azure AD finns [integrera dina lokala identiteter med Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Alla Azure-prenumerationer har en förtroenderelation med en Azure AD-instans. Det innebär att den litar på den katalogen för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma katalog, men en prenumeration litar bara på en katalog. Mer information finns i [hur Azure-prenumerationer är associerade med Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Förutom att definiera enskilda Azure-konto identiteter, även kallat *användare*, du kan också definiera *grupper* i Azure AD. Det är ett bra sätt att hantera åtkomst till resurser i en prenumeration med hjälp av rollbaserad åtkomstkontroll (RBAC) för att skapa användargrupper. Läs hur du skapar grupper i [skapa en grupp i Azure Active Directory-förhandsgranskning](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Du kan också skapa och hantera grupper efter [med hjälp av PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Hantera prenumerationer

En prenumeration är en logisk gruppering av Azure-tjänster som är länkad till ett Azure-konto. En enda Azure-konto kan innehålla flera prenumerationer. Fakturering för Azure-tjänster som görs på basis av per prenumeration. En lista över tillgängliga prenumerationserbjudanden efter typ, se [Erbjudandeinformationen för Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Azure-prenumerationer har en konto-administratör som har fullständig kontroll över prenumerationen, och en tjänstadministratör kan vem som har kontroll över alla tjänster i prenumerationen. Information om klassiska prenumerationsadministratörer finns i [Lägg till eller ändra Azure-prenumerationsadministratörer](../../billing/billing-add-change-azure-subscription-administrator.md). Förutom administratörer, enskilda konton kan få detaljerad kontroll över Azure-resurser med [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Resursgrupper

När du etablerar nya Azure-tjänster kan göra du det i en viss prenumeration. Enskilda Azure-tjänster, som också kallas resurser skapas i samband med en resursgrupp. Resursgrupper gör det enklare att distribuera och hantera ditt programs resurser. En resursgrupp bör innehålla alla resurser för programmet som du vill arbeta med som en enhet. Du kan flytta resurser mellan resursgrupper och även till olika prenumerationer. Läs om hur du flyttar resurser i [flytta resurser till ny resursgrupp eller prenumeration](../../resource-group-move-resources.md).

Azure Resource Explorer är ett bra verktyg för visualisering av de resurser som du redan har skapat i din prenumeration. Mer information finns i [Använd Azure Resource Explorer för att visa och ändra resurser](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Bevilja åtkomst till resurser

När du tillåter åtkomst till Azure-resurser, men det är alltid en bra idé att förse användarna med det lägsta privilegium som krävs för att utföra en viss uppgift.

-   **Rollbaserad åtkomstkontroll (RBAC)**: I Azure kan du bevilja åtkomst till användarkonton (huvudkonton) på ett angivet omfång: prenumerationen, resursgruppen eller enskilda resurser. Med RBAC kan du distribuera en uppsättning resurser i en resursgrupp och tilldela behörigheter till en specifik användare eller grupp. Du kan också begränsa åtkomsten till endast de resurser som tillhör målresursgruppen. Du kan också ge åtkomst till en enda resurs, till exempel en virtuell dator eller ett virtuellt nätverk. Om du vill bevilja åtkomst, tilldela en roll till användare, grupp eller tjänstens huvudnamn. Det finns många fördefinierade roller du kan även definiera dina egna anpassade roller. Mer information finns i [vad är rollbaserad åtkomstkontroll (RBAC)?](../../role-based-access-control/overview.md).

    >**När du ska använda**: när du behöver detaljerad åtkomsthantering för användare och grupper eller när du behöver göra en användare en ägare av en prenumeration.

    >**Kom igång**: Mer information finns i [hantera åtkomst med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md).

-   **Tjänsten huvudnamn objekt**: förutom att ge åtkomst till användares huvudnamn och grupper kan du ge samma åtkomst till ett huvudnamn för tjänsten.

    > **När du ska använda**: när du programmässigt hantera Azure-resurser eller beviljar åtkomst för program. Mer information finns i [Skapa Active Directory-program och tjänstens huvudnamn](../../resource-group-create-service-principal-portal.md).

#### <a name="tags"></a>Taggar

Azure Resource Manager kan du tilldela anpassade taggar till enskilda resurser. Taggar som är nyckel / värde-par, kan vara användbart när du behöver organisera resurser för fakturering och övervakning. Taggar ger dig ett sätt att spåra resurser mellan flera resursgrupper. Du kan tilldela taggar i portalen i Azure Resource Manager-mall eller programmässigt, med hjälp av REST API, Azure CLI eller PowerShell. Du kan tilldela flera taggar till varje resurs. Mer information finns i [med taggar för att organisera Azure-resurser](../../resource-group-using-tags.md).

### <a name="billing"></a>Fakturering

Är betydande problem i flytt från lokala databehandling till molnbaserade tjänster, spåra och uppskatta användning och kostnader. Det är viktigt för att kunna beräkna vad nya resurser kostnaden för att köra på månatlig basis. Du måste också kunna projektet hur faktureringen ser ut för en viss månad baserat på aktuell kostnad.

#### <a name="get-resource-usage-data"></a>Hämta användningsdata för resursen

Azure tillhandahåller en uppsättning fakturering REST API: er som ger åtkomst till resursförbrukning och metadatainformation för Azure-prenumerationer. Dessa Billing API: er ger dig möjlighet att bättre förutse och hantera Azure-kostnader. Du kan spåra och analyserar indelad i timmar, skapa utgiftsgränsen aviseringar och förutsäga framtida fakturering baserat på aktuella användningstrender.

>**Kom igång**: Mer information om hur du använder API: erna fakturering finns [översikt över Azure-fakturering användning och RateCard APIs](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Förutse framtida kostnader

Även om det är svårt för att uppskatta kostnader i tid, Azure har en [priskalkylator](https://azure.microsoft.com/pricing/calculator/) som du kan använda när du beräkna kostnaden för distribuerade resurser. Du kan också använda bladet fakturering i portalen och den fakturering REST API: er för att beräkna framtida kostnader, baserat på aktuell förbrukning.

>**Kom igång**: se [översikt över Azure-fakturering användning och RateCard APIs](../../billing-usage-rate-card-overview.md).

#### <a name="set-up-billing-alerts"></a>Ställa in faktureringsvarningar

När du har distribuerat programmet eller lösningen på Azure kan skapa du aviseringar som skickar du e-när du itu med utgiftsgränser som definieras i aviseringen.

>**Kom igång**: Mer information finns i [ställa in faktureringsvarningar för dina Microsoft Azure-prenumerationer](../../billing-set-up-alerts.md).
