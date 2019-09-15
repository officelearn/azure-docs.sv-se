---
title: Privata Docker-containerregister i Azure – översikt
description: Introduktion till Azure Container Registry-tjänsten, som tillhandahåller molnbaserade, hanterade, privata Docker-register.
services: container-registry
author: stevelas
manager: gwallace
ms.service: container-registry
ms.topic: overview
ms.date: 06/28/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 2262948a8368f9448d876166b712ebaf670b5a56
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994361"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introduktion till privata Docker-containerregister i Azure

Azure Container Registry är en hanterad privat Docker-registerpost som baseras på Docker-registret 2,0 med öppen källkod. Skapa och underhåll Azure Container register för att lagra och hantera dina privata Docker-behållar avbildningar.

Använd Azures behållar register med din befintliga pipeline för utveckling och distribution av behållare, eller Använd Azure Container Registry uppgifter för att bygga behållar avbildningar i Azure. Bygg på begäran eller gör helt automatiserade versioner med utlösare, till exempel käll kods incheckningar och bas avbildnings uppdateringar.

Mer information om Docker-och Registry-koncept finns [](https://docs.docker.com/engine/docker-overview/) i Docker-översikten och [om register, databaser och avbildningar](container-registry-concepts.md).

## <a name="use-cases"></a>Användningsfall

Hämta avbildningar från ett Azure-containerregister till olika distributionsmål:

* **Skalbart dirigeringssystem** som hanterar program i containrar över kluster med värdar, inklusive [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) och [Docker Swarm](https://docs.docker.com/swarm/).
* **Azure-tjänster** som stöder att skapa och köra program i skala, inkluderar [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [ Service Fabric](/azure/service-fabric/), med mera.

Utvecklare kan även skicka till ett behållarregister som en del av ett arbetsflöde för utveckling av container. Du kan till exempel rikta in ett behållar register från ett kontinuerligt integrerings-och leverans verktyg, till exempel [Azure](/azure/devops/pipelines/ecosystems/containers/acr-template) -pipeliner eller [Jenkins](https://jenkins.io/).

Konfigurera ACR-aktiviteter för att automatiskt återskapa program avbildningar när deras bas avbildningar uppdateras, eller automatisera avbildningar när ditt team gör en kod till en git-lagringsplats. Skapa aktiviteter i flera steg för att automatisera skapandet, testning och korrigering av flera behållar avbildningar parallellt i molnet.

Azure tillhandahåller verktyg som inkluderar Azures kommando rads gränssnitt, Azure Portal och API-stöd för att hantera dina Azure Container register. Du kan också installera [Docker-tillägget för Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) och tillägget [Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) för att arbeta med dina Azure Container register. Hämta och push-avbildningar till ett Azure Container Registry, eller kör ACR-aktiviteter, allt i Visual Studio Code.

## <a name="key-features"></a>Huvudfunktioner

* **Register-SKU: er** – skapa en eller flera behållar register i din Azure-prenumeration. Register är tillgängliga i tre SKU:er: [Basic, standard och Premium](container-registry-skus.md), som har stöd för webhook-integrering, registerscanner med Azure Active Directory och ta bort funktioner. Dra nytta av lokal, nätverksnära lagring av dina containeravbildningar genom att skapa ett register på samma Azure-plats som dina distributioner. Använd funktionen [geo-replikering](container-registry-geo-replication.md) i Premium-register för avancerad replikering och distributionsscenarier för containeravbildningar. 

  Du [styr åtkomsten](container-registry-authentication.md) till en container med hjälp av en Azure-identitet, ett Azure Active Directory-kopplat [tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md) eller ett angivet administratörskonto. Logga in i registret med hjälp av Azure CLI eller standard `docker login` -kommandot.

* **Bilder och artefakter som stöds** – grupperade i en lagrings plats, är varje avbildning en skrivskyddad ögonblicks bild av en Docker-kompatibel behållare. Azure-containerregister kan innehålla både Windows- och Linux-avbildningar. Du styr avbildningsnamnen för alla containerdistributioner. Använd [Docker-standardkommandon](https://docs.docker.com/engine/reference/commandline/) för att skicka avbildningar till en lagringsplats, eller för att hämta en avbildning från en lagringsplats. Förutom Docker-behållar avbildningar, Azure Container Registry lagrar [relaterade innehålls format](container-registry-image-formats.md) , till exempel [Helm-diagram](container-registry-helm-repos.md) och bilder som skapats i [specifikationen Open container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Azure Container Registry uppgifter** – Använd [Azure Container Registry uppgifter](container-registry-tasks-overview.md) (ACR-aktiviteter) för att effektivisera skapandet, testning, spridning och distribution av avbildningar i Azure. Du kan till exempel använda ACR-aktiviteter för att utöka din utvecklings inre-slinga till molnet `docker build` genom att avlasta åtgärder till Azure. Konfigurera skaparuppgifter för att automatisera din korrigeringspipeline för operativsystems- och ramverkscontainrar och skapa avbildningar automatiskt när ditt team checkar in kod för källkontroll.

  [Aktiviteter med flera steg](container-registry-tasks-overview.md#multi-step-tasks) innehåller stegvisa aktivitets definitioner och körning för att skapa, testa och korrigera behållar avbildningar i molnet. Uppgiftsstegen definierar enskilda behållaravbildningars bygg- och push-åtgärder. De kan också definiera körningen av en eller flera behållare så varje steg använder behållaren som sin körningsmiljö.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett containerregister med hjälp av Azure Portal](container-registry-get-started-portal.md)
* [Skapa ett containerregister med hjälp av Azure CLI](container-registry-get-started-azure-cli.md)
* [Automatisera behållar versioner och underhåll med ACR-uppgifter](container-registry-tasks-overview.md)
