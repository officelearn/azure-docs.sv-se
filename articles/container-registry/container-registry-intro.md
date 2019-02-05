---
title: Privata Docker-containerregister i Azure – översikt
description: Introduktion till Azure Container Registry-tjänsten, som tillhandahåller molnbaserade, hanterade, privata Docker-register.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 09/25/2018
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 0118c17045b86c88d8d92048787a20bd7d309c0b
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55298355"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introduktion till privata Docker-containerregister i Azure

Azure Container Registry är en hanterad [Docker-registertjänst](https://docs.docker.com/registry/) som baseras på Docker Registry 2.0 (öppen källkod). Skapa och underhåll Azure-containerregister för att lagra och hantera dina privata avbildningar av [Docker-containrar](https://www.docker.com/what-docker).

Använd containerregister i Azure med dina befintliga utvecklings- och distributionspipelines för containrar. Använd Azure Container Registry Build (ACR Build) för att skapa behållaravbildningar i Azure. Skapa på begäran eller automatisera helt med källkodsincheckning och skaparutlösare för uppdatering av basavbildning.

Information om Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).

## <a name="use-cases"></a>Användningsfall

Hämta avbildningar från ett Azure-containerregister till olika distributionsmål:

* **Skalbart dirigeringssystem** som hanterar program i containrar över kluster med värdar, inklusive [Kubernetes](http://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) och [Docker Swarm](https://docs.docker.com/swarm/).
* **Azure-tjänster** som stöder att skapa och köra program i skala, inkluderar [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [ Service Fabric](/azure/service-fabric/), med mera.

Utvecklare kan även skicka till ett behållarregister som en del av ett arbetsflöde för utveckling av container. Du kan till exempel arbeta mot ett containerregister från ett verktyg för löpande integrering och distribution som [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) eller [Jenkins](https://jenkins.io/).

Konfigurera [ACR Tasks](#azure-container-registry-build) för att automatiskt återskapa programavbildningar när deras basavbildningar uppdateras. Använd ACR Tasks för att automatisera avbildningsversioner när ditt team checkar in kod till en Git-lagringsplats.

## <a name="key-concepts"></a>Viktiga begrepp

* **Register** – Skapa ett eller flera containerregister i din Azure-prenumeration. Register är tillgängliga i tre SKU:er: [Basic, Standard och Premium](container-registry-skus.md), och var och en stöder webhook-integrering, registerautentisering med Azure Active Directory och borttagning. Dra nytta av lokal, nätverksnära lagring av dina containeravbildningar genom att skapa ett register på samma Azure-plats som dina distributioner. Använd funktionen [geo-replikering](container-registry-geo-replication.md) i Premium-register för avancerad replikering och distributionsscenarier för containeravbildningar. Ett fullständigt kvalificerat registernamn har formatet `myregistry.azurecr.io`.

  Du [styr åtkomsten](container-registry-authentication.md) till en container med hjälp av en Azure-identitet, ett Azure Active Directory-kopplat [tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md) eller ett angivet administratörskonto. Logga in i registret med hjälp av kommandoradsgränssnittet för Azure eller `docker login`-standardkommandot.

* **Lagringsplats** – Ett register innehåller en eller flera lagringsplatser, som lagrar grupper med containeravbildningar. Azure Container Registry har stöd för namnområden för lagringsplatser på flera nivåer. Med namnområden för flera nivåer kan du gruppera samlingar med avbildningar relaterade till en viss app, eller en samling appar för specifika utvecklingsgrupper eller operativa team. Exempel:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` representerar en företagsomfattande avbildning
  * `myregistry.azurecr.io/warrantydept/dotnet-build` representerar en avbildning som används för att skapa .NET-appar, som delas på garantiavdelningen.
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` representerar en webbavbildning, grupperad i appen för kundöverföringar, som ägs av garantiavdelningen.

* **Avbildning** – Lagras på en lagringsplats. Varje avbildning är en skrivskyddad ögonblicksbild av en Docker-kompatibel container. Azure-containerregister kan innehålla både Windows- och Linux-avbildningar. Du styr avbildningsnamnen för alla containerdistributioner. Använd [Docker-standardkommandon](https://docs.docker.com/engine/reference/commandline/) för att skicka avbildningar till en lagringsplats, eller för att hämta en avbildning från en lagringsplats. Förutom containeravbildningar lagrar Azure Container Registry [relaterade innehållsformat](container-registry-image-formats.md) som [Helm-diagram](container-registry-helm-repos.md) som används för att distribuera program till Kubernetes.

* **Container** – En container definierar programvara och dess beroenden och är inneslutna i ett komplett filsystem, inklusive kod, runtime, systemverktyg och bibliotek. Kör Docker-containrar baserat på Windows- eller Linux-avbildningar som du hämtar från ett containerregister. Containrar som körs på en enskild dator delar operativsystemets kernel. Docker-containrar är helt portabla till alla större Linux-distributioner, macOS och Windows.

## <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

[Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks) är en uppsättning funktioner i Azure Container Registry som tillhandahåller strömlinjeformade och effektiva avbildningsversioner för Docker-behållaren i Azure. Använd ACR Tasks för att utöka din utvecklings inre-loop till molnet genom att avlasta `docker build`-åtgärder till Azure. Konfigurera skaparuppgifter för att automatisera din korrigeringspipeline för operativsystems- och ramverkscontainrar och skapa avbildningar automatiskt när ditt team checkar in kod för källkontroll.

[Uppgifter i flera steg](container-registry-tasks-overview.md#multi-step-tasks-preview), en förhandsversion av ACR Tasks som innehåller steg-baserad aktivitetsdefinition och körning för att skapa, testa och patcha behållaravbildningar i molnet. Uppgiftsstegen definierar enskilda behållaravbildningars bygg- och push-åtgärder. De kan också definiera körningen av en eller flera behållare så varje steg använder behållaren som sin körningsmiljö.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett containerregister med hjälp av Azure Portal](container-registry-get-started-portal.md)
* [Skapa ett containerregister med hjälp av Azure CLI](container-registry-get-started-azure-cli.md)
* [Automatisera korrigering av operativsystem och ramverk med ACR Tasks](container-registry-tasks-overview.md)