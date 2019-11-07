---
title: Vanliga frågor och svar om Azure våren Cloud | Microsoft Docs
description: Läs vanliga frågor och svar om Azure våren Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 1fb241ab1335e6b9c9be55acbbe2e21378831b69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607229"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Den här artikeln behandlar vanliga frågor om Azure våren Cloud. 

## <a name="general"></a>Allmänt

### <a name="why-azure-spring-cloud"></a>Varför Azure våren Cloud?

Azure våren Cloud tillhandahåller en PaaS (Platform as a Service) för våren-utvecklare. Azure våren Cloud hanterar din program infrastruktur så att du kan fokusera på program kod och affärs logik. Kärn funktioner som är inbyggda i Azure våren Cloud, inkluderar Eureka, konfigurations Server, tjänst register Server, pivotal build-tjänst, blå-grön distributioner med mera. Den här tjänsten gör det också möjligt för utvecklare att binda sina program med Azure-tjänster som CosmosDB, MySQL och Azure cache för Redis.

Azure våren Cloud förbättrar programdiagnostikens upplevelse för utvecklare och operatörer genom att integrera Azure Monitor, Application Insights och Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Vilka tjänst planer erbjuder Azure våren Cloud?

Azure våren Cloud erbjuder en service plan under för hands perioden.  En våren Cloud-distribution innehåller 16 vCPU kärnor och 32 GB minne.  Den övre kanten för varje mikrotjänst instans i en distribution är 4 vCPU kärnor med 8 GB minne.

Resurs | Belopp
------- | -------
App-instanser per fjäder program | 20
Totalt antal App-instanser per Azure våren Cloud Service-instans | 50 *
Azure våren Cloud Service-instanser per region per prenumeration | 11.2
Beständiga volymer | 10 x 50 GBytes

*_öppna ett [support ärende](https://azure.microsoft.com/support/faq/) för att höja gränsen._

Mer information finns i [vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Hur säkert är Azure våren Cloud?

Säkerhet och sekretess är en av de viktigaste prioriteringarna för Azure-och Azure våren Cloud-kunder. Azure säkerställer att endast kunden har åtkomst till program data, loggar eller konfigurationer genom att kryptera alla dessa data på ett säkert sätt. Alla tjänst instanser i Azure våren Cloud är isolerade från varandra.

Azure våren Cloud tillhandahåller fullständig SSL och certifikat hantering.

Viktiga säkerhets uppdateringar för OpenJDK och våren Cloud runtime används i Azure våren Cloud så snart som möjligt.

### <a name="which-regions-azure-spring-cloud-are-available"></a>Vilka regioner kan Azure våren Cloud vara tillgängliga?

Östra USA, västra USA 2, Västeuropa och Sydostasien.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Vilka är kända begränsningar i Azure våren Cloud

Här följer kända begränsningar i Azure våren-molnet medan tjänsten är i för hands version.

* `spring.application.name` åsidosätts av det program namn som används för att skapa varje program.
* `server.port` tillåts inte i konfigurations filen från git-lagrings platsen. Att lägga till den i konfigurations filen kommer förmodligen att orsaka att programmet inte kan kommas åt från andra program eller Internet.
* Azure Portal-och Resource Manager-mallarna stöder inte överföring av programpaket. Detta kan bara göras via program distribution av Azure CLI.
* För kvot begränsningar, se [vilka Service planer som erbjuder Azure våren Cloud-erbjudandet](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Hur kan jag ge feedback och rapportera problem?

Om du stöter på problem med Azure våren Cloud kan du skapa en [support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Om du vill ha funktions förfrågningar kan du gå till [Azure feedback](https://feedback.azure.com/forums/34192--general-feedback) för att begära funktioner eller ge feedback.

## <a name="development"></a>Utveckling

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Jag är en våren-utvecklare men är nybörjare på Azure, vilket är det snabbaste sättet för mig att lära sig hur man utvecklar reserverade fjäder moln program?

Det snabbaste sättet att komma igång med Azure våren Cloud är att följa [den här snabb](spring-cloud-quickstart-launch-app-portal.md)starten.

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Vilken Java-körning stöder Azure våren Cloud?

Azure våren Cloud har stöd för Java 8 och 11.

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>Var kan jag se mina vårens program loggar och mått?

Hitta mått på fliken Översikt över appen och fliken [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) .

Azure våren Cloud stöder export av dina våren-programloggar och-mått till Azure Storage, EventHub och [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Tabell namnet i Log Analytics är `AppPlatformLogsforSpring`. Läs den här artikeln om våra [diagnostiska tjänster](diagnostic-services.md)för att aktivera det.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Stöder Azure våren Cloud Distributed tracing?

Ja, besök [Distributed tracing](spring-cloud-tutorial-distributed-tracing.md) för mer information.

### <a name="what-resource-types-does-service-binding-support"></a>Vilka resurs typer stöder tjänst bindning?

Tre tjänster stöds för närvarande: Azure Cosmos DB, Azure Database for MySQL och Azure cache för Redis.

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>Kan jag Visa/lägga till/flytta beständiga volymer från i mina program?
Ja.

## <a name="deployment"></a>Distribution

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Stöder Azure våren Cloud blå-grön distribution?
Ja, besök [guiden för mellanlagrings miljö](spring-cloud-howto-staging-environment.md) för mer information.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Kan jag få åtkomst till Kubernetes för att ändra mina program behållare?

Nej.  Azure våren Cloud är en sammanfattning av utvecklaren från den underliggande arkitekturen, så att du kan koncentrera dig på program kod och affärs logik.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Har Azure våren Cloud stöd för att skapa behållare från källa?

Ja, besök [distribuera från källan](spring-cloud-launch-from-source.md) för mer information.

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Stöder Azure våren Cloud automatisk skalning i App-instanser?

Nej.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>Vilka är bästa praxis för att migrera befintliga våren-mikrotjänster till Azure våren Cloud?

Innan du migrerar befintliga våren-mikrotjänster till Azure våren Cloud
* Alla program beroenden måste lösas.
* Förbered dina konfigurations poster, miljövariabler och JVM-parametrar så att du kan jämföra dem med distributionen i Azure våren Cloud.
* Om du vill använda tjänst bindningen går du igenom dina Azure-tjänster och ser till att du har rätt åtkomst behörighet.
* Vi rekommenderar att du tar bort eller inaktiverar inbäddade tjänster som kan vara i konflikt med tjänster som hanteras av Azure våren Cloud, till exempel tjänsten tjänst identifiering, konfigurations Server osv.
*-* Vi rekommenderar att du använder officiella och stabila pivoterande våren-bibliotek. Icke-officiella, beta-eller förgrenings versioner av pivotal våren-bibliotek har inget SLA-stöd.

Efter migreringen kan du övervaka CPU/RAM-mått och nätverks trafik för att se till att program instanserna skalas på rätt sätt.

## <a name="next-steps"></a>Nästa steg

[Kolla in fel söknings guiden om du har fler frågor](spring-cloud-troubleshoot.md).