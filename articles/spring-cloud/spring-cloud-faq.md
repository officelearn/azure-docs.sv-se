---
title: Vanliga frågor och svar om Azure Spring Cloud | Microsoft-dokument
description: Den här artikeln besvarar vanliga frågor om Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 62623bcadb35f21117ddc2601195e34598c2dff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298758"
---
# <a name="azure-spring-cloud-faq"></a>Vanliga frågor om Azure Spring Cloud

Den här artikeln besvarar vanliga frågor om Azure Spring Cloud. 

## <a name="general"></a>Allmänt

### <a name="why-azure-spring-cloud"></a>Varför Azure Spring Cloud?

Azure Spring Cloud tillhandahåller en plattform som en tjänst (PaaS) för Spring Cloud-utvecklare. Azure Spring Cloud hanterar din programinfrastruktur så att du kan fokusera på programkod och affärslogik. Grundläggande funktioner som är inbyggda i Azure Spring Cloud inkluderar Eureka, Config Server, Service Registry Server, Pivotal Build Service, Blå-gröna distributioner och mycket mer. Med den här tjänsten kan utvecklare också binda sina program med andra Azure-tjänster, till exempel Azure Cosmos DB, Azure Database for MySQL och Azure Cache for Redis.

Azure Spring Cloud förbättrar programdiagnostikupplevelsen för utvecklare och operatörer genom att integrera Azure Monitor, Application Insights och Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Vilka serviceplaner erbjuder Azure Spring Cloud?

Azure Spring Cloud erbjuder en serviceplan under förhandsgranskningsperioden.  En Spring Cloud-distribution innehåller 16 vCPU-kärnor och 32 GB minne.  Den övre gränsen för varje mikrotjänstinstans i en distribution är 4 vCPU-kärnor med 8 GB minne.

Resurs | Belopp
------- | -------
Appinstanser per fjäderprogram | 20
Totalt antal appinstanser per Azure Spring Cloud-tjänstinstans | 500
Azure Spring Cloud-tjänstinstanser per region per prenumeration | 10
Beständiga volymer | 10 x 50 GByte

\*_Öppna en [supportbiljett](https://azure.microsoft.com/support/faq/)_ om du vill höja gränsen.

Mer information finns i [Vanliga frågor och svar om Azure Support](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Hur säkert är Azure Spring Cloud?

Säkerhet och sekretess är bland de viktigaste prioriteringarna för Azure- och Azure Spring Cloud-kunder. Azure hjälper till att säkerställa att endast kunder har åtkomst till programdata, loggar eller konfigurationer genom att kryptera alla dessa data på ett säkert sätt. Alla tjänstinstanser i Azure Spring Cloud är isolerade från varandra.

Azure Spring Cloud tillhandahåller fullständig TLS/SSL och certifikathantering.

Kritiska säkerhetskorrigeringar för OpenJDK- och Spring Cloud-körningar tillämpas på Azure Spring Cloud så snart som möjligt.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>I vilka regioner är Azure Spring Cloud tillgängligt?

Östra USA, västra USA 2, Västeuropa och Sydostasien.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Vilka är de kända begränsningarna i Azure Spring Cloud?

Under förhandsversionen har Azure Spring Cloud följande kända begränsningar:

* `spring.application.name`åsidosätts av programnamnet som används för att skapa varje program.
* `server.port`är inte tillåtet i konfigurationsfilen från Git-repoen. Om du lägger till den i konfigurationsfilen kan programmet troligen inte nås från andra program eller internet.
* Azure-portalen och Azure Resource Manager-mallarna stöder inte överföring av programpaket. Du kan bara ladda upp programpaket genom att distribuera programmet via Azure CLI.
* Mer information om kvotbegränsningar finns i [Vilka serviceplaner erbjuder Azure Spring Cloud?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Hur kan jag ge feedback och rapportera problem?

Om du stöter på problem med Azure Spring Cloud skapar du en [Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Om du vill skicka en funktionsförfrågan eller ge feedback går du till [Azure Feedback](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Utveckling

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Jag är en Spring Cloud-utvecklare men ny i Azure. Vilket är det snabbaste sättet för mig att lära mig hur du utvecklar ett Azure Spring Cloud-program?

För det snabbaste sättet att komma igång med Azure Spring Cloud följer du instruktionerna i [Snabbstart: Starta ett Azure Spring Cloud-program med hjälp av Azure-portalen](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Vilken Java-körning stöder Azure Spring Cloud?

Azure Spring Cloud stöder Java 8 och 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Var kan jag visa mina Spring Cloud-programloggar och mått?

Hitta mått på fliken Appöversikt och fliken [Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics)

Azure Spring Cloud stöder export av Spring Cloud-programloggar och mått till Azure Storage, EventHub och [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Tabellnamnet i Log Analytics är *AppPlatformLogsforSpring*. Mer information om hur du aktiverar det finns i [Diagnostiktjänster](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Har Azure Spring Cloud stöd för distribuerad spårning?

Ja. Mer information finns i [Självstudiekurs: Använd distribuerad spårning med Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Vilka resurstyper stöder Service Binding?

Tre tjänster stöds för närvarande: Azure Cosmos DB, Azure Database for MySQL och Azure Cache for Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Kan jag visa, lägga till eller flytta beständiga volymer inifrån mina program?

Ja.

## <a name="deployment"></a>Distribution

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Stöder Azure Spring Cloud blågrön distribution?
Ja. Mer information finns i [Konfigurera en mellanlagringsmiljö](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Kan jag komma åt Kubernetes för att manipulera mina programbehållare?

Nej.  Azure Spring Cloud abstraherar utvecklaren från den underliggande arkitekturen, så att du kan koncentrera dig på programkod och affärslogik.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Stöder Azure Spring Cloud byggbehållare från källan?

Ja. Mer information finns i [Starta ditt Spring Cloud-program från källkoden](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Stöder Azure Spring Cloud automatisk skalning i appinstanser?

Nej.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Vilka är de bästa metoderna för att migrera befintliga Spring Cloud-mikrotjänster till Azure Spring Cloud?

När du migrerar befintliga Spring Cloud-mikrotjänster till Azure Spring Cloud är det en bra idé att följa följande metodtips:
* Alla programberoenden måste lösas.
* Förbered dina konfigurationsposter, miljövariabler och JVM-parametrar så att du kan jämföra dem med distributionen i Azure Spring Cloud.
* Om du vill använda Tjänstbindning går du igenom dina Azure-tjänster och kontrollerar att du har angett lämpliga åtkomstbehörigheter.
* Vi rekommenderar att du tar bort eller inaktiverar inbäddade tjänster som kan stå i konflikt med tjänster som hanteras av Azure Spring Cloud, till exempel vår tjänstidentifieringstjänst, Config Server och så vidare.
* Vi rekommenderar att du använder officiella, stabila Pivotal Spring-bibliotek. Inofficiella, beta- eller kluriga versioner av Pivotal Spring-bibliotek har inget servicenivåavtal (SLA) stöd.

Efter migreringen övervakar du processor-/RAM-måtten och nätverkstrafiken för att säkerställa att programinstanserna skalas på rätt sätt.

## <a name="next-steps"></a>Nästa steg

Om du har ytterligare frågor läser du [felsökningsguiden för Azure Spring Cloud](spring-cloud-troubleshoot.md).
