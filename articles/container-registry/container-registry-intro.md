---
title: Privata Docker-containerregister i Azure – översikt
description: Introduktion till Azure Container Registry-tjänsten, som tillhandahåller molnbaserade, hanterade, privata Docker-register.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 06/28/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: b8b4b5fc3ec15d921ff5580aff4d0202be1d38b9
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797903"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introduktion till privata Docker-containerregister i Azure

Azure Container Registry är en hanterad, privata Docker registry-tjänsten baserat på öppen källkod Docker Registry 2.0. Skapa och underhåll Azure-behållarregister för att lagra och hantera dina privata Docker-behållaravbildningar.

Använda Azure-behållarregister med dina befintliga och behållarutvecklings eller använda Azure Container Registry åtgärder för att skapa avbildningar i Azure. Skapa på begäran, eller helt automatisera versioner med utlösare som förbinder källkoden och grundläggande uppdateringar av avbildningar.

Mer information om Docker och registret begrepp finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/) och [om register, databaser och avbildningar](container-registry-concepts.md).

## <a name="use-cases"></a>Användningsfall

Hämta avbildningar från ett Azure-containerregister till olika distributionsmål:

* **Skalbart dirigeringssystem** som hanterar program i containrar över kluster med värdar, inklusive [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) och [Docker Swarm](https://docs.docker.com/swarm/).
* **Azure-tjänster** som stöder att skapa och köra program i skala, inkluderar [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [ Service Fabric](/azure/service-fabric/), med mera.

Utvecklare kan även skicka till ett behållarregister som en del av ett arbetsflöde för utveckling av container. Till exempel mot ett behållarregister från ett verktyg för löpande integrering och leverans som [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines.md) eller [Jenkins](https://jenkins.io/).

Konfigurera ACR-uppgifter för att automatiskt återskapa programavbildningar när deras Källavbildningen uppdateras, eller automatisera avbildningar när ditt team förbinder kod till en Git-lagringsplats. Skapa uppgifter i flera steg för att automatisera att bygga, testa och korrigeringar flera behållaravbildningar parallellt i molnet.

Azure tillhandahåller verktyg, t.ex. Azure-kommandoradsgränssnittet, Azure-portalen och API-stöd för att hantera dina Azure-behållarregister. Du kan också installera den [Docker-tillägg för Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) och [Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) tillägg att arbeta med din Azure-behållarregister. Hämta och push-överför avbildningar till ett Azure container registry eller köra ACR aktiviteter, allt inom Visual Studio Code.

## <a name="key-features"></a>Huvudfunktioner

* **Register-SKU: er** – skapa en eller flera behållarregister i Azure-prenumerationen. Register är tillgängliga i tre SKU:er: [Basic, Standard och Premium](container-registry-skus.md), som stöder webhook-integrering, registerautentisering med Azure Active Directory och borttagning. Dra nytta av lokal, nätverksnära lagring av dina containeravbildningar genom att skapa ett register på samma Azure-plats som dina distributioner. Använd funktionen [geo-replikering](container-registry-geo-replication.md) i Premium-register för avancerad replikering och distributionsscenarier för containeravbildningar. 

  Du [styr åtkomsten](container-registry-authentication.md) till en container med hjälp av en Azure-identitet, ett Azure Active Directory-kopplat [tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md) eller ett angivet administratörskonto. Logga in i registret med hjälp av Azure CLI eller standarden `docker login` kommando.

* **Stöd för avbildningar och artefakter** -grupperade i en databas kan varje avbildning är en skrivskyddad ögonblicksbild av en Docker-kompatibla behållare. Azure-containerregister kan innehålla både Windows- och Linux-avbildningar. Du styr avbildningsnamnen för alla containerdistributioner. Använd [Docker-standardkommandon](https://docs.docker.com/engine/reference/commandline/) för att skicka avbildningar till en lagringsplats, eller för att hämta en avbildning från en lagringsplats. Förutom Docker-behållaravbildningar, Azure Container Registry lagrar [relaterat innehåll format](container-registry-image-formats.md) som [Helm-diagram](container-registry-helm-repos.md) och bilder som är inbyggda i [öppna behållare initiativ OCI ()-avbildning Formatera specifikationen](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Azure Container Registry uppgifter** -Använd [Azure Container Registry uppgifter](container-registry-tasks-overview.md) (ACR aktiviteter) för att förenkla skapande, testning, push-överföring och distribution av avbildningar i Azure. Till exempel använda ACR uppgifter för att utöka din utveckling inre-loop till molnet genom att avlasta `docker build` åtgärder till Azure. Konfigurera skaparuppgifter för att automatisera din korrigeringspipeline för operativsystems- och ramverkscontainrar och skapa avbildningar automatiskt när ditt team checkar in kod för källkontroll.

  [Uppgifter i flera steg](container-registry-tasks-overview.md#multi-step-tasks) ger steg baserade aktivitetsdefinition och körning för att skapa, testa och korrigeringar behållaravbildningar i molnet. Uppgiftsstegen definierar enskilda behållaravbildningars bygg- och push-åtgärder. De kan också definiera körningen av en eller flera behållare så varje steg använder behållaren som sin körningsmiljö.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett containerregister med hjälp av Azure Portal](container-registry-get-started-portal.md)
* [Skapa ett containerregister med hjälp av Azure CLI](container-registry-get-started-azure-cli.md)
* [Automatisera behållare byggen och underhåll med ACR-uppgifter](container-registry-tasks-overview.md)
