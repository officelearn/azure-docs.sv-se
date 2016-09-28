<properties
   pageTitle="Övervaka ett Azure Container Service-kluster med Sysdig | Microsoft Azure"
   description="Övervaka ett Azure Container Service-kluster med Sysdig."
   services="container-service"
   documentationCenter=""
   authors="rbitia"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Behållare, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="t-ribhat"/>


# Övervaka ett Azure Container Service-kluster med Sysdig

I den här artikeln ska vi distribuera Sysdig-agenter till alla agentnoder i ditt Azure Container Service-kluster. Du behöver ett konto hos Sysdig för den här konfigurationen. 

## Krav 

[Distribuera](container-service-deployment.md) och [anslut](container-service-connect.md) ett kluster som konfigurerats av Azure Container Service. Utforska [Marathon-gränssnittet](container-service-mesos-marathon-ui.md). Gå till [http://app.sysdigcloud.com](http://app.sysdigcloud.com) för att ställa in ett Sysdig-molnkonto. 

## Sysdig

Sysdig är en övervakningstjänst som gör att du kan övervaka dina behållare i klustret. Sysdig är kända för att hjälpa till med felsökning men innehåller även grundläggande övervakningsmätvärden för processor, nätverk, minne och I/O. Sysdig gör det enkelt att se vilka behållare som arbetar hårdast eller använder mest minne och CPU. Den här vyn finns i avsnittet ”Översikt”, som för närvarande är en betaversion. 

![Sysdig-gränssnitt](./media/container-service-monitoring-sysdig/sysdig6.png) 

## Konfigurera en Sysdig-distribution med Marathon

Här visas hur du konfigurerar och distribuerar Sysdig-program till klustret med Marathon. 

Gå till ditt DC/OS-gränssnittet via [http://localhost:80/](http://localhost:80/) När du kommer till DC/OS-användargränssnittet navigerar du till ”Universe”, som finns längst ned till vänster och söker sedan efter ”Sysdig.”

![Sysdig i DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig1.png)

Nu för att slutföra konfigurationen behöver du ett Sysdig-molnkonto eller ett kostnadsfritt utvärderingskonto. När du har loggat in på Sysdig-molnwebbplatsen klickar du på ditt användarnamn. Din ”snabbtangent” bör nu visas på sidan. 

![API-nyckel för Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Ange härnäst din snabbtangent i Sysdig-konfigurationen i DC/OS Universe. 

![Sysdig-konfigurationen i DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig3.png)

Nu anger du instanserna till 10000000 så när du har lagt till en ny nod i klustret, kommer Sysdig automatiskt att distribuera en agent till den nya noden. Det här är en tillfällig lösning för att kontrollera att Sysdig distribueras till alla nya agenter i klustret. 

![Sysdig-konfigurationen i DC/OS Universe-instanser](./media/container-service-monitoring-sysdig/sysdig4.png)

När du har installerat paketet navigerar du tillbaka till Sysdig-gränssnittet. Nu kan du utforska de olika användningsmätvärdena för behållarna i klustret. 


<!--HONumber=Sep16_HO3-->


