---
title: Introduktion till Azure våren Cloud
description: Lär dig om funktionerna och fördelarna med Azure våren Cloud för att distribuera och hantera Java våren-program i Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: a8b6535a6e1f6bf083b4a82b9f2ecacbd222cb27
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949770"
---
# <a name="what-is-azure-spring-cloud"></a>Vad är Azure Spring Cloud?

Azure våren Cloud gör det enkelt att distribuera våren Boot-baserade mikroservice-program till Azure utan kod ändringar.  Azure våren Cloud hanterar livs cykeln för våren Cloud-program, så att utvecklare kan fokusera på sin kod.  Våren Cloud tillhandahåller livs cykel hantering med omfattande övervakning och diagnostik, konfigurations hantering, tjänst identifiering, CI/CD-integrering, blå-grön distributioner med mera.

Som en del av Azures eko system gör Azure våren Cloud enkel bindning till andra Azure-tjänster, inklusive lagring, databaser, övervakning och mycket annat.

Azure våren Cloud erbjuds för närvarande som en offentlig för hands version. Med den offentliga för hands versionen kan kunder experimentera med nya funktioner före den officiella versionen.  Funktioner och tjänster för offentliga för hands versioner är inte avsedda för användning i produktion.  Om du vill ha mer information om support under för hands versionerna kan du läsa [vanliga frågor och svar](https://azure.microsoft.com/support/faq/) eller arkiv en [supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) .

I den här introduktionen beskrivs konfigurations servern för Azure våren Cloud, hur du aktiverar blå/gröna distributioner, skalar appar och hur du övervakar appens prestanda.

## <a name="spring-cloud-config-server"></a>Konfigurations Server för våren Cloud

Azure våren Cloud config server tillhandahåller en storskalig konfiguration i ett distribuerat system med stöd för både server-och klient sidan.  Azure våren Cloud config server är en central plats för att hantera program egenskaper i alla miljöer. Mer information finns i [referens för vår moln konfigurations Server](https://spring.io/projects/spring-cloud-config.md). 



## <a name="enable-bluegreen-deployments"></a>Aktivera blå/gröna distributioner

Azure våren Cloud stöder blå/gröna distributioner för att släppa och uppdatera kod i produktions miljöer.  Genom att använda det här mönstret för ändrings hantering kan utvecklare implementera funktioner och kod ändringar med säkerheten för omedelbar återgång vid behov.  Med Azure kan utvecklare koncentrera sig på att skriva kod genom att hantera flera produktions miljöer och göra det enkelt att uppdatera eller återställa kod ändringar utan att avbryta programmet.  Mer information om mellanlagrings miljöer och blå/gröna distributioner finns i den här [instruktions artikeln](spring-cloud-howto-staging-environment.md).

## <a name="automate-cicd-pipelines"></a>Automatisera CI/CD-pipeliner

Azure våren Cloud ger integrering med Azure DevOps med hjälp av Azure CLI.  Med Azure DevOps kan du automatisera kod integrering och distribution till ditt våren-program.  Mer information finns i den här [artikeln](spring-cloud-howto-cicd.md).

## <a name="scale-your-application"></a>Skala ditt program

Med Azure våren Cloud kan du enkelt skala Micro-tjänsterna i moln instrument panelen för Azure våren.  Både antalet virtuella processorer och mängden minne som är tillgängligt för mikrotjänster kan skalas upp eller ned för att passa dina behov.  Skalningen börjar gälla om några sekunder och kräver inte kod ändringar eller omdistribution.  Om du vill veta mer kan du slutföra den här [självstudien](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Programövervakning

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Övervaka ditt program med hjälp av distribuerad spårning och Azure App insikter

Med Microsofts verktyg för distribuerad spårning kan utvecklare felsöka och övervaka komplexa sammanlänkningar mellan mikrotjänster i ett program.  Genom att integrera [våren Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) med azures [Application Insights](../azure-monitor/insights/insights-overview.md)tillhandahåller Azure kraftfull, distribuerad spårnings funktion direkt från Azure Portal.  Om du vill veta mer kan du slutföra den här [självstudien](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Nästa steg
Kom igång genom att slutföra snabb starten för fjäder molnet med hjälp av [Azure CLI](spring-cloud-quickstart-launch-app-cli.md), [Azure Portal](spring-cloud-quickstart-launch-app-portal.md)eller [maven](spring-cloud-quickstart-launch-app-maven.md).

Fler exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

> [!div class="nextstepaction"]
> [Snabb start: Distribuera ditt första Azure våren Cloud-program](spring-cloud-quickstart.md)
