---
title: Privata Docker-behållarregister i Azure
description: Introduktion till Azure Container Registry-tjänsten, som tillhandahåller molnbaserade, hanterade, privata Docker-register.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview
ms.date: 01/22/2018
ms.author: stevelas
ms.custom: mvc
ms.openlocfilehash: fc5ea475ebef9ec5c590bd9e696025fb845cc4cf
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introduktion till privata Docker-behållarregister i Azure

Azure Container Registry är en hanterad [Docker-registertjänst](https://docs.docker.com/registry/) som baseras på Docker Registry 2.0 (öppen källkod). Skapa och underhåll Azure-behållarregister för att lagra och hantera dina privata avbildningar av [Docker-behållare](https://www.docker.com/what-docker). Använd behållarregister i Azure med dina befintliga behållarutvecklings- och distributionspipelines och dra nytta av andras kunskaper i Docker-communityn.

Bakgrundsinformation om Docker och behållare finns i [Användarhandbok för Docker](https://docs.docker.com/engine/userguide/).

## <a name="use-cases"></a>Användningsfall
Hämta avbildningar från ett Azure-behållarregister till olika distributionsmål:

* **Skalbart dirigeringssystem** som hanterar program i behållare över kluster med värdar, inklusive [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) och [Kubernetes](http://kubernetes.io/docs/).
* **Azure-tjänster** som stöder utveckling och körning av program i stor skala, bland annat [Container Service](../container-service/index.yml), [App Service](/app-service/index.md), [Batch](../batch/index.yml) och [Service Fabric](/azure/service-fabric/).

Utvecklare kan även skicka till ett behållarregister som en del av ett arbetsflöde för utveckling av behållare. Du kan till exempel arbeta mot ett behållarregister från ett verktyg för löpande integrering och distribution som [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) eller [Jenkins](https://jenkins.io/).

## <a name="key-concepts"></a>Viktiga begrepp
* **Register** – Skapa en eller flera behållarregister i din Azure-prenumeration. Register är tillgängliga i tre SKU:er [Basic, Standard och Premium](container-registry-skus.md), och var och en stöder webhook-integrering, registerautentisering med Azure Active Directory och borttagning. Dra nytta av lokal, nätverksnära lagring av dina behållaravbildningar genom att skapa ett register på samma Azure-plats som dina distributioner. Använd funktionen [geo-replikering](container-registry-geo-replication.md) i Premium-register för avancerad replikering och distributionsscenarier för behållaravbildningar. Ett fullständigt kvalificerat registernamn har formatet `myregistry.azurecr.io`.

  Du [styr åtkomsten](container-registry-authentication.md) till en behållare med hjälp av ett Azure Active Directory-kopplat [tjänstobjekt](../active-directory/active-directory-application-objects.md) eller ett angivet administratörskonto. Kör `docker login`-standardkommandot för att autentisera med ett register.

* **Lagringsplats** – Ett register innehåller en eller flera databaser, som är grupper med behållaravbildningar. Azure Container Registry har stöd för namnområden för lagringsplatser på flera nivåer. Med namnområden för flera nivåer kan du gruppera samlingar med avbildningar relaterade till en viss app, eller en samling appar för specifika utvecklingsgrupper eller operativa team. Till exempel:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` representerar en företagsomfattande avbildning
  * `myregistry.azurecr.io/warrantydept/dotnet-build` representerar en avbildning som används för att skapa .NET-appar, som delas på garantiavdelningen.
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` representerar en webbavbildning, grupperad i appen för kundöverföringar, som ägs av garantiavdelningen.

* **Avbildning** – Lagras på en lagringsplats. Varje avbildning är en skrivskyddad ögonblicksbild av en Docker-behållare. Azure-behållarregister kan innehålla både Windows- och Linux-avbildningar. Du styr avbildningsnamnen för alla behållardistributioner. Använd [Docker-standardkommandon](https://docs.docker.com/engine/reference/commandline/) för att skicka avbildningar till en lagringsplats, eller för att hämta en avbildning från en lagringsplats.

* **Behållare** – Behållare definierar programvara och dess beroenden och är inneslutna i ett komplett filsystem, inklusive kod, runtime, systemverktyg och bibliotek. Kör Docker-behållare baserat på Windows- eller Linux-avbildningar som du hämtar från ett behållarregister. Behållare som körs på en enskild dator delar operativsystemets kernel. Docker-behållare är helt portabla till alla större Linux-distributioner, macOS och Windows.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett behållarregister med hjälp av Azure Portal](container-registry-get-started-portal.md)
* [Skapa ett behållarregister med hjälp av Azure CLI](container-registry-get-started-azure-cli.md)
* [Skicka din första avbildning med hjälp av Docker CLI](container-registry-get-started-docker-cli.md)
