---
title: Hanterade containerregister
description: Introduktion till Azure Container Registry-tjänsten, som tillhandahåller molnbaserade, hanterade, privata Docker-register.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 1992a2a63d16a955d136459f5dbaece7df815c71
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77132033"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introduktion till privata Docker-containerregister i Azure

Azure Container Registry är en hanterad, privat Docker-registertjänst baserad på Hamnregistret 2.0 med öppen källkod. Skapa och underhåll Azure-behållarregister för att lagra och hantera dina privata Docker-behållaravbildningar och relaterade artefakter.

Använd Azure-behållarregister med dina befintliga behållarutvecklings- och distributionspipelor, eller använd Azure Container Registry Tasks för att skapa behållaravbildningar i Azure. Bygg på begäran, eller helt automatisera byggen med utlösare som källkods commits och basavbildningsuppdateringar.

Mer information om Docker- och registerbegrepp finns i [Översikten över Docker](https://docs.docker.com/engine/docker-overview/) och [Om register, databaser och avbildningar](container-registry-concepts.md).

## <a name="use-cases"></a>Användningsfall

Hämta avbildningar från ett Azure-containerregister till olika distributionsmål:

* **Skalbart dirigeringssystem** som hanterar program i containrar över kluster med värdar, inklusive [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) och [Docker Swarm](https://docs.docker.com/swarm/).
* **Azure-tjänster** som stöder att skapa och köra program i skala, inkluderar [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [ Service Fabric](/azure/service-fabric/), med mera.

Utvecklare kan även skicka till ett behållarregister som en del av ett arbetsflöde för utveckling av container. Rikta till exempel ett behållarregister från ett kontinuerligt [integrations-](/azure/devops/pipelines/ecosystems/containers/acr-template) och leveransverktyg som Azure Pipelines eller [Jenkins](https://jenkins.io/).

Konfigurera ACR-uppgifter för att automatiskt återskapa programavbildningar när deras basavbildningar uppdateras, eller automatisera bildversioner när teamet genomför kod till en Git-databas. Skapa flerstegsuppgifter för att automatisera att skapa, testa och korrigera flera behållaravbildningar parallellt i molnet.

Azure tillhandahåller verktyg, inklusive Azure Command-Line Interface, Azure Portal och API-stöd för att hantera dina Azure-behållarregister. Installera eventuellt [Docker-tillägget för Visual Studio-kod](https://code.visualstudio.com/docs/azure/docker) och [Azure-kontotillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) för att arbeta med dina Azure-behållarregister. Hämta och skicka avbildningar till ett Azure-behållarregister eller kör ACR-uppgifter, allt i Visual Studio Code.

## <a name="key-features"></a>Huvudfunktioner

* **RegisterSKU:** Skapa ett eller flera behållarregister i din Azure-prenumeration. Register finns i tre SKU:er: [Basic, Standard och Premium](container-registry-skus.md), som alla stöder integrering av webhook, registerautentisering med Azure Active Directory och borttagningsfunktioner. Dra nytta av lokal, nätverksnära lagring av dina containeravbildningar genom att skapa ett register på samma Azure-plats som dina distributioner. Använd funktionen [geo-replikering](container-registry-geo-replication.md) i Premium-register för avancerad replikering och distributionsscenarier för containeravbildningar. 

* **Säkerhet och åtkomst** - Du loggar in i ett `docker login` register med hjälp av Azure CLI eller standardkommandot. Azure Container Registry överför behållaravbildningar via HTTPS och stöder TLS för att skydda klientanslutningar. 

  > [!IMPORTANT]
  > Från och med den 13 januari 2020 kräver Azure Container Registry alla säkra anslutningar från servrar och program för att använda TLS 1.2. Aktivera TLS 1.2 med hjälp av en nyligen dockerklient (version 18.03.0 eller senare). Stöd för TLS 1.0 och 1.1 kommer att dras tillbaka. 

  Du [styr åtkomsten](container-registry-authentication.md) till en container med hjälp av en Azure-identitet, ett Azure Active Directory-kopplat [tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md) eller ett angivet administratörskonto. Använd rollbaserad åtkomstkontroll (RBAC) för att tilldela användare eller system detaljerade behörigheter till ett register.

  Säkerhetsfunktioner i Premium SKU inkluderar [innehållsförtroende](container-registry-content-trust.md) för [signering av avbildningstaggar och brandväggar och virtuella nätverk (förhandsversion)](container-registry-vnet.md) för att begränsa åtkomsten till registret. Azure Security Center integreras eventuellt med Azure Container Registry för att [skanna avbildningar](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) när en avbildning skjuts till ett register.

* **Bilder och artefakter som stöds** – Grupperade i en databas är varje bild en skrivskyddad ögonblicksbild av en Docker-kompatibel behållare. Azure-containerregister kan innehålla både Windows- och Linux-avbildningar. Du styr avbildningsnamnen för alla containerdistributioner. Använd [Docker-standardkommandon](https://docs.docker.com/engine/reference/commandline/) för att skicka avbildningar till en lagringsplats, eller för att hämta en avbildning från en lagringsplats. Förutom Docker-behållaravbildningar lagrar Azure Container Registry [relaterade innehållsformat](container-registry-image-formats.md) som [Helm-diagram](container-registry-helm-repos.md) och avbildningar som är byggda i [OCI-specifikationsspecifikationen (Open Container Initiative).](https://github.com/opencontainers/image-spec/blob/master/spec.md)

* **Automatiserade avbildningsversioner** – Använd ACR-uppgifter [(Azure Container Registry Tasks)](container-registry-tasks-overview.md) för att effektivisera byggande, testning, pushning och distribution av avbildningar i Azure. Använd till exempel ACR-uppgifter för att utöka din inre `docker build` utvecklingsloop till molnet genom att avlasta åtgärder till Azure. Konfigurera skaparuppgifter för att automatisera din korrigeringspipeline för operativsystems- och ramverkscontainrar och skapa avbildningar automatiskt när ditt team checkar in kod för källkontroll.

  [Multistegsuppgifter](container-registry-tasks-overview.md#multi-step-tasks) ger stegbaserad uppgiftsdefinition och körning för att skapa, testa och korrigera behållaravbildningar i molnet. Uppgiftsstegen definierar enskilda containeravbildningars bygg- och push-åtgärder. De kan också definiera körningen av en eller flera container så varje steg använder containern som sin körningsmiljö.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett containerregister med hjälp av Azure Portal](container-registry-get-started-portal.md)
* [Skapa ett behållarregister med Azure CLI](container-registry-get-started-azure-cli.md)
* [Automatisera behållarbyggen och underhåll med ACR-uppgifter](container-registry-tasks-overview.md)