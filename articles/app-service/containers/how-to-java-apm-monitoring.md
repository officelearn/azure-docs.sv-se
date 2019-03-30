---
title: Konfigurera Java-APM- och övervakningsverktyg på Linux – Azure App Service
description: Lär dig hur du skickar loggar och mått för din Java-program som körs på App Service på Linux till NewRelic och appen Dynamics APM-providers
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 03/21/2019
ms.author: astay;routlaw
ms.custom: seodec18
ms.openlocfilehash: e6a22258266bda18c9ff79590d88e70d512f6c77
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630146"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Så här gör du: Programprestandaövervakning verktyg med Java-appar i Azure App Service i Linux

Den här artikeln visar hur du ansluter Java-program som distribueras på Azure App Service i Linux med den NewRelic och AppDynamics för programprestandaövervakning (APM) plattformar.

## <a name="configure-new-relic"></a>Konfigurera New Relic

1. Skapa ett konto för NewRelic på [NewRelic.com](https://newrelic.com/signup)
2. Ladda ned Java-agenten från NewRelic, den kommer att ha ett filnamn som liknar `newrelic-java-x.x.x.zip`.
3. Kopiera licensnyckeln, måste den att konfigurera agenten senare.
4. [SSH till din App Service-instans](/azure/app-service/containers/app-service-linux-ssh-support) och skapa en ny katalog `/home/site/wwwroot/apm`.
5. Ladda upp uppackat NewRelic Java-agentfilerna till en katalog under `/home/site/wwwroot/apm`. Filer för hanteringsagenten måste vara i `/home/site/wwwroot/apm/newrelic`.
6. Ändra YAML-fil på `/home/site/wwwroot/apm/newrelic/newrelic.yml` och Ersätt platshållarvärdet licens med din egen licensnyckel.
7. Bläddra till ditt program i App Service i Azure-portalen och skapa en ny inställning för programmet.
    - Om din app använder **Java SE**, skapa miljövariabeln `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Om du använder **Tomcat**, skapa miljövariabeln `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Om du använder **WildFly**, finns i dokumentationen för New Relic [här](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) anvisningar om hur du installerar Java-agenten och JBoss konfiguration.
    - Om du redan har en miljövariabel för `JAVA_OPTS` eller `CATALINA_OPTS`, lägga till den `javaagent` alternativet i slutet av det aktuella värdet.

## <a name="configure-appdynamics"></a>Konfigurera AppDynamics

1. Skapa ett konto för AppDynamics på [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Hämta Java-agenten från webbplatsen AppDynamics, namnet på filen ska vara detsamma som `AppServerAgent-x.x.x.xxxxx.zip`
1. [SSH till din App Service-instans](/azure/app-service/containers/app-service-linux-ssh-support) och skapa en ny katalog `/home/site/wwwroot/apm`.
1. Ladda upp Java-agentfilerna till en katalog under `/home/site/wwwroot/apm`. Filer för hanteringsagenten måste vara i `/home/site/wwwroot/apm/appdynamics`.
1. Bläddra till ditt program i App Service i Azure-portalen och skapa en ny inställning för programmet.
    - Om du använder **Java SE**, skapa miljövariabeln `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` där `<YOUR_SITE_NAME>` är din App Service-namn.
    - Om du använder **Tomcat**, skapa miljövariabeln `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` där `<YOUR_SITE_NAME>` är din App Service-namn.
    - Om du använder **WildFly**, finns i dokumentationen för AppDynamics [här](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) anvisningar om hur du installerar Java-agenten och JBoss konfiguration.
