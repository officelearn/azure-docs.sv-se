---
title: "Dockerbehållarvärdar i Azure-molnet | Microsoft Docs"
description: "Azure Container Service erbjuder ett sätt att förenkla skapande, konfiguration och hantering av ett kluster på virtuella datorer som är förkonfigurerade för att köra program i behållare."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, behållare, Micro-tjänster, Mesos, Azure"
ms.assetid: 52da4163-1182-4b2e-be00-4951e5c1da16
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 31897e11abfe70ed08381f0d13c6bdabe56c28ed
ms.openlocfilehash: 04fabadb1c713deb007b740369496b61bb5f424f
ms.lasthandoff: 03/02/2017



---
# <a name="introduction-to-docker-container-hosting-solutions-with-azure-container-service"></a>Introduktion till värdlösningar med Dockerbehållare med Azure Container Service 
Azure Container Service gör det enklare för dig att skapa, konfigurera och hantera av ett kluster på virtuella datorer som är förkonfigurerade för att köra program i behållare. Det använder en optimerad konfiguration av populära verktyg för öppen källkod för schemaläggning och dirigering. På så sätt kan du använda dina befintliga kunskaper eller använda en stor och växande mängd communityexpertis för att distribuera och hantera behållarbaserade program i Microsoft Azure.

![Med Azure Container Service kan du hantera program i behållare på flera värdar i Azure.](./media/acs-intro/acs-cluster-new.png)

I Azure Container Service används formatet med Docker-behållare så att dina programbehållare är fullständigt bärbara. Formatet stöder Marathon och DC/OS, Docker Swarm eller Kubernetes så att du kan skala programmen till tusentals behållare eller till och med tiotusentals.

Genom att använda Azure Container Service kan du dra nytta av Azures funktioner på företagsnivå samtidigt som programmen fortsätter att vara bärbara – det gäller även orkestreringslagren.

## <a name="using-azure-container-service"></a>Använda Azure Container Service
Vårt mål med Azure Container Service är att tillhandahålla en behållarvärdmiljö genom att använda verktyg och teknik med öppen källkod som är populära bland kunderna idag. Därför exponerar vi standard API-slutpunkter för den dirigering du väljer (DC/OS, Docker Swarm eller Kubernetes). Genom att använda dessa slutpunkter kan du använda all programvara som kan kommunicera till slutpunkterna. För exempelvis Docker Swarm-slutpunkten kan du använda kommandoradsgränssnittet Docker (CLI). För DC/OS kanske du väljer DCOS CLI. För Kubernetes kanske du väljer `kubectl`.

## <a name="creating-a-docker-cluster-by-using-azure-container-service"></a>Skapa ett Docker-kluster med Azure Container Service
Om du vill börja använda Azure Container Service distribuerar du ett Azure Container Service-kluster via portalen (sök efter **Azure Container Service** på Marketplace) med hjälp av en Azure Resource Manager-mall ([Docker Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm), [DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) eller [Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)) eller med [Azure CLI 2.0](container-service-create-acs-cluster-cli.md). De tillhandahållna snabbstartsmallarna kan ändras så att de inkluderar ytterligare eller avancerad Azure-konfiguration. Mer information finns i [Distribuera ett Azure Container Service-kluster](container-service-deployment.md).

## <a name="deploying-an-application"></a>Distribuera ett program
Med Azure Container Service kan du välja mellan Docker Swarm, DC/OS och Kubernetes för dirigering. Hur du distribuerar programmet beror på ditt val av dirigering.

