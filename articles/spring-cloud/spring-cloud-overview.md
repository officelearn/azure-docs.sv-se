---
title: Introduktion till Azure våren Cloud
description: Lär dig om funktionerna och fördelarna med Azure våren Cloud för att distribuera och hantera Java våren-program i Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 74ebdafb835aff75f282b9d6ac02d8ccf672a2be
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501098"
---
# <a name="what-is-azure-spring-cloud"></a>Vad är Azure Spring Cloud?

Azure våren Cloud gör det enkelt att distribuera våren Boot-baserade mikroservice-program till Azure utan kod ändringar.  Azure våren Cloud hanterar infrastrukturen i våren Cloud-program, så att utvecklare kan fokusera på sin kod.  Våren Cloud tillhandahåller livs cykel hantering med omfattande övervakning och diagnostik, konfigurations hantering, tjänst identifiering, CI/CD-integrering, blå-grön distributioner med mera.

Azure våren Cloud stöder både Java [våren start](https://spring.io/projects/spring-boot) och ASP.net Core [Steeltoe](https://steeltoe.io/) -appar. Steeltoe-stöd erbjuds för närvarande som en offentlig för hands version. Med den offentliga för hands versionen kan du experimentera med nya funktioner före den officiella versionen.  Funktioner och tjänster för offentliga för hands versioner är inte avsedda för användning i produktion.  Mer information finns i [vanliga frågor och svar](https://azure.microsoft.com/support/faq/) om fil [supportbegäran](../azure-portal/supportability/how-to-create-azure-support-request.md).

Som en del av Azures eko system gör Azure våren Cloud enkel bindning till andra Azure-tjänster, inklusive lagring, databaser, övervakning och mycket annat.

I den här introduktionen beskrivs följande funktioner i Azure våren-molnet:

* Konfigurationsserver
* Blå/gröna distributioner
* Program skalning
* Integrering med Azure DevOps
* Programövervakning

## <a name="spring-cloud-config-server"></a>Konfigurations Server för våren Cloud

Azure våren Cloud config server tillhandahåller en storskalig konfiguration i ett distribuerat system med stöd för både server-och klient sidan.  Azure våren Cloud config server är en central plats för att hantera program egenskaper i alla miljöer. Mer information finns i [referens för vår moln konfigurations Server](https://spring.io/projects/spring-cloud-config). 

## <a name="bluegreen-deployments"></a>Blå/gröna distributioner

Azure våren Cloud stöder blå/gröna distributioner för att släppa och uppdatera kod i produktions miljöer.  Detta mönster för ändrings hantering gör det möjligt för utvecklare att implementera funktioner och kod ändringar med säkerheten för omedelbar återgång vid behov.  Utvecklare kan koncentrera sig på att skriva kod med flera produktions miljöer för att uppdatera eller återställa kod ändringar utan att avbryta programmet.  Mer information om mellanlagrings miljöer och blå/gröna distributioner finns i den här [instruktions artikeln](spring-cloud-howto-staging-environment.md).

## <a name="cicd-pipeline-automation"></a>Automatisering av CI/CD-pipeline

Azure våren Cloud ger integrering med Azure DevOps med hjälp av Azure CLI.  Med Azure DevOps kan du automatisera kod integrering och distribution till ditt våren-program.  Mer information finns i den här [artikeln](spring-cloud-howto-cicd.md).

## <a name="application-scaling"></a>Program skalning

Med Azure våren Cloud kan du enkelt skala Micro-tjänsterna i moln instrument panelen för Azure våren.  Både antalet virtuella processorer och mängden minne som är tillgängligt för mikrotjänster kan skalas upp eller ned för att passa dina behov.  Skalningen börjar gälla om några sekunder och kräver inte kod ändringar eller omdistribution.  Om du vill veta mer kan du slutföra den här [självstudien](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Programövervakning

Med Microsofts verktyg för distribuerad spårning kan utvecklare felsöka och övervaka komplexa sammanlänkningar mellan mikrotjänster i ett program.  Genom att integrera [våren Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) med azures [Application Insights](../azure-monitor/monitor-reference.md)tillhandahåller Azure kraftfull, distribuerad spårnings funktion direkt från Azure Portal.  Om du vill veta mer kan du slutföra den här [självstudien](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Nästa steg

Kom igång genom att slutföra snabb starten för [våren Cloud](spring-cloud-quickstart.md)

Exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).