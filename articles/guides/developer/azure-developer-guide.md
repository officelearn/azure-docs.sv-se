---
title: Komma igång guide för utvecklare på Azure | Microsoft-dokument
description: Den här artikeln innehåller viktig information för utvecklare som vill komma igång med Microsoft Azure-plattformen för deras utvecklingsbehov.
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
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: d74fd2e3f6b5cc090c6313aba67a1e139c713b85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245010"
---
# <a name="get-started-guide-for-azure-developers"></a>Komma igång-guide för Azure-utvecklare

## <a name="what-is-azure"></a>Vad är Azure?

Azure är en komplett molnplattform som kan vara värd för dina befintliga program och effektivisera ny programutveckling. Azure kan även förbättra lokala program. Azure integrerar de molntjänster som du behöver för att utveckla, testa, distribuera och hantera dina program, samtidigt som du drar nytta av effektiviteten i molnbaserad databehandling.

Genom att vara värd för dina program i Azure kan du börja små och enkelt skala ditt program när kundens efterfrågan växer. Azure erbjuder också den tillförlitlighet som behövs för program med hög tillgänglighet, även inklusive redundans mellan olika regioner. [Med Azure-portalen](https://portal.azure.com) kan du enkelt hantera alla dina Azure-tjänster. Du kan också hantera dina tjänster programmässigt med hjälp av tjänstspecifika API:er och mallar.

Den här guiden är en introduktion till Azure-plattformen för programutvecklare. Det ger vägledning och vägledning som du behöver för att börja skapa nya program i Azure eller migrera befintliga program till Azure.

## <a name="where-do-i-start"></a>Vad ska jag börja med?

Med alla tjänster som Azure erbjuder kan det vara en skrämmande uppgift att ta reda på vilka tjänster du behöver för att stödja din lösningsarkitektur. I det här avsnittet beskrivs de Azure-tjänster som utvecklare ofta använder. En lista över alla Azure-tjänster finns i [Azure-dokumentationen](../../index.yml).

Först måste du bestämma hur du ska vara värd för ditt program i Azure. Behöver du hantera hela infrastrukturen som en virtuell dator (VM). Kan du använda de plattformshanteringsfunktioner som Azure tillhandahåller? Kanske behöver du en serverlös ram för att vara värd för körning av kod bara?

Ditt program behöver molnlagring, vilket Azure innehåller flera alternativ för. Du kan dra nytta av Azures företagsautentisering. Det finns också verktyg för molnbaserad utveckling och övervakning, och de flesta värdtjänster erbjuder DevOps-integrering.

Nu ska vi titta på några av de specifika tjänster som vi rekommenderar att undersöka för dina program.

### <a name="application-hosting"></a>Programhosting

Azure tillhandahåller flera molnbaserade beräkningserbjudanden för att köra ditt program så att du inte behöver oroa dig för infrastrukturinformationen. Du kan enkelt skala upp eller skala ut dina resurser när programanvändningen växer.

Azure erbjuder tjänster som stöder dina programutvecklings- och värdbehov. Azure tillhandahåller infrastruktur som en tjänst (IaaS) för att ge dig full kontroll över din programvärd. Azures PaaS-erbjudanden (Platform as a Service) tillhandahåller de fullständigt hanterade tjänster som behövs för att driva dina appar. Det finns även sann serverlös hosting i Azure där allt du behöver göra är att skriva din kod.

![Alternativ för hosting av Azure-program](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure Apptjänst

När du vill ha den snabbaste sökvägen för att publicera dina webbaserade projekt bör du överväga Azure App Service. App Service gör det enkelt att utöka dina webbappar för att stödja dina mobila klienter och publicera lätt förbrukade REST-API:er. Den här plattformen tillhandahåller autentisering med hjälp av sociala leverantörer, trafikbaserad automatisk skalning, testning i produktion och kontinuerliga och behållarbaserade distributioner.

Du kan skapa webbappar, tillbakaslut för mobilappar och API-appar.

Eftersom alla tre apptyperna delar App Service-körningen kan du vara värd för en webbplats, stödja mobila klienter och exponera dina API:er i Azure, allt från samma projekt eller lösning. Mer information om App Service finns i [Vad är Azure Web Apps](../../app-service/overview.md).

App Service har utformats med DevOps i åtanke. Den stöder olika verktyg för publicering och kontinuerlig integrationsdistributioner. Dessa verktyg inkluderar GitHub webhooks, Jenkins, Azure DevOps, TeamCity och andra.

Du kan migrera dina befintliga program till App Service med hjälp av [onlinemigreringsverktyget](https://appmigration.microsoft.com/).

> **När du ska använda**: Använd App Service när du migrerar befintliga webbprogram till Azure och när du behöver en fullständigt hanterad värdplattform för dina webbappar. Du kan också använda App Service när du behöver stödja mobila klienter eller exponera REST-API:er med din app.
>
> **Kom igång:** App Service gör det enkelt att skapa och distribuera din första [webbapp,](../../app-service/app-service-web-get-started-dotnet.md) [mobilapp](../../app-service-mobile/app-service-mobile-ios-get-started.md)eller [API-app](../../app-service/app-service-web-tutorial-rest-api.md).
>
> **Prova nu:** Med App Service kan du etablera en kortlivad app för att prova plattformen utan att behöva registrera dig för ett Azure-konto. Prova plattformen och [skapa din Azure App Service-app](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Som en IaaS-provider (Infrastructure as a Service) kan du distribuera till eller migrera ditt program till antingen Virtuella Datorer i Windows eller Linux. Tillsammans med Azure Virtual Network stöder Virtuella Azure-datorer distributionen av virtuella Windows- eller Linux-datorer till Azure. Med virtuella datorer har du total kontroll över datorns konfiguration. När du använder virtuella datorer ansvarar du för alla serverprogramvarans installations-, konfigurations-, underhålls- och operativsystemkorrigeringar.

På grund av den kontrollnivå som du har med virtuella datorer kan du köra ett brett utbud av serverarbetsbelastningar på Azure som inte passar in i en PaaS-modell. Dessa arbetsbelastningar omfattar databasservrar, Windows Server Active Directory och Microsoft SharePoint. Mer information finns i dokumentationen för virtuella datorer för [antingen Linux](/azure/virtual-machines/linux/) eller [Windows](/azure/virtual-machines/windows/).

> **När du ska använda**: Använd virtuella datorer när du vill ha fullständig kontroll över programinfrastrukturen eller migrera lokala programarbetsbelastningar till Azure utan att behöva göra ändringar.
>
> **Kom igång:** Skapa en [virtuell Linux-dator](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) eller [Windows VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) från Azure-portalen.

#### <a name="azure-functions-serverless"></a>Azure-funktioner (serverlösa)

Hellre än att oroa sig för att bygga ut och hantera ett helt program eller infrastruktur för att köra din kod, tänk om du bara kunde skriva din kod och få den att köras som svar på händelser eller på ett schema?  [Azure Functions](../../azure-functions/functions-overview.md) är ett "serverlöst" erbjudande som låter dig skriva precis den kod du behöver. Med Functions kan du utlösa kodkörning med HTTP-begäranden, webhooks, molntjänsthändelser eller enligt ett schema. Du kan koda på ditt utvecklingsspråk, till exempel C,\#F,\#Node.js, Python eller PHP. Med förbrukningsbaserad fakturering betalar du bara för den tid som koden körs och Azure-skalor efter behov.

> **När du ska använda**: Använd Azure-funktioner när du har kod som utlöses av andra Azure-tjänster, av webbaserade händelser eller enligt ett schema. Du kan också använda Funktioner när du inte behöver omkostnaderna för ett fullständigt värdprojekt eller när du bara vill betala för den tid som koden körs. Mer information finns i [Översikt över Azure-funktioner](../../azure-functions/functions-overview.md).
>
> **Kom igång:** Följ snabbstartshandledningen Funktioner för att [skapa din första funktion](../../azure-functions/functions-create-first-azure-function.md) från portalen.
>
> **Prova nu:** Med Azure Functions kan du köra din kod utan att behöva registrera dig för ett Azure-konto. Prova nu och [skapa din första Azure-funktion](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric är en distribuerad systemplattform. Den här plattformen gör det enkelt att bygga, paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Det ger också omfattande funktioner för programhantering, till exempel:

* Etablering
* Distribuera
* Övervakning
* Uppgradering/korrigering
* Ta bort

Appar, som körs på en delad pool av datorer, kan börja i liten skala till hundratals eller tusentals datorer efter behov.

Service Fabric stöder WebAPI med Open Web Interface för .NET (OWIN) och ASP.NET Core. Det ger SDKs för att bygga tjänster på Linux i både .NET Core och Java. Mer information om Service Fabric finns i [dokumentationen för Service Fabric](https://docs.microsoft.com/azure/service-fabric/).

> **När du ska använda:** Service Fabric är ett bra val när du skapar ett program eller skriver om ett befintligt program för att använda en mikrotjänstarkitektur. Använd Service Fabric när du behöver mer kontroll över, eller direkt åtkomst till, den underliggande infrastrukturen.
>
> **Kom igång:** [Skapa ditt första Azure Service Fabric-program](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Förbättra dina program med Azure-tjänster

Tillsammans med programhosting tillhandahåller Azure tjänsteerbjudanden som kan förbättra funktionaliteten. Azure kan också förbättra utvecklingen och underhållet av dina program, både i molnet och lokalt.

#### <a name="hosted-storage-and-data-access"></a>Värdlagring och dataåtkomst

De flesta program måste lagra data, så hur du än väljer att vara värd för ditt program i Azure, överväga en eller flera av följande lagrings- och datatjänster.

- **Azure Cosmos DB**: En globalt distribuerad databastjänst med flera modeller. Med den här databasen kan du elastiskt skala dataflöde och lagring i valfritt antal geografiska regioner med ett omfattande serviceavtal.

  > **När du ska använda:** När ditt program behöver dokument-, tabell- eller diagramdatabaser, inklusive MongoDB-databaser, med flera väldefinierade konsekvensmodeller.
  >
  > **Kom igång:** [Skapa en Azure Cosmos DB-webbapp](../../cosmos-db/create-sql-api-dotnet.md). Om du är MongoDB-utvecklare läser du [Skapa en MongoDB-webbapp med Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Azure Storage**: Erbjuder varaktig, högtillgänglig lagring för blobbar, köer, filer och andra typer av icke-relationella data. Lagring ger lagringsgrunden för virtuella datorer.

  > **När du ska använda**: När appen lagrar icke-relationella data, till exempel nyckelvärdespar (tabeller), blobbar, filresurser eller meddelanden (köer).
  >
  > **Kom igång**: Välj bland någon av dessa typer lagring: [blobbar,](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) [tabeller,](../../cosmos-db/table-storage-how-to-use-dotnet.md) [köer](../../storage/queues/storage-dotnet-how-to-use-queues.md)eller [filer](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL Database**: En Azure-baserad version av Microsoft SQL Server-motorn för lagring av relationstabelldata i molnet. SQL Database ger förutsägbar prestanda, skalbarhet utan driftstopp, affärskontinuitet och dataskydd.

  > **När du ska använda**: När ditt program kräver datalagring med referensintegritet, transaktionsstöd och stöd för TSQL-frågor.
  >
  > **Kom igång:** [Skapa en SQL-databas på några minuter med hjälp av Azure-portalen](../../sql-database/sql-database-get-started.md).


Du kan använda [Azure Data Factory](../../data-factory/introduction.md) för att flytta befintliga lokala data till Azure. Om du inte är redo att flytta data till molnet kan du ansluta din App Service-värdapp till lokala resurser med [hybridanslutningar](../../app-service/app-service-hybrid-connections.md) i Azure App Service. Du kan också ansluta till Azure-data och lagringstjänster från dina lokala program.

#### <a name="docker-support"></a>Support för Docker

Docker-behållare, en form av OS-virtualisering, gör att du kan distribuera program på ett mer effektivt och förutsägbart sätt. Ett containeriserat program fungerar i produktion på samma sätt som på dina utvecklings- och testsystem. Du kan hantera behållare med hjälp av standarddockerverktyg. Du kan använda dina befintliga kunskaper och populära verktyg med öppen källkod för att distribuera och hantera behållarbaserade program på Azure.

Azure innehåller flera sätt att använda behållare i dina program.

- **Azure Docker VM-tillägg:** Låter dig konfigurera din virtuella dator med Docker-verktyg för att fungera som dockervärd.

  > **När du ska använda**: När du vill generera konsekventa behållardistributioner för dina program på en virtuell dator, eller när du vill använda [Docker Compose](https://docs.docker.com/compose/overview/).
  >
  > **Kom igång:** [Skapa en Docker-miljö i Azure med hjälp av tillägget Docker VM](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Azure Kubernetes Service**: Låter dig skapa, konfigurera och hantera ett kluster av virtuella datorer som är förkonfigurerade för att köra behållarprogram. Mer information om Azure Kubernetes-tjänsten finns i [azure kubernetes service-introduktion](../../aks/intro-kubernetes.md).

  > **När du ska använda**: När du behöver skapa produktionsklara, skalbara miljöer som tillhandahåller ytterligare schemaläggnings- och hanteringsverktyg, eller när du distribuerar ett Docker Swarm-kluster.
  >
  > **Kom igång:** [Distribuera ett Kubernetes-tjänstkluster](../../aks/tutorial-kubernetes-deploy-cluster.md).

- **Docker Machine:** Låter dig installera och hantera en Docker-motor på virtuella värdar med hjälp av docker-maskinkommandon.

  >**När du ska använda**: När du snabbt behöver prototyp en app genom att skapa en enda Docker-värd.

- **Anpassad Docker-avbildning för App Service:** Gör att du kan använda Docker-behållare från ett behållarregister eller en kundbehållare när du distribuerar en webbapp på Linux.

  > **När du ska använda**: När du distribuerar en webbapp på Linux till en Docker-avbildning.
  >
  > **Kom igång:** [Använd en anpassad Docker-avbildning för App Service på Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Autentisering

Det är viktigt att inte bara veta vem som använder dina program, men också för att förhindra obehörig åtkomst till dina resurser. Azure innehåller flera sätt att autentisera dina appklienter.

- **Azure Active Directory (Azure AD)**: Microsofts multitenant, molnbaserade identitets- och åtkomsthanteringstjänst. Du kan lägga till SSO (Single-sign on) i dina program genom att integrera med Azure AD. Du kan komma åt katalogegenskaper med hjälp av Azure AD Graph API direkt eller Microsoft Graph API. Du kan integrera med Azure AD-stöd för OAuth2.0-auktoriseringsramverket och Open ID Connect med hjälp av inbyggda HTTP/REST-slutpunkter och Azure AD-autentiseringsbibliotek för flera plattformar.

  > **När du ska använda**: När du vill ge en SSO-upplevelse kan du arbeta med Graph-baserade data eller autentisera domänbaserade användare.
  >
  > **Kom igång:** Mer information finns i [Azure Active Directory-utvecklarens guide](../../active-directory/develop/v2-overview.md).

- **Apptjänstautentisering**: När du väljer AppTjänst som värd för din app får du också inbyggt autentiseringsstöd för Azure AD, tillsammans med leverantörer av sociala identiteter – inklusive Facebook, Google, Microsoft och Twitter.

  > **När du ska använda**: När du vill aktivera autentisering i en App Service-app med hjälp av Azure AD, leverantörer av social identitet eller båda.
  >
  > **Kom igång:** Mer information om autentisering i App Service finns [i Autentisering och auktorisering i Azure App Service](../../app-service/overview-authentication-authorization.md).

Mer information om metodtips för säkerhet i Azure finns i [Metodtips och mönster för Azure-säkerhet](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Övervakning

Med ditt program igång i Azure måste du övervaka prestanda, hålla utkik efter problem och se hur kunderna använder din app. Azure innehåller flera övervakningsalternativ.

-   **Application Insights**: En Azure-värdförd utökningsbar analystjänst som integreras med Visual Studio för att övervaka dina live-webbprogram. Det ger dig de data som du behöver för att förbättra prestanda och användbarhet för dina appar kontinuerligt. Den här förbättringen uppstår oavsett om du är värd för dina program på Azure eller inte.

    >**Kom igång:** Följ [självstudiekursen Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: En tjänst som hjälper dig att visualisera, fråga, dirigera, arkivera och agera på de mått och loggar som du genererar med din Azure-infrastruktur och dina resurser. Monitor är en enda källa för övervakning av Azure-resurser och tillhandahåller de datavyer som visas i Azure-portalen.

    >**Kom igång:** [Kom igång med Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>DevOps-integrering

Oavsett om det handlar om att etablera virtuella datorer eller publicera dina webbappar med kontinuerlig integrering integreras Azure med de flesta populära DevOps-verktygen. Du kan arbeta med de verktyg som du redan har och maximera din befintliga upplevelse med stöd för verktyg som:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity (Svenska)
* Ansible
* Azure DevOps

> **Kom igång:** Information om hur du ser DevOps-alternativ för en App Service-app finns i [Kontinuerlig distribution till Azure App Service](../../app-service/deploy-continuous-deployment.md).
>
> **Prova nu:** [Prova flera av DevOps-integreringarna](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Azure-regioner

Azure är en global molnplattform som är allmänt tillgänglig i många regioner runt om i världen. När du etablerar en tjänst, ett program eller en virtuell dator i Azure ombeds du att välja en region. Den här regionen representerar ett specifikt datacenter där ditt program körs eller där dina data lagras. Dessa regioner motsvarar specifika platser som publiceras på sidan [Azure-regioner.](https://azure.microsoft.com/regions/)

### <a name="choose-the-best-region-for-your-application-and-data"></a>Välj den bästa regionen för ditt program och dina data

En av fördelarna med att använda Azure är att du kan distribuera dina program till olika datacenter runt om i världen. Den region som du väljer kan påverka programmets prestanda. Det är till exempel bättre att välja en region som ligger närmare de flesta av dina kunder för att minska svarstiden i nätverksbegäranden. Du kanske också vill välja din region för att uppfylla de juridiska kraven för att distribuera din app i vissa länder/regioner. Det är alltid en bra idé att lagra programdata i samma datacenter eller i ett datacenter så nära det datacenter som är värd för ditt program.

### <a name="multi-region-apps"></a>Appar med flera regioner

Även om det är osannolikt, är det inte omöjligt för ett helt datacenter att gå offline på grund av en händelse som en naturkatastrof eller Internet-fel. Det är en bra idé att vara värd för viktiga affärsprogram i mer än ett datacenter för att ge maximal tillgänglighet. Om du använder flera regioner kan du också minska svarstiden för globala användare och ge ytterligare möjligheter till flexibilitet vid uppdatering av program.

Vissa tjänster, till exempel Virtual Machine och App Services, använder [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) för att aktivera support med flera regioner med redundans mellan regioner för att stödja företagsprogram med hög tillgänglighet. Ett exempel finns i [Azure-referensarkitektur: Kör ett webbprogram i flera regioner](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**När du ska använda**: När du har program för företags- och hög tillgänglighet som drar nytta av redundans och replikering.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hur hanterar jag mina program och projekt?

Azure innehåller en omfattande uppsättning upplevelser som du kan skapa och hantera dina Azure-resurser, program och projekt – både programmässigt och i [Azure-portalen](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Kommandoradsgränssnitt och PowerShell

Azure erbjuder två sätt att hantera dina program och tjänster från kommandoraden. Du kan använda verktyg som Bash, Terminal, kommandotolken eller kommandoradsverktyget. Vanligtvis kan du utföra samma uppgifter från kommandoraden som i Azure-portalen, till exempel skapa och konfigurera virtuella datorer, virtuella nätverk, webbappar och andra tjänster.

-   [Azure Command-Line Interface (CLI):](../../xplat-cli-install.md)Låter dig ansluta till en Azure-prenumeration och programmera olika uppgifter mot Azure-resurser från kommandoraden.

-   [Azure PowerShell](../../powershell-install-configure.md): Innehåller en uppsättning moduler med cmdlets som gör att du kan hantera Azure-resurser med hjälp av Windows PowerShell.

### <a name="azure-portal"></a>Azure Portal

[Azure-portalen](https://portal.azure.com) är ett webbaserat program. Du kan använda Azure-portalen för att skapa, hantera och ta bort Azure-resurser och -tjänster. Den innehåller:

* En konfigurerbar instrumentpanel
* Verktyg för hantering av Azure-resurser
* Tillgång till prenumerationsinställningar och faktureringsinformation. Mer information finns i [Översikt över Azure-portalen](../../azure-portal-overview.md).

### <a name="rest-apis"></a>REST API:er

Azure bygger på en uppsättning REST API:er som stöder Azure-portalens användargränssnitt. De flesta av dessa REST-API:er stöds också för att låta dig programmera och hantera dina Azure-resurser och -program från alla Internetaktiverade enheter. Den fullständiga uppsättningen REST API-dokumentation finns i [Azure REST SDK-referensen](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>API:er

Tillsammans med REST-API:er kan du också programmässigt hantera resurser från dina program med hjälp av plattformsspecifika Azure SDK:er, inklusive SDK:er för följande utvecklingsplattformar:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [Php](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Kör](https://docs.microsoft.com/azure/go)

Tjänster som [mobilappar](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) och [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) tillhandahåller SDK:er på klientsidan så att du kan komma åt tjänster från webb- och mobilappar.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Att köra din app på Azure innebär sannolikt att du arbetar med flera Azure-tjänster. Dessa tjänster följer samma livscykel och kan ses som en logisk enhet. En webbapp kan till exempel använda webbappar, SQL-databas, lagring, Azure-cache för Redis och Azure Content Delivery Network-tjänster. [Med Azure Resource Manager](../../azure-resource-manager/management/overview.md) kan du arbeta med resurserna i ditt program som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i en enda samordnad åtgärd.

Tillsammans med logiskt gruppering och hantering av relaterade resurser innehåller Azure Resource Manager distributionsfunktioner som gör att du kan anpassa distributionen och konfigurationen av relaterade resurser. Du kan till exempel använda Resurshanteraren distribuera och konfigurera ett program. Det här programmet kan bestå av flera virtuella datorer, en belastningsutjämning och en Azure SQL-databas som en enda enhet.

Du utvecklar dessa distributioner med hjälp av en Azure Resource Manager-mall, som är ett JSON-formaterat dokument. Med mallar kan du definiera en distribution och hantera dina program med hjälp av deklarativa mallar i stället för skript. Mallarna kan fungera för olika miljöer, till exempel testning, mellanlagring och produktion. Du kan till exempel använda mallar för att lägga till en knapp i en GitHub-repo som distribuerar koden i osiningen till en uppsättning Azure-tjänster med ett enda klick.

> **När du ska använda**: Använd Resource Manager-mallar när du vill ha en mallbaserad distribution för din app som du kan hantera programmässigt med hjälp av REST API:er, Azure CLI och Azure PowerShell.
>
> **Kom igång:** Information om hur du kommer igång med mallar finns i [Skapa Azure Resource Manager-mallar](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Förstå konton, prenumerationer och fakturering

Som utvecklare gillar vi att dyka rakt in i koden och försöka komma igång så fort som möjligt med att få våra program att köras. Vi vill verkligen uppmuntra dig att börja arbeta i Azure så enkelt som möjligt. För att göra det enkelt erbjuder Azure en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/). Vissa tjänster har till och med funktionen "Prova gratis", till exempel [Azure App Service](https://tryappservice.azure.com/), som inte kräver att du ens skapar ett konto. Hur roligt det än är att gå in i kodning och distribuera ditt program till Azure, är det också viktigt att ta lite tid att förstå hur Azure fungerar. Specifikt bör du förstå hur det fungerar ur en synvinkel av användarkonton, prenumerationer och fakturering.

### <a name="what-is-an-azure-account"></a>Vad är ett Azure-konto?

Om du vill skapa eller arbeta med en Azure-prenumeration måste du ha ett Azure-konto. Ett Azure-konto är helt enkelt en identitet i Azure AD eller i en katalog, till exempel en arbets- eller skolorganisation, som Azure AD litar på. Om du inte tillhör en sådan organisation kan du alltid skapa en prenumeration med hjälp av ditt Microsoft-konto, som är betrodd av Azure AD. Mer information om hur du integrerar lokal Windows Server Active Directory med Azure AD finns i [Integrera dina lokala identiteter med Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Alla Azure-prenumerationer har en förtroenderelation med en Azure AD-instans. Det innebär att den litar på den katalogen för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma katalog, men en prenumeration litar bara på en katalog. Mer information finns i [Hur Azure-prenumerationer associeras med Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Förutom att definiera enskilda Azure-kontoidentiteter, även kallade *användare,* kan du definiera *grupper* i Azure AD. Att skapa användargrupper är ett bra sätt att hantera åtkomst till resurser i en prenumeration med hjälp av rollbaserad åtkomstkontroll (RBAC). Mer information om hur du skapar grupper finns [i Skapa en grupp i förhandsversionen av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Du kan också skapa och hantera grupper med hjälp av [PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Hantera dina prenumerationer

En prenumeration är en logisk gruppering av Azure-tjänster som är kopplad till ett Azure-konto. Ett enda Azure-konto kan innehålla flera prenumerationer. Fakturering för Azure-tjänster sker per prenumeration. En lista över tillgängliga prenumerationserbjudanden efter typ finns i [Microsoft Azure Offer Details](https://azure.microsoft.com/support/legal/offer-details/). Azure-prenumerationer har en kontoadministratör som har full kontroll över prenumerationen. De har också en tjänstadministratör som har kontroll över alla tjänster i prenumerationen. Information om klassiska prenumerationsadministratörer finns i [Lägga till eller ändra Azure-prenumerationsadministratörer](../../cost-management-billing/manage/add-change-subscription-administrator.md). Enskilda konton kan beviljas detaljerad kontroll över Azure-resurser med hjälp av [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Resursgrupper

När du etablerar nya Azure-tjänster gör du det i en viss prenumeration. Enskilda Azure-tjänster, som också kallas resurser, skapas i kontexten för en resursgrupp. Resursgrupper gör det enklare att distribuera och hantera programmets resurser. En resursgrupp bör innehålla alla resurser för ditt program som du vill arbeta med som en enhet. Du kan flytta resurser mellan resursgrupper och till och med till olika prenumerationer. Mer information om hur du flyttar resurser finns i [Flytta resurser till ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Azure Resource Explorer är ett bra verktyg för att visualisera de resurser som du redan har skapat i din prenumeration. Mer information finns i [Använda Azure Resource Explorer för att visa och ändra resurser](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Bevilja åtkomst till resurser

När du tillåter åtkomst till Azure-resurser är det alltid en bra idé att ge användarna den lägsta behörighet som krävs för att utföra en viss uppgift.

- **Rollbaserad åtkomstkontroll (RBAC):** I Azure kan du bevilja åtkomst till användarkonton (huvudnamn) med ett angivet scope: prenumeration, resursgrupp eller enskilda resurser. Med RBAC kan du distribuera resurser till en resursgrupp och bevilja behörigheter till en viss användare eller grupp. Du kan också begränsa åtkomsten till endast de resurser som tillhör målgruppen. Du kan också bevilja åtkomst till en enskild resurs, till exempel en virtuell dator eller ett virtuellt nätverk. Om du vill bevilja åtkomst tilldelar du en roll till användaren, gruppen eller tjänstens huvudnamn. Det finns många fördefinierade roller och du kan också definiera dina egna anpassade roller. Mer information finns i [Vad är rollbaserad åtkomstkontroll (RBAC)?](../../role-based-access-control/overview.md).

  > **När du ska använda**: När du behöver finkornig åtkomsthantering för användare och grupper eller när du behöver göra en användare till ägare av en prenumeration.
  >
  > **Kom igång:** Mer information finns i [Hantera åtkomst med RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md).

- **Huvudobjekt för tjänsten**: Tillsammans med åtkomst till användarens huvudnamn och grupper kan du ge samma åtkomst till ett tjänsthuvudnamn.

  > **När du ska använda**: När du programmässigt hanterar Azure-resurser eller beviljar åtkomst för program. Mer information finns i [Skapa Active Directory-program och tjänsthuvudnamn](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Taggar

Med Azure Resource Manager kan du tilldela anpassade taggar till enskilda resurser. Taggar, som är nyckel-värde par, kan vara till hjälp när du behöver organisera resurser för fakturering eller övervakning. Taggar ger dig ett sätt att spåra resurser över flera resursgrupper. Du kan tilldela taggar på följande sätt:

* I portalen
* I Azure Resource Manager-mallen
* Använda REST API
* Använda Azure CLI
* Använda PowerShell

Du kan tilldela flera taggar till varje resurs. Mer information finns i [Använda taggar för att ordna dina Azure-resurser](../../resource-group-using-tags.md).

### <a name="billing"></a>Fakturering

I övergången från lokal databehandling till molnbaserade tjänster är spårning och uppskattning av tjänstens användning och relaterade kostnader betydande problem. Det är viktigt att uppskatta vad nya resurser kostar att köra på månadsbasis. Du kan också projicera hur faktureringen ser ut för en viss månad baserat på de aktuella utgifterna.

#### <a name="get-resource-usage-data"></a>Hämta resursanvändningsdata

Azure tillhandahåller en uppsättning REST-API:er för fakturering som ger åtkomst till resursförbrukning och metadatainformation för Azure-prenumerationer. Dessa fakturerings-API:er ger dig möjlighet att bättre förutsäga och hantera Azure-kostnader. Du kan spåra och analysera utgifter i timsteg och skapa utgiftsaviseringar. Du kan också förutsäga framtida fakturering baserat på aktuella användningstrender.

>**Kom igång:** Mer information om hur du använder fakturerings-API:erna finns i [översikten över Azure Billing Usage och RateCard API:er](../../cost-management-billing/manage/usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Förutse framtida kostnader

Även om det är svårt att uppskatta kostnaderna i förväg har Azure verktyg som kan hjälpa dig. Den har en [priskalkylator](https://azure.microsoft.com/pricing/calculator/) för att uppskatta kostnaden för distribuerade resurser. Du kan också använda faktureringsresurserna i portalen och REST-API:erna för fakturering för att uppskatta framtida kostnader, baserat på aktuell förbrukning.

>**Kom igång:** Se [översikt över Azure Billing Usage och RateCard API:er](../../cost-management-billing/manage/usage-rate-card-overview.md).
