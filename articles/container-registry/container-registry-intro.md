---
title: Privata Docker-containerregister i Azure – översikt
description: Introduktion till Azure Container Registry-tjänsten, som tillhandahåller molnbaserade, hanterade, privata Docker-register.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 04/03/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: ba75d196bdb53fab104ab6c01391e762b4a3841b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270531"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introduktion till privata Docker-containerregister i Azure

Azure Container Registry är en hanterad [Docker-registertjänst](https://docs.docker.com/registry/) som baseras på Docker Registry 2.0 (öppen källkod). Skapa och underhåll Azure-containerregister för att lagra och hantera dina privata avbildningar av [Docker-containrar](https://www.docker.com/what-docker).

Använd behållarregister i Azure med dina befintliga och behållarutvecklings, eller Använd [ACR uppgifter](#azure-container-registry-tasks) att skapa avbildningar i Azure. Skapa på begäran eller automatisera helt med källkodsincheckning och skaparutlösare för uppdatering av basavbildning.

Information om Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).

## <a name="use-cases"></a>Användningsfall

Hämta avbildningar från ett Azure-containerregister till olika distributionsmål:

* **Skalbart dirigeringssystem** som hanterar program i containrar över kluster med värdar, inklusive [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) och [Docker Swarm](https://docs.docker.com/swarm/).
* **Azure-tjänster** som stöder att skapa och köra program i skala, inkluderar [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [ Service Fabric](/azure/service-fabric/), med mera.

Utvecklare kan även skicka till ett behållarregister som en del av ett arbetsflöde för utveckling av container. Du kan till exempel arbeta mot ett containerregister från ett verktyg för löpande integrering och distribution som [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) eller [Jenkins](https://jenkins.io/).

Konfigurera ACR-uppgifter för att automatiskt återskapa programavbildningar när deras Källavbildningen uppdateras, eller automatisera avbildningar när ditt team förbinder kod till en Git-lagringsplats. Skapa uppgifter i flera steg för att automatisera att bygga, testa och korrigeringar flera behållaravbildningar parallellt i molnet.

Azure tillhandahåller verktyg, t.ex. Azure-kommandoradsgränssnittet, Azure-portalen och API-stöd för att hantera dina Azure-behållarregister. Du kan också installera den [Docker-tillägg för Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) och [Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) tillägg att arbeta med din Azure-behållarregister. Hämta och push-överför avbildningar till ett Azure container registry eller köra ACR aktiviteter, allt inom Visual Studio Code.

## <a name="key-concepts"></a>Viktiga begrepp

* **Register** – Skapa ett eller flera containerregister i din Azure-prenumeration. Register är tillgängliga i tre SKU:er: [Basic, Standard och Premium](container-registry-skus.md), som stöder webhook-integrering, registerautentisering med Azure Active Directory och borttagning. Dra nytta av lokal, nätverksnära lagring av dina containeravbildningar genom att skapa ett register på samma Azure-plats som dina distributioner. Använd funktionen [geo-replikering](container-registry-geo-replication.md) i Premium-register för avancerad replikering och distributionsscenarier för containeravbildningar. Ett fullständigt kvalificerat registernamn har formatet `myregistry.azurecr.io`.

  Du [styr åtkomsten](container-registry-authentication.md) till en container med hjälp av en Azure-identitet, ett Azure Active Directory-kopplat [tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md) eller ett angivet administratörskonto. Logga in i registret med hjälp av Azure CLI eller standarden `docker login` kommando.

* **Databasen** – ett register innehåller en eller flera databaser som är virtuella grupper med behållaravbildningar med samma namn men olika taggar eller sammandrag. Azure Container Registry har stöd för namnområden för lagringsplatser på flera nivåer. Med namnområden för flera nivåer kan du gruppera samlingar med avbildningar relaterade till en viss app, eller en samling appar för specifika utvecklingsgrupper eller operativa team. Exempel:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` representerar en företagsomfattande avbildning
  * `myregistry.azurecr.io/warrantydept/dotnet-build` representerar en avbildning som används för att skapa .NET-appar som delas på garantiavdelningen.
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` representerar en webbavbildning, grupperad i appen för kundöverföringar, ägs av garantiavdelningen.

* **Avbildning** – Lagras på en lagringsplats. Varje avbildning är en skrivskyddad ögonblicksbild av en Docker-kompatibel container. Azure-containerregister kan innehålla både Windows- och Linux-avbildningar. Du styr avbildningsnamnen för alla containerdistributioner. Använd [Docker-standardkommandon](https://docs.docker.com/engine/reference/commandline/) för att skicka avbildningar till en lagringsplats, eller för att hämta en avbildning från en lagringsplats. Förutom containeravbildningar lagrar Azure Container Registry [relaterade innehållsformat](container-registry-image-formats.md) som [Helm-diagram](container-registry-helm-repos.md) som används för att distribuera program till Kubernetes.

* **Container** – En container definierar programvara och dess beroenden och är inneslutna i ett komplett filsystem, inklusive kod, runtime, systemverktyg och bibliotek. Kör Docker-containrar baserat på Windows- eller Linux-avbildningar som du hämtar från ett containerregister. Containrar som körs på en enskild dator delar operativsystemets kernel. Docker-containrar är helt portabla till alla större Linux-distributioner, macOS och Windows.

## <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

[Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks) är en uppsättning funktioner i Azure Container Registry som tillhandahåller strömlinjeformade och effektiva avbildningsversioner för Docker-behållaren i Azure. Använd ACR Tasks för att utöka din utvecklings inre-loop till molnet genom att avlasta `docker build`-åtgärder till Azure. Konfigurera skaparuppgifter för att automatisera din korrigeringspipeline för operativsystems- och ramverkscontainrar och skapa avbildningar automatiskt när ditt team checkar in kod för källkontroll.

[Uppgifter i flera steg](container-registry-tasks-overview.md#multi-step-tasks) ger steg baserade aktivitetsdefinition och körning för att skapa, testa och korrigeringar behållaravbildningar i molnet. Uppgiftsstegen definierar enskilda behållaravbildningars bygg- och push-åtgärder. De kan också definiera körningen av en eller flera behållare så varje steg använder behållaren som sin körningsmiljö.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett containerregister med hjälp av Azure Portal](container-registry-get-started-portal.md)
* [Skapa ett containerregister med hjälp av Azure CLI](container-registry-get-started-azure-cli.md)
* [Automatisera framework uppdatering med ACR uppgifter av operativsystem och](container-registry-tasks-overview.md)
