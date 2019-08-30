---
title: Kom igång-guide för utvecklare på Azure | Microsoft Docs
description: Det här avsnittet innehåller viktig information för utvecklare som vill komma igång med Microsoft Azure-plattformen för deras utvecklings behov.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: f04dd628ae0999edd202c98521526ae70e1ff0af
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141221"
---
# <a name="get-started-guide-for-azure-developers"></a>Introduktionsguide för Azure-utvecklare

## <a name="what-is-azure"></a>Vad är Azure?

Azure är en komplett moln plattform som kan vara värd för dina befintliga program, effektivisera utvecklingen av nya program och till och med förbättra lokala program. Azure integrerar de moln tjänster som du behöver för att utveckla, testa, distribuera och hantera dina program – samtidigt som du drar nytta av effektiviteten hos molnbaserad data behandling.

Genom att vara värd för dina program i Azure kan du starta små och enkelt skala ditt program när din kund efter frågan växer. Azure erbjuder också den tillförlitlighet som krävs för program med hög tillgänglighet, till exempel även redundans mellan olika regioner. Med [Azure Portal](https://portal.azure.com) kan du enkelt hantera alla dina Azure-tjänster. Du kan också hantera dina tjänster program mässigt med hjälp av tjänste/regionsspecifika API: er och mallar.

**Vem bör läsa detta**: Den här guiden är en introduktion till Azure-plattformen för programutvecklare. Det ger vägledning och riktning som du behöver för att börja skapa nya program i Azure eller migrera befintliga program till Azure.

## <a name="where-do-i-start"></a>Vad ska jag börja med?

Med alla tjänster som Azure erbjuder kan det vara en avskräckande uppgift för att ta reda på vilka tjänster du behöver för att stödja din lösnings arkitektur. I det här avsnittet beskrivs de Azure-tjänster som utvecklare ofta använder. En lista över alla Azure-tjänster finns i [Azure-dokumentationen](../../index.md).

Först måste du bestämma hur du ska vara värd för ditt program i Azure. Behöver du hantera hela infrastrukturen som en virtuell dator (VM). Kan du använda de plattforms hanterings funktioner som Azure tillhandahåller? Kanske behöver du en server lös Ramverks struktur för att endast köra kod på värden?

Ditt program behöver moln lagring, som Azure tillhandahåller flera alternativ för. Du kan dra nytta av Azures företagsspecifika autentisering. Det finns också verktyg för molnbaserad utveckling och övervakning, och de flesta värd tjänster erbjuder DevOps-integrering.

Nu ska vi titta på några av de tjänster som vi rekommenderar att du undersöker för dina program.

### <a name="application-hosting"></a>Program värd

Azure tillhandahåller flera molnbaserade beräknings erbjudanden för att köra ditt program så att du inte behöver bekymra dig om infrastruktur informationen. Du kan enkelt skala upp eller skala ut dina resurser när din program användning växer.

Azure erbjuder tjänster som har stöd för dina program utvecklings-och värd behov. Azure tillhandahåller IaaS (Infrastructure as a Service) för att ge dig fullständig kontroll över dina program värdar. Azures PaaS-erbjudanden (Platform as a Service) tillhandahåller de fullständigt hanterade tjänsterna som krävs för att sätta dina appar. Det finns även sant Server lös värd i Azure där allt du behöver göra är att skriva koden.

![Azure Application hosting-alternativ](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

När du vill att den snabbaste sökvägen ska publicera dina webbaserade projekt bör du överväga Azure App Service. App Service gör det enkelt att utöka dina webbappar till att stödja dina mobila klienter och publicera enkelt förbrukade REST-API: er. Den här plattformen tillhandahåller autentisering med hjälp av sociala leverantörer, principbaserad automatisk skalning, testning i produktion och kontinuerliga och containerbaserade distributioner.

Du kan skapa webbappar, Server delar för mobilappar och API Apps.

Eftersom alla tre typer av appar delar App Service runtime, kan du vara värd för en webbplats, stödja mobila klienter och exponera dina API: er i Azure, allt från samma projekt eller lösning. Mer information om App Service finns i [Vad är Azure Web Apps](../../app-service/overview.md).

App Service har utformats med DevOps i åtanke. Den har stöd för olika verktyg för publicering och kontinuerlig integrering av distributioner, inklusive GitHub Webhooks, Jenkins, Azure DevOps, TeamCity och andra.

Du kan migrera dina befintliga program till App Service med hjälp av [verktyget online-migrering](https://www.migratetoazure.net/).

> **När du ska använda**: Använd App Service när du migrerar befintliga webb program till Azure och när du behöver en fullständigt hanterad värd plattform för dina webb program. Du kan också använda App Service när du behöver stöd för mobila klienter eller exponera REST-API: er med din app.
> 
> **Kom igång**: App Service är det enkelt att skapa och distribuera din första [webbapp](../../app-service/app-service-web-get-started-dotnet.md), [mobilapp](../../app-service-mobile/app-service-mobile-ios-get-started.md)eller [API-app](../../app-service/app-service-web-tutorial-rest-api.md).
> 
> **Prova nu**: Med App Service kan du etablera en app med kort livs längd för att testa plattformen utan att behöva registrera dig för ett Azure-konto. Testa plattformen och [skapa din Azure App Service-app](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure virtuella maskiner

Som en IaaS-Provider (Infrastructure as a Service) kan du använda Azure för att distribuera till eller migrera ditt program till virtuella Windows-eller Linux-datorer. Tillsammans med Azure Virtual Network stöder Azure Virtual Machines distributionen av virtuella Windows-eller Linux-datorer till Azure. Med virtuella datorer har du total kontroll över datorns konfiguration. När du använder virtuella datorer ansvarar du för all serverprogram vara installation, konfiguration, underhåll och operativ system uppdateringar.

På grund av den kontroll nivå som du har med virtuella datorer kan du köra ett brett utbud av Server arbets belastningar på Azure som inte passar in i en PaaS modell. Dessa arbets belastningar omfattar databas servrar, Windows Server Active Directory och Microsoft SharePoint. Mer information finns i Virtual Machines-dokumentationen för [Linux](/azure/virtual-machines/linux/) eller [Windows](/azure/virtual-machines/windows/).

> **När du ska använda**: Använd Virtual Machines när du vill ha fullständig kontroll över din program infrastruktur eller migrera lokala program arbets belastningar till Azure utan att behöva göra några ändringar.
> 
> **Kom igång**: Skapa en virtuell [Linux-dator](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) eller en [virtuell Windows-dator](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) från Azure Portal.

#### <a name="azure-functions-serverless"></a>Azure Functions (utan server)

I stället för att oroa dig för att skapa och hantera ett helt program eller infrastrukturen för att köra din kod. Vad händer om du bara skulle skriva din kod och låta den köras som svar på händelser eller enligt ett schema?  [Azure Functions](../../azure-functions/functions-overview.md) är ett "Server lös"-format som du kan använda för att skriva enbart den kod du behöver. Med Function utlöses kod körning av HTTP-förfrågningar, Webhooks, moln tjänst händelser eller enligt ett schema. Du kan koda i det utvecklings språk som du väljer, till exempel\#C,\#F, Node. js, python eller php. Med förbruknings-baserad fakturering betalar du bara för den tid som koden körs och Azure skalas efter behov.

> **När du ska använda**: Använd Azure Functions när du har kod som utlöses av andra Azure-tjänster, via webbaserade händelser eller enligt ett schema. Du kan också använda funktioner när du inte behöver lägga till ett komplett värdbaserade projekt eller om du bara vill betala för den tid som koden körs. Läs mer i [Azure Functions översikt](../../azure-functions/functions-overview.md).
> 
> **Kom igång**: Följ själv studie kursen för att [skapa din första funktion](../../azure-functions/functions-create-first-azure-function.md) från portalen.
> 
> **Prova nu**: Med Azure Functions kan du köra din kod utan att behöva registrera dig för ett Azure-konto. Testa nu och [skapa din första Azure-funktion](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric är en distribuerad system plattform som gör det enkelt att bygga, paketera, distribuera och hantera skalbara och pålitliga mikrotjänster. Den innehåller också omfattande funktioner för program hantering för etablering, distribution, övervakning, uppgradering, uppdatering och borttagning av distribuerade program. Appar som körs på en delad pool med datorer kan starta små och skala till hundratals eller tusentals datorer efter behov.

Service Fabric stöder WebAPI med Open Web Interface för .NET (OWIN) och ASP.NET Core. Den innehåller SDK: er för att skapa tjänster i Linux i både .NET Core och Java. Mer information om Service Fabric finns i Service Fabric- [dokumentationen](https://docs.microsoft.com/azure/service-fabric/).

> **När du ska använda:** Service Fabric är ett bra val när du skapar ett program eller skriver om ett befintligt program för att använda en arkitektur för mikrotjänster. Använd Service Fabric när du behöver mer kontroll över eller direkt åtkomst till, den underliggande infrastrukturen.
> 
> **Kom igång:** [Skapa ditt första Azure Service Fabric-program](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Förbättra dina program med Azure-tjänster

Förutom program värd tillhandahåller Azure tjänst erbjudanden som kan förbättra funktioner, utveckling och underhåll av dina program, både i molnet och lokalt.

#### <a name="hosted-storage-and-data-access"></a>Värdbaserad lagring och data åtkomst

De flesta program måste lagra data, så oavsett hur du bestämmer dig för att vara värd för ditt program i Azure bör du överväga en eller flera av följande lagrings-och data tjänster.

- **Azure Cosmos DB**: En globalt distribuerad databas tjänst med flera modeller som gör att du kan skala data flöde och lagring i valfritt antal geografiska regioner med ett omfattande service avtal. 
  > **När du ska använda:** När ditt program behöver dokument-, tabell-eller graf-databaser, inklusive MongoDB-databaser, med flera väldefinierade konsekvens modeller. 
  > 
  > **Kom igång**: [Bygg en Azure Cosmos DB-webbapp](../../cosmos-db/create-sql-api-dotnet.md). Om du är en MongoDB-utvecklare kan du läsa mer i [bygga en MongoDB-webbapp med Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Azure Storage**: Erbjuder hållbar lagring med hög tillgänglighet för blobbar, köer, filer och andra typer av ej relationella data. Storage tillhandahåller lagrings bas för virtuella datorer.

  > **När du ska använda**: När din app lagrar ej relationella data, till exempel nyckel/värde-par (tabeller), blobbar, fil resurser eller meddelanden (köer).
  > 
  > **Kom igång**: Välj någon av dessa typer av lagring: [blobbar](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabeller](../../cosmos-db/table-storage-how-to-use-dotnet.md), [köer](../../storage/queues/storage-dotnet-how-to-use-queues.md)eller [filer](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL Database**: En Azure-baserad version av Microsoft SQL Server motor för att lagra Relations tabell data i molnet. SQL Database ger förutsägbar prestanda, skalbarhet utan drift avbrott, verksamhets kontinuitet och data skydd.

  > **När du ska använda**: När ditt program kräver data lagring med referens integritet, transaktionell support och stöd för TSQL-frågor.
  > 
  > **Kom igång**: [Skapa en SQL-databas på några minuter med hjälp av Azure Portal](../../sql-database/sql-database-get-started.md).


Du kan använda [Azure Data Factory](../../data-factory/introduction.md) för att flytta befintliga lokala data till Azure. Om du inte är redo att flytta data till molnet kan [hybridanslutningar](../../biztalk-services/integration-hybrid-connection-overview.md) i BizTalk Services ansluta din app service värdbaserade app till lokala resurser. Du kan också ansluta till Azure data-och lagrings tjänster från dina lokala program.

#### <a name="docker-support"></a>Docker-stöd

Docker-behållare, en form av OS-virtualisering, gör att du kan distribuera program på ett mer effektivt och förutsägbart sätt. Ett program i behållare fungerar i produktion på samma sätt som i utvecklings-och test systemen. Du kan hantera behållare med hjälp av standard Docker-verktyg. Du kan använda dina befintliga kunskaper och populära verktyg med öppen källkod för att distribuera och hantera behållare baserade program på Azure.

Azure tillhandahåller flera olika sätt att använda behållare i dina program.

- **Azure Docker VM-tillägg**: Gör att du kan konfigurera din virtuella dator med Docker-verktyg som fungerar som en Docker-värd.

  > **När du ska använda**: När du vill generera konsekvent behållar distributioner för dina program på en virtuell dator eller när du vill använda [Docker Compose](https://docs.docker.com/compose/overview/).
  > 
  > **Kom igång**: [Skapa en Docker-miljö i Azure med hjälp av Docker VM-tillägget](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Azure Container Service**: Gör att du kan skapa, konfigurera och hantera ett kluster med virtuella datorer som är förkonfigurerade för att köra program i behållare. Mer information om container service finns [Azure Container Service introduktion](../../container-service/container-service-intro.md).

  > **När du ska använda**: När du behöver skapa produktions klara, skalbara miljöer som tillhandahåller ytterligare schemaläggnings-och hanterings verktyg, eller när du distribuerar ett Docker Swarm-kluster.
  > 
  > **Kom igång**: [Distribuera ett Container Service-kluster](../../container-service/dcos-swarm/container-service-deployment.md).

- **Docker-dator**: Gör att du kan installera och hantera en Docker-motor på virtuella värdar med hjälp av Docker Machine-kommandon.

  >**När du ska använda**: När du snabbt behöver prototyp av en app genom att skapa en enda Docker-värd.

- **Anpassad Docker-avbildning för App Service**: Gör att du kan använda Docker-behållare från ett behållar register eller en kund behållare när du distribuerar en webbapp i Linux.

  > **När du ska använda**: När du distribuerar en webbapp på Linux till en Docker-avbildning.
  > 
  > **Kom igång**: [Använd en anpassad Docker-avbildning för App Service på Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

Det är viktigt att inte bara veta vem som använder dina program, utan även för att förhindra obehörig åtkomst till dina resurser. Azure tillhandahåller flera olika sätt att autentisera dina app-klienter.

- **Azure Active Directory (Azure AD)** : Den molnbaserade identitets-och åtkomst hanterings tjänsten för Microsoft-klienten. Du kan lägga till enkel inloggning (SSO) till dina program genom att integrera med Azure AD. Du kan komma åt katalog egenskaper med hjälp av Azure AD Graph API direkt eller Microsoft Graph-API: et. Du kan integrera med Azure AD-stöd för OAuth 2.0 Authorization Framework och öppna ID Connect genom att använda interna HTTP/REST-slutpunkter och Azure AD-autentiseringspaket.

  > **När du ska använda**: När du vill ge en SSO-upplevelse arbetar du med Diagrambaserade data eller autentiserar domänbaserade användare.
  > 
  > **Kom igång**: Mer information finns i [Azure Active Directory Developer ' s guide](../../active-directory/develop/v1-overview.md).

- **App Service autentisering**: När du väljer App Service som värd för din app får du även inbyggt stöd för autentisering för Azure AD, tillsammans med sociala identitets leverantörer – inklusive Facebook, Google, Microsoft och Twitter.

  > **När du ska använda**: När du vill aktivera autentisering i en App Service-app med hjälp av Azure AD, sociala identitets leverantörer eller båda.
  > 
  > **Kom igång**: Mer information om autentisering i App Service finns [i autentisering och auktorisering i Azure App Service](../../app-service/overview-authentication-authorization.md).

Mer information om rekommenderade säkerhets metoder i Azure finns i [metod tips och mönster för Azure-säkerhet](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Övervakning

Med ditt program igång i Azure måste du kunna övervaka prestanda, titta efter problem och se hur kunderna använder din app. Azure tillhandahåller flera övervaknings alternativ.

-   **Visual Studio-Application Insights**: En Azure-värdbaserad utöknings bar analys tjänst som integreras med Visual Studio för att övervaka dina Live-webbprogram. Det ger dig de data du behöver för att kontinuerligt förbättra prestanda och användbarhet för dina appar, oavsett om de finns i Azure eller inte.

    >**Kom igång**: Följ [Application Insights själv studie kursen](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: En tjänst som hjälper dig att visualisera, fråga, cirkulera, arkivera och agera på de mått och loggar som genereras av din Azure-infrastruktur och-resurser. Övervakaren tillhandahåller de datavyer som visas i Azure Portal och är en enda källa för övervakning av Azure-resurser.
 
    >**Kom igång**: [Kom igång med Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>DevOps-integration

Oavsett om det är etablering av virtuella datorer eller om du publicerar dina webbappar med kontinuerlig integrering, integreras Azure med de flesta populära DevOps-verktygen. Med stöd för verktyg som Jenkins, GitHub, Puppet, chef, TeamCity, Ansible, Azure DevOps och andra kan du arbeta med de verktyg som du redan har och maximera din befintliga upplevelse.

> **Prova nu:** [Prova flera av DevOps](https://azure.microsoft.com/try/devops/)-integreringarna.
> 
> **Kom igång**: Om du vill se DevOps-alternativ för en App Service app, se [kontinuerlig distribution till Azure App Service](../../app-service/deploy-continuous-deployment.md).


## <a name="azure-regions"></a>Azure-regioner

Azure är en global moln plattform som är allmänt tillgänglig i många regioner runtom i världen. När du etablerar en tjänst, ett program eller en virtuell dator i Azure uppmanas du att välja en region som representerar ett särskilt data Center där ditt program körs eller var dina data lagras. Dessa regioner motsvarar vissa platser som publiceras på sidan [Azure-regioner](https://azure.microsoft.com/regions/) .

### <a name="choose-the-best-region-for-your-application-and-data"></a>Välj den bästa regionen för ditt program och dina data

En av fördelarna med att använda Azure är att du kan distribuera dina program till olika data center runtom i världen. Den region som du väljer kan påverka programmets prestanda. Till exempel är det bättre att välja en region som är närmare för de flesta kunder för att minska svars tiden i nätverks förfrågningar. Du kanske också vill välja din region för att uppfylla de juridiska kraven för att distribuera din app i vissa länder/regioner. Det är alltid bästa praxis att lagra program data i samma data Center eller i ett Data Center så nära som möjligt till data centret som är värd för ditt program.

### <a name="multi-region-apps"></a>Appar med flera regioner

Även om det är osannolikt är det inte omöjligt för ett helt data Center att gå offline på grund av en händelse som en natur katastrof eller ett Internet haveri. Vi rekommenderar att du är värd för viktiga affärs program i mer än ett Data Center för att få maximal tillgänglighet. Att använda flera regioner kan också minska svars tiden för globala användare och ge ytterligare möjligheter till flexibilitet vid uppdatering av program.

Vissa tjänster, till exempel virtuella datorer och App Services, använder [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) för att möjliggöra stöd för flera regioner med redundans mellan regioner för att stödja företags program med hög tillgänglighet. Ett exempel finns i [referens arkitektur för Azure: Kör ett webb program i flera regioner](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**När du ska använda**: När du har företags program och program med hög tillgänglighet som drar nytta av redundans och replikering.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hur gör jag för att hantera mina program och projekt?

Azure innehåller en omfattande uppsättning upplevelser som du kan använda för att skapa och hantera dina Azure-resurser, program och projekt – både program mässigt och i [Azure Portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Kommando rads gränssnitt och PowerShell

Azure tillhandahåller två sätt att hantera dina program och tjänster från kommando raden med hjälp av bash, Terminal, kommando tolken eller kommando rads verktyget som du väljer. Vanligt vis kan du utföra samma uppgifter från kommando raden som i Azure Portal, till exempel skapa och konfigurera virtuella datorer, virtuella nätverk, webbappar och andra tjänster.

-   [Kommando rads gränssnitt för Azure (CLI)](../../xplat-cli-install.md): Gör att du kan ansluta till en Azure-prenumeration och program olika uppgifter mot Azure-resurser från kommando raden.

-   [Azure PowerShell](../../powershell-install-configure.md): Innehåller en uppsättning moduler med cmdletar som gör att du kan hantera Azure-resurser med hjälp av Windows PowerShell.

### <a name="azure-portal"></a>Azure Portal

Azure Portal är ett webbaserat program som du kan använda för att skapa, hantera och ta bort Azure-resurser och-tjänster. Azure Portal finns på <https://portal.azure.com>. Den innehåller en anpassningsbar instrument panel, verktyg för att hantera Azure-resurser och åtkomst till prenumerations inställningar och fakturerings information. Mer information finns i [Översikt över Azure Portal](../../azure-portal-overview.md).

### <a name="rest-apis"></a>REST API:er

Azure bygger på en uppsättning REST API: er som stöder Azure Portal gränssnittet. De flesta av dessa REST API: er stöds också för att låta dig program mässigt etablera och hantera dina Azure-resurser och-program från valfri enhet med Internet. En fullständig uppsättning REST API-dokumentation finns i [Azure rest SDK](https://docs.microsoft.com/rest/api/)-referensen.

### <a name="apis"></a>API:er

Förutom REST-API: er kan många Azure-tjänster också hantera resurser från dina program program mässigt med hjälp av plattformsspecifika Azure-SDK: er, inklusive SDK: er för följande utvecklings plattformar:

-   [NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/azure/go)

Tjänster som [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) och [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) tillhandahålla SDK: er på klient sidan så att du kan komma åt tjänster från webb-och mobilappar.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Att köra din app på Azure innebär troligen att du arbetar med flera Azure-tjänster, som alla följer samma livs cykel och kan betraktas som en logisk enhet. Till exempel kan en webbapp använda Web Apps, SQL Database, lagring, Azure cache för Redis och Azure Content Delivery Network-tjänster. Med [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) kan du arbeta med resurserna i ditt program som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i en enda, koordinerad åtgärd.

Förutom att logiskt gruppera och hantera relaterade resurser, innehåller Azure Resource Manager distributions funktioner som gör att du kan anpassa distributionen och konfigurationen av relaterade resurser. Med hjälp av Resource Manager kan du till exempel distribuera och konfigurera ett program som består av flera virtuella datorer, en belastningsutjämnare och en Azure SQL-databas som en enskild enhet.

Du utvecklar dessa distributioner med hjälp av en Azure Resource Manager mall, som är ett JSON-formaterat dokument. Med mallar kan du definiera en distribution och hantera dina program med hjälp av deklarativ mallar i stället för skript. Dina mallar kan användas i olika miljöer, till exempel testning, mellanlagring och produktion. Med hjälp av mallar kan du till exempel lägga till en knapp i en GitHub-lagrings platsen som distribuerar koden i lagrings platsen till en uppsättning med Azure-tjänster med ett enda klick.

> **När du ska använda**: Använd Resource Manager-mallar när du vill ha en mall baserad distribution för din app som du kan hantera program mässigt med hjälp av REST API: er, Azure CLI och Azure PowerShell.
> 
> **Kom igång**: Information om hur du kommer igång med mallar finns i [redigera Azure Resource Manager mallar](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Förstå konton, prenumerationer och fakturering

Som utvecklare vill vi gå in i koden och försöka komma igång så snabbt som möjligt med att köra våra program. Vi vill gärna att du ska börja arbeta i Azure så enkelt som möjligt. Azure erbjuder en [kostnads fri utvärderings version](https://azure.microsoft.com/free/)för att hjälpa till att göra det enkelt. Vissa tjänster har även funktionen "Testa gratis", t. ex. [Azure App Service](https://tryappservice.azure.com/), som inte kräver att du ens skapar ett konto. Så roligt som det är att koda och distribuera ditt program till Azure, är det också viktigt att ta en stund att förstå hur Azure fungerar från en synvinkel från användar konton, prenumerationer och fakturering.

### <a name="what-is-an-azure-account"></a>Vad är ett Azure-konto?

För att kunna skapa eller arbeta med en Azure-prenumeration måste du ha ett Azure-konto. Ett Azure-konto är bara en identitet i Azure AD eller i en katalog, till exempel en arbets-eller skol organisation som är betrodd av Azure AD. Om du inte tillhör en sådan organisation kan du alltid skapa en prenumeration med ditt Microsoft-konto, som är betrott av Azure AD. Mer information om hur du integrerar lokala Windows Server-Active Directory med Azure AD finns i [integrera dina lokala identiteter med Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Alla Azure-prenumerationer har en förtroenderelation med en Azure AD-instans. Det innebär att den litar på den katalogen för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma katalog, men en prenumeration litar bara på en katalog. Mer information finns i [hur Azure-prenumerationer är associerade med Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Förutom att definiera enskilda Azure-konto identiteter, även kallade *användare*, kan du även definiera *grupper* i Azure AD. Att skapa användar grupper är ett bra sätt att hantera åtkomst till resurser i en prenumeration med hjälp av rollbaserad åtkomst kontroll (RBAC). Information om hur du skapar grupper finns [i skapa en grupp i Azure Active Directory för hands version](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Du kan också skapa och hantera grupper med [hjälp av PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Hantera prenumerationer

En prenumeration är en logisk gruppering av Azure-tjänster som är länkad till ett Azure-konto. Ett enda Azure-konto kan innehålla flera prenumerationer. Faktureringen för Azure-tjänster görs per prenumeration. En lista över tillgängliga prenumerations erbjudanden per typ finns i [Microsoft Azure erbjudande information](https://azure.microsoft.com/support/legal/offer-details/). Azure-prenumerationer har en konto administratör som har fullständig kontroll över prenumerationen och en tjänst administratör som har kontroll över alla tjänster i prenumerationen. Information om klassiska prenumerations administratörer finns i [lägga till eller ändra Azure-prenumerations administratörer](../../billing/billing-add-change-azure-subscription-administrator.md). Förutom administratörer kan enskilda konton beviljas detaljerad kontroll över Azure-resurser med hjälp av [rollbaserad åtkomst kontroll (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Resursgrupper

När du etablerar nya Azure-tjänster gör du det i en specifik prenumeration. Enskilda Azure-tjänster, som även kallas resurser, skapas i kontexten för en resurs grupp. Resurs grupper gör det enklare att distribuera och hantera dina programs resurser. En resurs grupp ska innehålla alla resurser för ditt program som du vill arbeta med som en enhet. Du kan flytta resurser mellan resurs grupper och till och med olika prenumerationer. Mer information om hur du flyttar resurser finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../../resource-group-move-resources.md).

Azure Resource Explorer är ett bra verktyg för visualisering av de resurser som du redan har skapat i din prenumeration. Mer information finns i [använda Azure Resource Explorer för att visa och ändra resurser](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Bevilja åtkomst till resurser

När du tillåter åtkomst till Azure-resurser är det alltid en bra idé att ge användarna den minsta behörighet som krävs för att utföra en specifik uppgift.

- **Rollbaserad åtkomst kontroll (RBAC)** : I Azure kan du bevilja åtkomst till användar konton (huvud konton) vid en angiven omfattning: prenumeration, resurs grupp eller enskilda resurser. Med RBAC kan du distribuera en uppsättning resurser till en resurs grupp och bevilja behörigheter till en speciell användare eller grupp. Du kan också begränsa åtkomsten till de resurser som tillhör mål resurs gruppen. Du kan också bevilja åtkomst till en enskild resurs, till exempel en virtuell dator eller ett virtuellt nätverk. Om du vill bevilja åtkomst tilldelar du en roll till användaren, gruppen eller tjänstens huvud namn. Det finns många fördefinierade roller och du kan också definiera egna anpassade roller. Mer information finns i [Vad är rollbaserad åtkomst kontroll (RBAC)?](../../role-based-access-control/overview.md).

  > **När du ska använda**: När du behöver detaljerade åtkomst hantering för användare och grupper eller när du behöver göra en användare till ägare av en prenumeration.
  > 
  > **Kom igång**: Läs mer i [Hantera åtkomst med RBAC och Azure Portal](../../role-based-access-control/role-assignments-portal.md).

- **Tjänst huvud objekt**: Förutom att ge åtkomst till användarens huvud namn och grupper kan du ge samma åtkomst till ett huvud namn för tjänsten.

  > **När du ska använda**: När du program mässigt hanterar Azure-resurser eller beviljar åtkomst för program. Mer information finns i [skapa Active Directory-program och tjänstens huvud namn](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Tags

Med Azure Resource Manager kan du tilldela anpassade taggar till enskilda resurser. Taggar, som är nyckel/värde-par, kan vara användbara när du behöver organisera resurser för fakturering eller övervakning. Taggar ger dig ett sätt att spåra resurser över flera resurs grupper. Du kan tilldela Taggar i portalen i Azure Resource Manager-mallen eller program mässigt genom att använda REST API, Azure CLI eller PowerShell. Du kan tilldela flera taggar till varje resurs. Mer information finns i [använda taggar för att ordna dina Azure-resurser](../../resource-group-using-tags.md).

### <a name="billing"></a>Fakturering

I flytten från lokal data behandling till molnbaserade tjänster är det viktigt att spåra och uppskatta tjänst användning och relaterade kostnader. Det är viktigt att kunna uppskatta vilka nya resurser som kostar att köras varje månad. Du måste också kunna projicera hur faktureringen ser ut under en månad baserat på de aktuella utgifterna.

#### <a name="get-resource-usage-data"></a>Hämta resursanvändnings data

Azure tillhandahåller en uppsättning fakturerings REST-API: er som ger åtkomst till resursförbrukning och metadatainformation för Azure-prenumerationer. Dessa API:er för fakturering ger dig möjlighet att förutse och hantera Azure-kostnader. Du kan spåra och analysera utgifter i steg om varje timme, skapa utgifts aviseringar och förutsäga framtida fakturering baserat på aktuella användnings trender.

>**Kom igång**: Mer information om hur du använder API:er för fakturering finns i [Översikt över Azure fakturerings användning och ratecard API: er](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Förutsäg framtida kostnader

Även om det är svårt att uppskatta kostnaderna i förväg, har Azure en [pris kalkylator](https://azure.microsoft.com/pricing/calculator/) som du kan använda när du beräknar kostnaden för distribuerade resurser. Du kan också använda fakturerings bladet i portalen och fakturerings REST-API: er för att beräkna framtida kostnader baserat på aktuell förbrukning.

>**Kom igång**: Se [Översikt över Azure fakturerings användning och ratecard API: er](../../billing-usage-rate-card-overview.md).
