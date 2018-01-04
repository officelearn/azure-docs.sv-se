---
title: "Jämförelse av Azure App Service, Virtual Machines, Service Fabric och Cloud Services | Microsoft Docs"
description: "Lär dig hur du väljer mellan Azure App Service, Virtual Machines, Service Fabric och Cloud Services som värd för webbappar."
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
ms.openlocfilehash: 0dba36e5490af56debd3b64b20d39809cd5d5f81
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Jämförelse mellan Azure App Service, Virtual Machines, Service Fabric och Cloud Services
## <a name="overview"></a>Översikt

Azure erbjuder flera värdalternativ för webbplatser: [Azure App Service][Azure App Service], [Virtual Machines][Virtual Machines], [Service Fabric][Service Fabric] och [Cloud Services][Cloud Services]. Den här artikeln hjälper dig att förstå alternativen och fatta rätt beslut för din webbapp.

Azure App Service är det bästa alternativet för de flesta webbapparna. Distribution och hantering finns integrerat i plattformen, platser kan skalas snabbt för att hantera hög trafikbelastning och inbyggd belastningsutjämning och Traffic Manager ger hög tillgänglighet. Du kan enkelt flytta befintliga webbplatser till Azure App Service med ett [onlinemigreringsverktyg](https://www.migratetoazure.net/), använda en app baserad på öppen källkod från webbappgalleriet eller skapa en ny webbplats med hjälp av ramverket och verktyg som du väljer själv. Med funktionen [WebJobs][WebJobs] kan du enkelt lägga till bearbetning av bakgrundsjobb i din App Service-webbapp.

Service Fabric är ett bra alternativ om du skapar en ny app eller skriver om en befintlig app för att använda en mikrotjänstarkitektur. Om du kör appar i en delad pool med datorer kan du börja i liten skala och utöka till hundratals eller tusentals datorer efter behov. Tillståndskänsliga tjänster gör det enkelt att lagra programtillstånd på ett konsekvent och tillförlitligt sätt och Service Fabric hanterar automatiskt tjänstens partitionering, skalning och tillgänglighet åt dig.  Service Fabric stöder också WebAPI med Open Web Interface för .NET (OWIN) och ASP.NET Core.  Jämfört med App Service ger Service Fabric också mer kontroll över, eller direkt åtkomst till, den underliggande infrastrukturen. Du kan fjärransluta till dina servrar eller konfigurera serverstartaktiviteter. Cloud Services liknar Service Fabric när det gäller kontroll jämfört med användarvänlighet, men det är numera en föråldrad tjänst och Service Fabric rekommenderas för ny utveckling.

Om du har ett befintligt program som kräver stora ändringar för att kunna köras i App Service eller Service Fabric kan du välja Virtual Machines för att underlätta migreringen till molnet. Men för korrekt konfigurering, skydd och underhåll av virtuella datorer krävs dock mer tid och IT-kunskaper jämfört med Azure App Service och Service Fabric. Om du överväger att använda Azure Virtual Machines bör du tänka på att det kräver löpande underhåll för korrigering, uppdatering och hantering av VM-miljön. Azure Virtual Machines är infrastruktur som tjänst (IaaS) medan App Service och Service Fabric är plattform som en tjänst (Paas). 

## <a name="features"></a>Jämförelse av funktioner
I följande tabell jämförs funktionerna i App Service, Cloud Services, Virtual Machines och Service Fabric för att hjälpa dig att välja rätt. Aktuell information om SLA för varje alternativ finns i [Azure Serviceavtal](https://azure.microsoft.com/support/legal/sla/).

| Funktion | App Service (webbappar) | Cloud Services (webbroller) | Virtuella datorer | Service Fabric | Anteckningar |
| --- | --- | --- | --- | --- | --- |
| Nästan omedelbar distribution |X | | |X |Det tar flera minuter att distribuera ett program eller en programuppdatering till en molntjänst. Att distribuera ett program till en webbapp tar bara några sekunder. |
| Skala upp till större datorer utan att distribuera om |X | | |X | |
| Webbserverinstanser delar innehåll och konfiguration, vilket innebär att du inte behöver distribuera om eller konfigurera om när du skalar. |X | | |X | |
| Flera distributionsmiljöer (produktion och mellanlagring) |X |X | |X |Med Service Fabric kan du ha flera miljöer för dina appar eller distribuera olika versioner av appar sida vid sida. |
| Automatisk hantering av OS-uppdateringar |X |X | | |Delvis via POA (Patch Orchestration Application) och fullständigt i framtiden. |
| Sömlös växling mellan plattformar (flytta enkelt mellan 32 bitar och 64 bitar) |X |X | | | |
| Distribuera kod med GIT, FTP |X | |X | | |
| Distribuera kod med webbdistribution |X | |X | |Cloud Services stöder användning av webbdistribution för att distribuera uppdateringar till enskilda rollinstanser. Men du kan inte använda webbdistribution för första distributionen för en roll, och om du använder webbdistribution för en uppdatering måste du distribuera varje instans av en roll separat. Flera instanser krävs för kvalificering för Cloud Service-serviceavtalet för produktionsmiljöer. |
| WebMatrix-stöd |X | |X | | |
| Åtkomst till tjänster som Service Bus, Storage och SQL Database |X |X |X |X | |
| Värd för webb- eller webbtjänstnivå i en arkitektur med flera nivåer |X |X |X |X | |
| Värd för mellannivå i en arkitektur med flera nivåer |X |X |X |X |En App Service-webbapp kan enkelt vara värd för en REST API-mellannivå och [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226)-funktionen kan vara värd för jobb som bearbetas i bakgrunden. Du kan köra WebJobs på en dedikerad webbplats för att få oberoende skalbarhet för nivån. |
| Integrerat stöd för MySQL som en tjänst |X |X | | | |
| Stöd för ASP.NET, klassisk ASP, Node.js, PHP, Python |X |X |X |X |Service Fabric stöder skapande av frontwebb med [ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) eller så kan du distribuera valfri programtyp (Node.js, Java m.m.) som en [körbar gästfil](../service-fabric/service-fabric-deploy-existing-app.md). |
| Skala ut till flera instanser utan omdistribution |X |X |X |X |Virtual Machines kan skala ut till flera instanser, men tjänsterna som körs på dem måste skrivas om för att hantera utskalningen. Du måste konfigurera en belastningsutjämnare för att dirigera begäranden mellan datorer och skapa en tillhörighetsgrupp för att förhindra samtidiga omstarter av alla instanser på grund av underhåll eller maskinvarufel. |
| Stöd för SSL |X |X |X |X |När det gäller App Service-webbappar stöds SSL för anpassade domännamn endast för lägena Basic och Standard. Information om hur du använder SSL med webbappar finns i [Configuring an SSL certificate for an Azure Website](app-service-web-tutorial-custom-ssl.md) (Konfigurera ett SSL-certifikat för en Azure-webbplats). |
| Integrering med Visual Studio |X |X |X |X | |
| Fjärrfelsökning |X |X |X | | |
| Distribuera kod med TFS |X |X |X |X | |
| Nätverksisolering med [Azure Virtual Network](/azure/virtual-network/) |X |X |X |X |Se även [Virtuell nätverksintegrering med Azure Websites](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| Stöd för [Azure Traffic Manager](/azure/traffic-manager/) |X |X |X |X | |
| Integrerad slutpunktsövervakning |X |X |X | | |
| Fjärrskrivbordsåtkomst till servrar | |X |X |X | |
| Installera eventuella anpassade MSI | |X |X |X |Med Service Fabric kan du vara värd för körbara filer som en [körbar gästfil](../service-fabric/service-fabric-deploy-existing-app.md) eller installera appar på de virtuella datorerna. |
| Möjlighet att definiera/köra startåtgärder | |X |X |X | |
| Kan lyssna på ETW-händelser | |X |X |X | |

## <a name="scenarios"></a>Scenarier och rekommendationer
Här följer några vanliga appscenarier med rekommendationer om vilket Azure-webbvärdsalternativ som passar bäst för respektive scenario.

* [Jag behöver en frontwebb med bakgrundsbearbetning och databas på serverdelen för att köra företagsprogram som är integrerade med lokala tillgångar.](#onprem)
* [Jag behöver ett tillförlitligt värdalternativ för min företagswebbplats med goda skalningsmöjligheter och global räckvidd.](#corp)
* [Jag har ett IIS6-program som körs på Windows Server 2003.](#iis6)
* [Jag har ett litet företag och behöver ett billigt värdalternativ för min webbplats, men med möjlighet att växa i framtiden.](#smallbusiness)
* [Jag är en webb- eller grafikdesigner som designar och bygger webbplatser åt mina kunder.](#designer)
* [Jag migrerar ett flernivåprogram med en frontwebb till molnet.](#multitier)
* [Mitt program är beroende av Windows- eller Linux-miljöer som kräver stora anpassningar och jag vill flytta det till molnet.](#custom)
* [Min webbplats använder programvara med öppen källkod och jag vill ha den i Azure.](#oss)
* [Jag har en verksamhetsspecifik app som behöver ansluta till företagets nätverk.](#lob)
* [Jag vill vara värd för ett REST-API eller en webbtjänst för mobila klienter.](#mobile)

### <a id="onprem"></a> Jag behöver en frontwebb med bakgrundsbearbetning och databas på serverdelen för att köra företagsprogram som är integrerade med lokala tillgångar.
Azure App Service är en bra lösning för komplexa företagsprogram. Med den kan du utveckla appar som skalas automatiskt på en belastningsutjämnad plattform, skyddas med Active Directory och ansluter till dina lokala resurser. Du kan enkelt hantera apparna via en portal och API:er i världsklass, och du kan få insikt i hur kunderna använder dem med App Insights-verktyg. Med funktionen [Webjobs][Webjobs] kan du köra bakgrundsprocesser och åtgärder som en del av din webbnivå, och med funktioner för hybridanslutning och VNET kan du enkelt ansluta tillbaka till lokala resurser. Azure App Service tillhandahåller 99,9 % SLA för webbprogram och kan du:

* Köra program tillförlitligt på en självåterställande molnplattform med automatiska korrigeringar.
* Skala automatiskt över ett globalt nätverk av datacenter.
* Säkerhetskopiera och återställa för haveriberedskap.
* Vara ISO-, SOC2- och PCI-kompatibel.
* Integrering med Active Directory

### <a id="corp"></a> Jag behöver ett tillförlitligt sätt att ha min företagswebbplats med goda skalningsmöjligheter och global räckvidd.
Azure App Service är en bra värdlösning för företagswebbplatser. Den gör att webbappar kan skalas snabbt och enkelt för att uppfylla behoven med ett globalt nätverk av datacenter. Den erbjuder lokal räckvidd, feltolerans och smart trafikhantering. Med allt på en plattform och hanteringsverktyg i världsklass kan du snabbt och enkelt få insikt i webbplatsens hälsa och trafik. Azure App Service tillhandahåller 99,9 % SLA för webbprogram och du kan:

* köra dina webbplatser på ett tillförlitligt sätt med en självåterställande molnplattform med automatiska korrigeringar.
* skala automatiskt över ett globalt nätverk av datacenter.
* säkerhetskopiera och återställa för haveriberedskap.
* hantera loggar och trafik med integrerade verktyg.
* vara ISO-, SOC2- och PCI-kompatibel.
* integrera med Active Directory

### <a id="iis6"></a>Jag har ett IIS6-program som körs på Windows Server 2003.
Azure App Service gör det enkelt att undvika infrastrukturkostnader vid migrering av äldre IIS6-program. Microsoft har skapat [lättanvända migreringsverktyg och detaljerade riktlinjer](https://www.migratetoazure.net/) som gör att du kan kontrollera kompatibiliteten och identifiera eventuella ändringar som behöver göras. Integrering med Visual Studio, TFS och gemensamma CMS-verktyg gör det enkelt att distribuera IIS6-program direkt till molnet. När de har distribuerats finns gedigna hanteringsverktyg i Azure Portal så att du kan skala ned för att minska kostnaderna och skala upp när det behövs. Med migreringsverktyget kan du:

* snabbt och enkelt migrera äldre Windows Server 2003-webbappar till molnet.
* välja att lämna din bifogade SQL-databas lokalt för att skapa en hybridapp.
* flytta SQL-databasen automatiskt tillsammans med ditt äldre program.

### <a id="smallbusiness"></a>Jag har ett litet företag och behöver ett billigt värdalternativ för min webbplats, men med möjlighet att växa i framtiden.
Azure App Service är en bra lösning för det här scenariot eftersom du kan börja använda det gratis och sedan lägga till fler funktioner när du behöver dem. Varje kostnadsfri webbapp levereras med en domän från Azure (*ditt_företag*.azurewebsites.net) och plattformen har integrerade verktyg för distribution och hantering och ett appgalleri som gör det enkelt att komma igång. Det finns många andra tjänster och skalningsalternativ som gör att webbplatsen kan växa vid ökade behov. Med Azure App Service kan du:

* börja med den kostnadsfria nivån och skala upp efter behov.
* använda appgalleriet för att snabbt komma igång med populära webbappar, till exempel WordPress.
* lägga till ytterligare Azure-tjänster och funktioner i din app efter behov.
* skydda din webbapp med HTTPS.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a id="designer"></a>Jag är en webb- eller grafikdesigner som designar och bygger webbplatser åt mina kunder
Webbutvecklare och designers kan enkelt integrera Azure App Service med flera olika ramverk och verktyg. Azure App Service har distributionsstöd för Git och FTP och nära integrering med verktyg och tjänster som Visual Studio och SQL Database. Med App Service kan du:

* använda kommandoradsverktyg för [automatiserade uppgifter][scripting].
* arbeta med populära språk som [.Net][dotnet], [PHP][PHP], [Node.js][nodejs] och [Python][Python].
* välja tre skalningsnivåer för att skala upp till mycket hög kapacitet.
* integrera med andra Azure-tjänster, till exempel [SQL Database][sqldatabase], [Service Bus][servicebus] och [Storage][Storage], eller partnererbjudanden från [Azure Store][azurestore] som MySQL och MongoDB.
* integrera med verktyg som Visual Studio, Git, WebMatrix, WebDeploy, TFS och FTP.

### <a id="multitier"></a>Jag migrerar ett flernivåprogram med en frontwebb till molnet
Om du kör ett flernivåprogram, till exempel en webbserver som ansluter till en databas, är Azure App Service ett bra alternativ med nära integrering med Azure SQL Database. Och du kan använda funktionen WebJobs för att köra processer på serverdelen.

Välj Service Fabric för en eller flera av dina nivåer om du behöver mer kontroll över servermiljön, till exempel möjlighet att fjärransluta till servern eller konfigurera serverstartåtgärder.

Välj Virtual Machines för en eller flera av dina nivåerna om du vill använda en egen datoravbildning eller köra serverprogramvara eller tjänster som du inte kan konfigurera i Service Fabric.

### <a id="custom"></a>Mitt program är beroende av Windows- eller Linux-miljöer som kräver stora anpassningar och jag vill flytta det till molnet.
Om ditt program kräver en komplex installation eller konfiguration av programvara och operativsystemet är Virtual Machines antagligen den bästa lösningen. Med Virtual Machines kan du:

* använda galleriet för virtuella datorer för att börja med ett operativsystem, till exempel Windows eller Linux, och sedan anpassa det efter dina programkrav.
* skapa och ladda upp en anpassad avbildning av en befintlig lokal server som ska köras på en virtuell dator i Azure.

### <a id="oss"></a>Min webbplats använder programvara med öppen källkod och jag vill ha den i Azure
Om ditt ramverk med öppen källkod stöds i App Service konfigureras de språk och ramverk du behöver för ditt program automatiskt åt dig. Med App Service kan du:

* använda populära språk som [.NET][dotnet], [PHP][PHP], [Node.js][nodejs] och [Python][Python].
* konfigurera WordPress, Drupal, Umbraco, DNN och många andra webbappar från tredje part.
* migrera en befintlig app eller skapa en ny från appgalleriet.

Om App Service inte stöder ditt ramverk med öppen källkod kan du köra det på något av de andra Azure-webbvärdalternativen. Med Virtual Machines installerar och konfigurerar du programvara på datoravbildningen, som kan vara baserad på Windows eller Linux.

### <a id="lob"></a>Jag har en verksamhetsspecifik app som behöver ansluta till företagets nätverk
Om du vill skapa en verksamhetsspecifik app kanske din webbplats behöver direkt åtkomst till tjänster eller data i företagsnätverket. Detta är möjligt i App Service, Service Fabric och Virtual Machines med hjälp av [Azure Virtual Network-tjänsten](/azure/virtual-network/). På App Service kan du använda [VNET-integrationsfunktionen](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) som gör att dina Azure-program körs som om de befann sig i företagsnätverket.

### <a id="mobile"></a>Jag vill vara värd för ett REST-API eller en webbtjänst för mobila klienter
Med HTTP-baserade webbtjänster kan du stödja flera olika klienter, inklusive mobila klienter. Ramverk som ASP.NET Web API kan integreras med Visual Studio för att göra det enklare att skapa och använda REST-tjänster.  Dessa tjänster exponeras från en webbslutpunkt så att du kan använda valfri webbvärdteknik i Azure för det här scenariot. App Service ett bra alternativ för att vara värd för REST-API:er. Med App Service kan du:

* snabbt skapa en [mobilapp](../app-service-mobile/app-service-mobile-value-prop.md) eller API-app som värd för HTTP-webbtjänsten i något av Azures globalt distribuerade datacenter.
* migrera befintliga tjänster eller skapa nya.
* uppnå SLA för tillgänglighet med en enda instans, eller skala ut till flera dedikerade datorer.
* använda den publicerade webbplatsen för att tillhandahålla REST-API: er till alla HTTP-klienter, inklusive mobila klienter.

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett konto går du till <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>. Där kan du direkt skapa en tillfällig startapp i Azure App Service utan kostnad. Inget kreditkort krävs, och du gör inga åtaganden.
> 
> 

## <a id="nextsteps"></a> Nästa steg
Mer information om de tre webbvärdalternativen finns i [Introduktion till Azure](../fundamentals-introduction-to-azure.md).

Om du vill komma igång med de alternativ som du valt för ditt program kan du se följande resurser:

* [Azure App Service](/azure/app-service/)
* [Azure Cloud Services](/azure/cloud-services/)
* [Azure Virtual Machines](/azure/virtual-machines/)
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