### <a name="using-dcos"></a>Använda DC/OS
DC/OS är ett distribuerat operativsystem som baseras på Apache Mesos distribuerade systemkernel. Apache Mesos finns i Apache Software Foundation och listar några av de [största namnen inom IT](http://mesos.apache.org/documentation/latest/powered-by-mesos/) som användare och bidragsgivare.

![Azure Container Service konfigurerat för DC/OS visar agenter och huvudnoder.](media/acs-intro/dcos.png)

DC/OS och Apache Mesos har en imponerande funktionsuppsättning:

* Beprövad skalbarhet
* Feltoleranta master och slavar med Apache ZooKeeper
* Stöd för Docker-formaterade behållare
* Intern isolering mellan aktiviteter med Linux-behållare
* Schemaläggning med flera resurser (minne, CPU, disk och portar)
* Java-, Python- och C++-API:er för att utveckla nya parallella program
* Ett webbgränssnitt för att visa klustertillstånd

Som standard innehåller DC/OS som körs på Azure Container Service Marathon-dirigeringsplattformen för schemaläggning av arbetsbelastningar. Med DC/OS-distributionen av ACS ingår dock Mesosphere Universe med tjänster som kan läggas till för din tjänst. Exempel på tjänster som ingår i Universe är Spark, Hadoop, Cassandra och många fler.

![DC/OS Universe i Azure Container Service](media/dcos/universe.png)

#### <a name="using-marathon"></a>Använda Marathon
Marathon är ett klusteromfattande init- och kontrollsystem för tjänster i cgroups – eller, när det gäller Azure Container Service, Docker-formaterade behållare. Marathon tillhandahåller ett webbgränssnitt som du kan distribuera dina program från. Du kan komma åt det via en URL-adress som ser ut ungefär som `http://DNS_PREFIX.REGION.cloudapp.azure.com` där DNS\_PREFIX och REGION definieras vid tidpunkten för distribution. Du kan naturligtvis också ange ett eget DNS-namn. Mer information om hur du kör en behållare med Marathon-webbgränssnittet finns i avsnittet om [hur du hanterar DC/OS-behållare via webbgränssnittet för Marathon](container-service-mesos-marathon-ui.md).

![Marathon-programlista](media/dcos/marathon-applications-list.png)

Du kan också använda REST API:er för att kommunicera med Marathon. Det finns ett antal klientbibliotek som är tillgängliga för varje verktyg. De täcker flera olika språk – och naturligtvis kan du använda HTTP-protokollet på ett annat språk. Dessutom har många DevOps-verktyg stöd för Marathon. Detta ger maximal flexibilitet för driftsgruppen när du arbetar med Azure Container Service-kluster. Mer information om hur du kör en behållare med REST-API:et för Marathon finns i avsnittet om [hur du hanterar DC/OS-behållare via REST-API:et för Marathon](container-service-mesos-marathon-rest.md).

### <a name="using-docker-swarm"></a>Använda Docker Swarm
Docker Swarm tillhandahåller interna kluster för Docker. Eftersom Docker Swarm tillhandahåller standard-API för Docker API kan vilket verktyg som helst som redan kommunicerar med en Docker-daemon använda Swarm för att transparent skala till flera värdar i Azure Container Service.

![Azure Container Service konfigurerat för Swarm.](media/acs-intro/acs-swarm2.png)

Verktyg som stöds för att hantera behållare i ett Swarm-kluster omfattar, men är inte begränsat till, följande:

* Dokku
* Docker CLI och Docker Compose
* Krane
* Jenkins

### <a name="using-kubernetes"></a>Använda Kubernetes
Kubernetes är ett populärt dirigeringsverktyg för behållare i produktionsklass med öppen källkod. Kubernetes automatiserar distributionen, skalningen och hanteringen av program som använder behållare. Eftersom verktyget är baserat öppen källkod och drivs av communityn fungerar det smidigt i Azure Container Service och kan användas till att distribuera behållare i stor skala i Azure Container Service.

![Azure Container Service konfigurerat för Kubernetes.](media/acs-intro/kubernetes.png)

Det har en omfattande uppsättning funktioner. Till exempel:
* Horisontell skalning
* Tjänstidentifiering och belastningsutjämning
* Hemligheter och konfigurationshantering
* API-baserade automatiserade distributioner och återställningar
* Självåterställning




## <a name="videos"></a>Videoklipp
Komma igång med Azure Container Service (101):  

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Container-Service-101/player]
>
>

Building Applications Using the Azure Container Service (Bygga program med Azure Container Service) (version 2016)

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/B822/player]
>
>

## <a name="next-steps"></a>Nästa steg

Distribuera ett behållartjänstkluster med hjälp av [portalen](container-service-deployment.md) eller [Azure CLI 2.0](container-service-create-acs-cluster-cli.md).
