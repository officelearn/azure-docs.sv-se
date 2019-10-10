---
title: Introduktion till Azure våren Cloud
description: Lär dig om funktionerna och fördelarna med Azure våren Cloud för att distribuera och hantera Java våren-program i Azure.
author: jpconnock
ms.service: spring-cloud
ms.topic: overview
ms.date: 10/04/2019
ms.author: jeconnoc
ms.openlocfilehash: c58e6b2a0c0de8295df65b44fbdeaeb2d4461948
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166540"
---
# <a name="what-is-azure-spring-cloud"></a>Vad är Azure våren Cloud?

Azure våren Cloud gör det enkelt att distribuera våren Boot-baserade mikroservice-program till Azure utan kod ändringar.  Med Azure våren Cloud kan utvecklare fokusera på sin kod genom att hantera livs cykeln för våren Cloud-program.  Våren Cloud tillhandahåller livs cykel hantering med omfattande övervakning och diagnostik, konfigurations hantering, tjänst identifiering, CI/CD-integrering, blå-grön distributioner med mera.

Som en del av Azures eko system gör Azure våren Cloud enkel bindning till andra Azure-tjänster, inklusive lagring, databaser, övervakning och mycket annat.

Azure våren Cloud är för närvarande en för hands version.  [Fyll i det här formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
)om du vill ha åtkomst till den här tjänsten medan den är i för hands version.

Kom igång genom att slutföra snabb starten för fjäder molnet med hjälp av [Azure CLI](spring-cloud-quickstart-launch-app-cli.md), [Azure Portal](spring-cloud-quickstart-launch-app-portal.md)eller [maven](spring-cloud-quickstart-launch-app-maven.md).

## <a name="application-configuration"></a>Program konfiguration

### <a name="spring-cloud-config-server"></a>Konfigurations Server för våren Cloud

Azure våren Cloud config server tillhandahåller en storskalig konfiguration i ett distribuerat system med stöd för både server-och klient sidan.  Config server är en central plats för att hantera program egenskaper i alla miljöer.  Om du vill veta mer kan du gå till [vår Cloud config server-referens](https://spring.io/projects/spring-cloud-config.md) och slutföra självstudien.

### <a name="enable-bluegreen-deployments"></a>Aktivera blå/gröna distributioner

Azure våren Cloud stöder blå/gröna distributioner för att släppa och uppdatera kod i produktions miljöer.  Genom att använda det här mönstret för ändrings hantering kan utvecklare implementera funktioner och kod ändringar med säkerheten för omedelbar återgång vid behov.  Med Azure kan utvecklare koncentrera sig på att skriva kod genom att hantera flera produktions miljöer och göra det enkelt att uppdatera eller återställa kod ändringar utan att avbryta programmet.  Mer information om mellanlagrings miljöer och blå/gröna distributioner finns i den här [instruktions artikeln](spring-cloud-howto-staging-environment.md).

### <a name="automate-cicd-pipelines"></a>Automatisera CI/CD-pipeliner

Azure våren Cloud ger integrering med Azure DevOps med hjälp av Azure CLI.  Med Azure DevOps kan du automatisera kod integrering och distribution till ditt våren-program.  Mer information finns i den här [artikeln](spring-cloud-howto-cicd.md).

### <a name="scale-your-application"></a>Skala ditt program

Med Azure våren Cloud kan du enkelt skala Micro-tjänsterna i moln instrument panelen för Azure våren.  Både antalet virtuella processorer och mängden minne som är tillgängligt för mikrotjänster kan skalas upp eller ned för att passa dina behov.  Skalningen börjar gälla om några sekunder och kräver inte kod ändringar eller omdistribution.  Om du vill veta mer kan du slutföra den här [självstudien](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Program övervakning

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Övervaka ditt program med hjälp av distribuerad spårning och Azure App insikter

Med Microsofts verktyg för distribuerad spårning kan utvecklare felsöka och övervaka komplexa sammanlänkningar mellan mikrotjänster i sina program.  Genom att integrera [våren Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) med azures [Application Insights](../azure-monitor/insights/insights-overview.md)tillhandahåller Azure kraftfull, distribuerad spårnings funktion direkt från Azure Portal.  Om du vill veta mer kan du slutföra den här [självstudien](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Nästa steg

- [Starta din våren Cloud-app från CLI](spring-cloud-quickstart-launch-app-cli.md)
