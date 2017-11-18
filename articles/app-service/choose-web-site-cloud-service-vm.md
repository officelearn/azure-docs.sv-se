---
title: "Jämförelse mellan Azure App Service, virtuella datorer, Service Fabric och Cloud Services | Microsoft Docs"
description: "Lär dig hur du väljer mellan Azure App Service, virtuella datorer, Service Fabric och Cloud Services som värd för webbprogram."
services: app-service\web, virtual-machines, cloud-services
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: 7d346a23-532a-42a9-98a8-23b7286d32a8
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/07/2016
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: bac9169bc41927ef8cf88aee256b2e057ccad4e9
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Jämförelse mellan Azure App Service, virtuella datorer, Service Fabric och Cloud Services
## <a name="overview"></a>Översikt
Azure erbjuder flera sätt att värden webbplatser: [Azure App Service][Azure App Service], [virtuella datorer][Virtual Machines], [Service Fabric] [ Service Fabric], och [molntjänster][Cloud Services]. Den här artikeln hjälper dig att förstå alternativen och fatta rätt beslut för webbprogram.

Azure Apptjänst är det bästa valet för de flesta web apps. Distribution och hantering är integrerade i plattformen, platser kan skalas snabbt för att hantera hög belastningar och inbyggda belastningen belastningsutjämning och traffic manager ger hög tillgänglighet. Du kan flytta befintliga platser till Azure App Service med en [online Migreringsverktyg](https://www.migratetoazure.net/), använda en app med öppen källkod från Web Application Gallery eller skapa en ny plats med framework och -verktyg. Den [WebJobs] [ WebJobs] funktionen gör det lätt att lägga till jobbet Bakgrundsbearbetning till din App Service webbapp.

Service Fabric är bra om du skapar en ny app eller skriva om en befintlig app om du vill använda en arkitektur för mikrotjänster. Appar som körs på en delad pool av datorer, kan du börja litet och växa i massiv skala med hundratals eller tusentals datorer efter behov. Tillståndskänsliga tjänster gör det enkelt att lagra programtillstånd konsekvent och tillförlitligt och Service Fabric hanterar automatiskt tjänsten partitionering, skalning och tillgänglighet för dig.  Service Fabric stöder också WebAPI med Open Web Interface för .NET (OWIN) och ASP.NET Core.  Jämfört med Apptjänst, innehåller Service Fabric också mer kontroll över, eller direkt åtkomst till den underliggande infrastrukturen. Du kan fjärråtkomst till dina servrar eller konfigurera servern startade uppgifter. Molntjänster liknar Service Fabric i kontroll jämfört med användbarhet, men det är nu en äldre service och Service Fabric rekommenderas för utveckling av nya.

Om du har ett befintligt program som kräver betydande ändringar av körs i Apptjänst eller Service Fabric kan du välja virtuella datorer för att förenkla Migrering till molnet. Korrekt kräver konfigurera, skydda och underhålla virtuella datorer dock mer tid och IT-kunskaper jämfört med Azure App Service och Service Fabric. Om du funderar på Azure Virtual Machines, kontrollera att ta hänsyn till det löpande underhållet enklare att korrigering, uppdatera och hantera Virtuella miljön. Virtuella Azure-datorer är Infrastructure-as-a-Service (IaaS), medan App Service och Service Fabric är plattform som en-tjänst (Paas). 

## <a name="features"></a>Jämförelse av funktioner
I följande tabell jämförs funktionerna i Apptjänst, Cloud Services, virtuella datorer och Service Fabric och hjälper dig att fatta det bästa valet. Aktuell information om SLA för varje alternativ Se [Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

| Funktion | Apptjänst (webbprogram) | Molntjänster (webbroller) | Virtuella datorer | Service Fabric | Anteckningar |
| --- | --- | --- | --- | --- | --- |
| Nästan omedelbar distribution |X | | |X |Distribuera ett program eller en uppdatering till en molntjänst eller skapa en virtuell dator tar flera minuter minst; distribuera ett program till en webbapp tar några sekunder. |
| Skala upp till större datorer utan att distribuera om |X | | |X | |
| Web server-instanser Dela innehåll och konfiguration, vilket innebär att du inte distribuera om eller konfigurera om när du skalar. |X | | |X | |
| Distributionsmiljöer med flera (produktion och mellanlagring) |X |X | |X |Service Fabric kan du ha flera miljöer för dina appar eller för att distribuera olika versioner av din app sida-vid-sida. |
| Automatisk hantering av OS |X |X | | |Delvis via korrigering Orchestration program (upp) och fullständigt i framtiden. |
| Växla sömlös plattform (enkelt flytta mellan 32-bitars och 64-bitars) |X |X | | | |
| Distribuera kod med GIT, FTP |X | |X | | |
| Distribuera kod med webbdistribution |X | |X | |Molntjänster stöder användning av webbdistribution för att distribuera uppdateringar till enskilda rollinstanser. Men du kan inte använda den för distribuering av en roll och om du använder webbdistribution för en uppdatering måste du distribuera separat för varje instans av en roll. Flera instanser krävs för att Cloud Service serviceavtalet för produktionsmiljöer. |
| WebMatrix stöd |X | |X | | |
| Åtkomst till tjänster som Service Bus, lagring, SQL-databas |X |X |X |X | |
| Värden webb- eller web services nivå i en arkitektur med flera nivåer |X |X |X |X | |
| Värden mellannivån en arkitektur med flera nivåer |X |X |X |X |App Service web apps enkelt kan vara värd för en REST API-mellannivå och [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) funktion kan vara värd för bakgrundsjobb för bearbetning. Du kan köra WebJobs på en dedikerad webbplats att uppnå oberoende skalbarhet för skiktet. |
| Integrerat stöd för MySQL as a service |X |X | | | |
| Stöd för ASP.NET, klassisk ASP, Node.js, PHP, Python |X |X |X |X |Service Fabric stöder skapandet av en web front-end med [ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) eller så kan du distribuera program (Node.js, Java och så vidare) som en [gäst körbara](../service-fabric/service-fabric-deploy-existing-app.md). |
| Skala ut till flera instanser utan att distribuera om |X |X |X |X |Virtuella datorer kan skala ut till flera instanser, men de tjänster som körs på dem. måste vara skriven för att hantera den här skalbar. Du måste konfigurera en belastningsutjämnare för att vidarebefordra begäranden på alla datorer och skapa en Tillhörighetsgrupp för att förhindra samtidiga omstarter av alla instanser på grund av underhåll eller maskinvarufel. |
| Stöd för SSL |X |X |X |X |SSL för anpassade domännamn stöds bara för Basic och Standard läge för App Service web apps. Information om hur du använder SSL med webbappar finns [konfigurera ett SSL-certifikat för en Azure-webbplats](app-service-web-tutorial-custom-ssl.md). |
| Visual Studio-integration |X |X |X |X | |
| Fjärrfelsökning |X |X |X | | |
| Distribuera kod med TFS |X |X |X |X | |
| Nätverksisolering med [Azure Virtual Network](/azure/virtual-network/) |X |X |X |X |Se även [Azure Websites virtuell nätverksintegration](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| Stöd för [Azure Traffic Manager](/azure/traffic-manager/) |X |X |X |X | |
| Integrerad Slutpunktsövervakning |X |X |X | | |
| Fjärråtkomst till skrivbordet till servrar | |X |X |X | |
| Installera eventuella anpassade MSI | |X |X |X |Service Fabric kan du vara värd för en körbar fil som en [gäst körbara](../service-fabric/service-fabric-deploy-existing-app.md) eller installera en app på virtuella datorer. |
| Möjlighet att definiera/kör uppstart uppgifter | |X |X |X | |
| Kan lyssna på ETW-händelser | |X |X |X | |

## <a name="scenarios"></a>Scenarier och rekommendationer
Här följer några vanliga Programscenarier med rekommendationer om vilka Azure webbvärd alternativet är mest lämpligt för varje.

* [Jag behöver en frontwebb med bakgrund bearbetning och databasen serverdelen för att köra företagsprogram som är integrerad med lokala tillgångar.](#onprem)
* [Jag behöver ett säkert sätt att vara värd för min företagets webbplats som kan skalas väl och erbjudanden globala nå.](#corp)
* [Jag har en IIS6-program som körs på Windows Server 2003.](#iis6)
* [Jag är ett litet företagsägare och jag behöver ett billigt sätt att vara värd för webbplatsen, men med framtida tillväxt i åtanke.](#smallbusiness)
* [Jag är en webbplats eller en grafisk designer och du vill skapa webbplatser för mina kunder.](#designer)
* [Jag migrera Mina flernivåapp med en webbklientdel till molnet.](#multitier)
* [Min programmet är beroende av anpassade Windows eller Linux-miljöer och jag vill flytta till molnet.](#custom)
* [Min plats använder programvara med öppen källkod och jag vill ha i Azure.](#oss)
* [Jag har ett line-of-business-program som ska anslutas till företagets nätverk.](#lob)
* [Jag vill vara värd för en REST API eller webbtjänst för mobila klienter.](#mobile)

### <a id="onprem"></a>Jag behöver en frontwebb med bakgrund bearbetning och databasen serverdelen för att köra företagsprogram som är integrerad med lokala tillgångar.
Azure Apptjänst är en bra lösning för komplexa program. Gör det möjligt att utveckla appar som skala automatiskt på en belastningen belastningsutjämnade plattform, skyddas med Active Directory och ansluta till lokala resurser. Den gör Hantera apparna enkelt via en världsklass portal och API: er och gör att du kan få insyn i hur kunder använder dem med app insight verktyg. Den [Webjobs] [ Webjobs] funktionen kan du köra bakgrundsprocesser och aktiviteter som en del av din webbnivå medan hybridanslutning och funktioner för virtuella nätverk gör det enkelt att ansluta tillbaka till lokala resurser. Azure App Service tillhandahåller tre 9 SLA för webbprogram och kan du:

* Kör dina program på ett tillförlitligt sätt på en molnplattform självläkande, automatisk uppdatering.
* Skala automatiskt över ett globalt nätverk av datacenter.
* Säkerhetskopiera och återställa för katastrofåterställning.
* Vara ISO, SOC2 och PCI.
* Integrering med Active Directory

### <a id="corp"></a>Jag behöver ett säkert sätt att vara värd för min företagets webbplats som kan skalas väl och erbjudanden globala nå.
Azure Apptjänst är en bra lösning för att hantera företagets webbplatser. Den gör webbappar till skala snabbt och enkelt sätt att uppfylla begäran över ett globalt nätverk av datacenter. Den erbjuder lokala reach feltolerans och intelligent trafikhantering. Så att du kan få insyn i hälsa för platsen och trafiken snabbt och enkelt på en plattform som tillhandahåller världsklass hanteringsverktyg. Azure App Service tillhandahåller tre 9 SLA för webbprogram och kan du:

* Vill du köra dina webbplatser på ett tillförlitligt sätt på en molnplattform självläkande, automatisk uppdatering.
* Skala automatiskt över ett globalt nätverk av datacenter.
* Säkerhetskopiera och återställa för katastrofåterställning.
* Hantera loggar och trafik med integrerade verktyg.
* Vara ISO, SOC2 och PCI.
* Integrering med Active Directory

### <a id="iis6"></a>Jag har en IIS6-program som körs på Windows Server 2003.
Azure App Service gör det enkelt att undvika infrastrukturkostnader med migrera äldre IIS6 program. Microsoft har utvecklat [lättanvända Migreringsverktyg och detaljerade riktlinjer](https://www.migratetoazure.net/) som gör att du kan kontrollera kompatibiliteten och identifiera eventuella ändringar som behöver göras. Integrering med Visual Studio, TFS och gemensamma CMS-verktyg som gör det enkelt att distribuera IIS6 program direkt till molnet. När har distribuerats, tillhandahåller Azure Portal stabil hanteringsverktyg som gör att du kan skala för att hantera kostnader och upp till uppfyller efterfrågan vid behov. Med Migreringsverktyget kan du:

* Snabbt och enkelt migrera äldre Windows Server 2003 webbprogrammet till molnet.
* Välja för att lämna bifogade SQL-databas lokalt för att skapa en hybridprogram.
* Flytta SQL-databasen tillsammans med äldre programmet automatiskt.

### <a id="smallbusiness"></a>Jag är ett litet företagsägare och jag behöver ett billigt sätt att vara värd för webbplatsen, men med framtida tillväxt i åtanke.
Azure Apptjänst är en bra lösning för det här scenariot eftersom du kan börja använda det kostnadsfritt och sedan lägga till flera funktioner när du behöver dem. Varje ledigt webbprogram som levereras med en domän som tillhandahålls av Azure (*your_company*. azurewebsites.net), och plattformen som innehåller integrerade distribution och hantering av verktyg, samt en programgalleriet som gör det enkelt att komma igång. Det finns många andra tjänster och skalningsalternativ som att platsen tillåts utvecklas med ökad användarens behov. Med Azure App Service kan du:

* Börja med den kostnadsfria nivån och skala upp efter behov.
* Använda Application Gallery för att snabbt kunna konfigurera populära webbprogram, till exempel WordPress.
* Lägga till ytterligare Azure-tjänster och funktioner i ditt program efter behov.
* Skydda ditt webbprogram med HTTPS.

### <a id="designer"></a>Jag är en webbplats eller en grafisk designer och vill skapa webbplatser för Mina kunders
För webbutvecklare och designers Azure App Service kan enkelt integreras med en mängd olika verktyg och ramverk, inkluderar stöd för distribution för Git och FTP och integreras med verktyg och tjänster som Visual Studio och SQL-databas. Med App Service kan du:

* Använd kommandoradsverktygen för [automatiserade aktiviteter][scripting].
* Arbeta med populära språk som [.Net][dotnet], [PHP][PHP], [Node.js] [ nodejs], och [Python][Python].
* Välj tre skalning nivåer för att skala till mycket hög kapacitet.
* Integrera med andra Azure-tjänster som [SQL-databas][sqldatabase], [Service Bus] [ servicebus] och [lagring] [ Storage], eller partnernätverk erbjudanden från den [Azure Store][azurestore], till exempel MySQL och MongoDB.
* Integrera med verktyg som Visual Studio, Git, WebMatrix, WebDeploy, TFS och FTP.

### <a id="multitier"></a>Jag migrera Mina flernivåapp med en webbklientdel till molnet
Om du kör en flernivåapp, till exempel en webbserver som ansluter till en databas är ett bra alternativ som integreras med Azure SQL Database i Azure App Service. Och du kan använda funktionen WebJobs för backend-processer som körs.

Välj Service Fabric för en eller flera av dina nivåerna om du behöver mer kontroll över servermiljö, till exempel möjligheten att fjärråtkomst till servern eller konfigurera servern startade uppgifter.

Välj virtuella datorer för en eller flera av dina nivåerna om du vill använda en egen datoravbildning eller kör programvara eller tjänster som du inte kan konfigurera på Service Fabric.

### <a id="custom"></a>Min programmet är beroende av anpassade Windows eller Linux-miljöer och jag vill flytta till molnet.
Om ditt program kräver komplexa installation eller konfiguration av programvara och operativsystem, Virtual Machines är antagligen den bästa lösningen. Med virtuella datorer kan du:

* Använda galleriet för virtuella datorer för att börja med ett operativsystem, till exempel Windows eller Linux och anpassa den efter dina krav för programmet.
* Skapa och ladda upp en anpassad avbildning av en befintlig lokal server körs på en virtuell dator i Azure.

### <a id="oss"></a>Min plats använder programvara med öppen källkod och jag vill ha i Azure
Om ditt öppen källkod framework stöds på Apptjänst, har språk och ramverk som behövs i programmet konfigurerats för du automatiskt. Apptjänst kan du:

* Använda många populära öppen källa språk, till exempel [.NET][dotnet], [PHP][PHP], [Node.js] [ nodejs], och [Python][Python].
* Konfigurera WordPress, Drupal, Umbraco, DNN och många andra tredjeparts-webbprogram.
* Migrera ett befintligt program eller skapa en ny från galleriet program.

Om ditt öppen källkod framework inte stöds på Apptjänst, kan du köra det på en av andra Azure webbhotell. Med virtuella datorer, installera och konfigurera programvaran på datoravbildningen, som kan vara Windows eller Linux-baserade.

### <a id="lob"></a>Jag har ett line-of-business-program som ska anslutas till företagets nätverk
Om du vill skapa ett line-of-business-program kan din webbplats kräver direkt åtkomst till tjänster eller data i företagsnätverket. Detta är möjligt på App Service, Service Fabric och virtuella datorer med den [Azure Virtual Network service](/azure/virtual-network/). På Apptjänst kan du använda den [VNET integrationsfunktionen](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), vilket gör att din Azure-program körs som om de befann sig i företagsnätverket.

### <a id="mobile"></a>Jag vill ha en REST API eller webbtjänst för mobila klienter
HTTP-baserade webbtjänster kan du stöder ett stort antal klienter, inklusive mobila klienter. Ramverk som ASP.NET Web API integrera med Visual Studio för att göra det enklare att skapa och använda REST-tjänster.  Tjänsterna är tillgängliga från en webbserver-slutpunkt, så att det är möjligt att använda en webbplats som värd för teknik på Azure som stöd för det här scenariot. Men är Apptjänst ett bra alternativ för värd för REST API: er. Med App Service kan du:

* Snabbt skapa en [mobilappen](../app-service-mobile/app-service-mobile-value-prop.md) eller API-app som värd för HTTP-webbtjänsten i något av Azures globalt distribueras datacenter.
* Migrera befintliga tjänster eller skapa nya.
* Uppnå SLA för tillgänglighet med en enda instans, eller skala ut till flera dedikerade datorer.
* Använd den publicerade platsen för att tillhandahålla REST API: er till alla HTTP-klienter, inklusive mobila klienter.

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett konto kan du gå till <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, där kan du direkt skapa en tillfällig startapp i Azure App Service kostnadsfritt. Inget kreditkort behövs, inga åtaganden.
> 
> 

## <a id="nextsteps"></a>Nästa steg
Mer information om de tre web värd alternativ, se [introduktion till Azure](../fundamentals-introduction-to-azure.md).

Om du vill komma igång med valda alternativ för ditt program, finns i följande resurser:

* [Azure App Service](/azure/app-service/)
* [Azure Cloud Services](/azure/cloud-services/)
* [Virtuella Azure-datorer](/azure/virtual-machines/)
* [Service Fabric](/azure/service-fabric/)

<!-- URL List -->

[Azure App Service]: /azure/app-service/
[Cloud Services]: /azure/cloud-services/
[Virtual Machines]: /azure/virtual-machines/
[Service Fabric]: /azure/service-fabric/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: app-service-web-tutorial-custom-ssl.md
[azurestore]: https://azuremarketplace.microsoft.com/en-us/marketplace/apps
[scripting]: https://azure.microsoft.com/documentation/scripts/?services=web-sites
[dotnet]: https://azure.microsoft.com/develop/net/
[nodejs]: https://azure.microsoft.com/develop/nodejs/
[PHP]: https://azure.microsoft.com/develop/php/
[Python]: https://azure.microsoft.com/develop/python/
[servicebus]: /azure/service-bus/
[sqldatabase]: /azure/sql-database/
[Storage]: /azure/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
