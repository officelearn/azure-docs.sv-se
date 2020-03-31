---
title: Introduktion till Azure Spring Cloud
description: Lär dig funktionerna och fördelarna med Azure Spring Cloud för att distribuera och hantera Java Spring-program i Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: 4426044b3608be0ded378f4f56cbec6bc1948d75
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78273263"
---
# <a name="what-is-azure-spring-cloud"></a>Vad är Azure Spring Cloud?

Azure Spring Cloud gör det enkelt att distribuera Spring Boot-baserade mikrotjänstprogram till Azure med noll kodändringar.  Azure Spring Cloud hanterar livscykeln för Spring Cloud-program, så att utvecklare kan fokusera på sin kod.  Spring Cloud tillhandahåller livscykelhantering med omfattande övervakning och diagnostik, konfigurationshantering, tjänstidentifiering, CI/CD-integrering, blågröna distributioner med mera.

Som en del av Azure-ekosystemet tillåter Azure Spring Cloud enkel bindning till andra Azure-tjänster, inklusive lagring, databaser, övervakning med mera.

Azure Spring Cloud erbjuds för närvarande som en offentlig förhandsversion. Offentliga förhandsversioner gör det möjligt för kunder att experimentera med nya funktioner innan de släpps officiellt.  Offentliga förhandsversionsfunktioner och tjänster är inte avsedda för produktionsanvändning.  Mer information om support under förhandsversioner finns i våra [vanliga frågor](https://azure.microsoft.com/support/faq/) och svar eller en [supportförfrågan för](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) att få veta mer.

För att komma igång, slutföra Spring Cloud-snabbstarten med [Azure CLI](spring-cloud-quickstart-launch-app-cli.md), [Azure-portalen](spring-cloud-quickstart-launch-app-portal.md)eller [Maven](spring-cloud-quickstart-launch-app-maven.md).

Fler exempel finns på GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>Programkonfiguration

### <a name="spring-cloud-config-server"></a>Konfigurationsserver för fjädermoln

Azure Spring Cloud Config Server tillhandahåller externaliserad konfiguration i ett distribuerat system med stöd för både server och klientsidan.  Config Server är en central plats för att hantera programegenskaper i alla miljöer.  Mer information finns i [Spring Cloud Config Server-referensen](https://spring.io/projects/spring-cloud-config.md) och slutför självstudien.

### <a name="enable-bluegreen-deployments"></a>Aktivera blå/grön distribution

Azure Spring Cloud stöder blå/gröna distributioner för att släppa och uppdatera kod till produktionsmiljöer.  Genom att utnyttja det här ändringshanteringsmönstret kan utvecklare implementera funktioner och kodändringar med säkerheten för en omedelbar återgång när det behövs.  Azure gör det möjligt för utvecklare att koncentrera sig på att skriva kod genom att hantera flera produktionsmiljöer och göra det enkelt att uppdatera eller återställa kodändringar utan att avbryta programmet.  Mer information om mellanlagringsmiljöer och blå/gröna distributioner finns i den här [instruktioner](spring-cloud-howto-staging-environment.md).

### <a name="automate-cicd-pipelines"></a>Automatisera CI/CD-pipelines

Azure Spring Cloud tillhandahåller integrering med Azure DevOps med Hjälp av Azure CLI.  Med Azure DevOps kan du automatisera kodintegrering och distribution till ditt Spring-program.  Mer information finns i den här [artikeln](spring-cloud-howto-cicd.md).

### <a name="scale-your-application"></a>Skala ditt program

Med Azure Spring Cloud kan du enkelt skala mikrotjänsterna på instrumentpanelen i Azure Spring Cloud.  Både antalet virtuella processorer och mängden minne som är tillgängligt för dina mikrotjänster kan skalas upp eller ned för att passa dina behov.  Skalningen börjar gälla på några sekunder och kräver inga kodändringar eller omfördelning.  Om du vill veta mer, fyll i den här [självstudien](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Programövervakning

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Övervaka ditt program med distribuerad spårning och Azure App Insights

Spring Cloud distribuerade spårningsverktyg gör det möjligt för utvecklare att felsöka och övervaka de komplexa sammankopplingarna mellan mikrotjänster i ett program.  Genom att integrera [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) med Azures [Application Insights](../azure-monitor/insights/insights-overview.md)tillhandahåller Azure kraftfulla distribuerade spårningsfunktioner direkt från Azure-portalen.  Om du vill veta mer, fyll i den här [självstudien](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Nästa steg

- [Starta din Spring Cloud-app från CLI](spring-cloud-quickstart-launch-app-cli.md)
