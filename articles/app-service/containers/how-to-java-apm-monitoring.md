---
title: Konfigurera Java-APM- och övervakningsverktyg med Azure App Service på Linux
description: Lär dig hur du skickar loggar och mått för din Java-program som körs på App Service på Linux till NewRelic och appen Dynamics APM-providers
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 11/27/2018
ms.author: astay;routlaw
ms.openlocfilehash: 06ae71fea1b85a74d87588d2635038c64c8540cc
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52500431"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Anvisningar: Prestandaövervakning av program-verktyg med Java-appar i Azure App Service i Linux

Den här artikeln visar hur du ansluter Java-program som distribueras på Azure App Service i Linux med den NewRelic och AppDynamics för programprestandaövervakning (APM) plattformar.

## <a name="configure-new-relic"></a>Konfigurera New Relic
1. Skapa ett konto för NewRelic på [NewRelic.com](https://newrelic.com/signup)
1. Ladda ned Java-agenten från NewRelic, den kommer att ha ett filnamn som liknar `newrelic-java-x.x.x.zip`.
1. Kopiera licensnyckeln, måste den att konfigurera agenten senare.
1. [SSH till din App Service-instans](/azure/app-service/containers/app-service-linux-ssh-support) och skapa en ny katalog `/home/site/wwwroot/apm`. 
1. Ladda upp uppackat NewRelic Java-agentfilerna till en katalog under `/home/site/wwwroot/apm`. Filer för hanteringsagenten måste vara i `/home/site/wwwroot/apm/newrelic`.
1. Ändra YAML-fil på `/home/site/wwwroot/apm/newrelic/newrelic.yml` och Ersätt platshållarvärdet licens med din egen licensnyckel.
1. Bläddra till ditt program i App Service i Azure-portalen och skapa en ny inställning för programmet.
    - Om din app använder **Java SE**, skapa miljövariabeln `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Om du använder **Tomcat**, skapa miljövariabeln `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Om du redan har en miljövariabel för `JAVA_OPTS` eller `CATALINA_OPTS`, lägga till den `javaagent` alternativet i slutet av det aktuella värdet.

## <a name="configure-appdynamics"></a>Konfigurera AppDynamics
1. Skapa ett konto för AppDynamics på [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Hämta Java-agenten från webbplatsen AppDynamics, namnet på filen ska vara detsamma som `AppServerAgent-x.x.x.xxxxx.zip`
1. [SSH till din App Service-instans](/azure/app-service/containers/app-service-linux-ssh-support) och skapa en ny katalog `/home/site/wwwroot/apm`. 
1. Ladda upp Java-agentfilerna till en katalog under `/home/site/wwwroot/apm`. Filer för hanteringsagenten måste vara i `/home/site/wwwroot/apm/appdynamics`.
1. IIn Azure portal, bläddra till programmet i App Service och skapa en ny inställning för programmet.
    - Om du använder **Java SE**, skapa miljövariabeln `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` där `<YOUR_SITE_NAME>` är din App Service-namn.
    - Om du använder **Tomcat**, skapa miljövariabeln `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` där `<YOUR_SITE_NAME>` är din App Service-namn.
