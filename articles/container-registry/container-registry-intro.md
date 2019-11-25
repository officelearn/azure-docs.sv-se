---
title: Managed container registries
description: Introduktion till Azure Container Registry-tjänsten, som tillhandahåller molnbaserade, hanterade, privata Docker-register.
author: stevelas
ms.topic: overview
ms.date: 06/28/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: fceaf312f35356acf18c0873a467ed43d11ddd83
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454936"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introduktion till privata Docker-containerregister i Azure

Azure Container Registry is a managed, private Docker registry service based on the open-source Docker Registry 2.0. Create and maintain Azure container registries to store and manage your private Docker container images.

Use Azure container registries with your existing container development and deployment pipelines, or use Azure Container Registry Tasks to build container images in Azure. Build on demand, or fully automate builds with triggers such as source code commits and base image updates.

For more about Docker and registry concepts, see the [Docker overview](https://docs.docker.com/engine/docker-overview/) and [About registries, repositories, and images](container-registry-concepts.md).

## <a name="use-cases"></a>Användningsfall

Hämta avbildningar från ett Azure-containerregister till olika distributionsmål:

* **Skalbart dirigeringssystem** som hanterar program i containrar över kluster med värdar, inklusive [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) och [Docker Swarm](https://docs.docker.com/swarm/).
* **Azure-tjänster** som stöder att skapa och köra program i skala, inkluderar [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [ Service Fabric](/azure/service-fabric/), med mera.

Utvecklare kan även skicka till ett behållarregister som en del av ett arbetsflöde för utveckling av container. For example, target a container registry from a continuous integration and delivery tool such as [Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) or [Jenkins](https://jenkins.io/).

Configure ACR Tasks to automatically rebuild application images when their base images are updated, or automate image builds when your team commits code to a Git repository. Create multi-step tasks to automate building, testing, and patching multiple container images in parallel in the cloud.

Azure provides tooling including Azure Command-Line Interface, Azure portal, and API support to manage your Azure container registries. Optionally install the [Docker Extension for Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) and the [Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) extension to work with your Azure container registries. Pull and push images to an Azure container registry, or run ACR Tasks, all within Visual Studio Code.

## <a name="key-features"></a>Huvudfunktioner

* **Registry SKUs** - Create one or more container registries in your Azure subscription. Registries are available in three SKUs: [Basic, Standard, and Premium](container-registry-skus.md), each of which supports webhook integration, registry authentication with Azure Active Directory, and delete functionality. Dra nytta av lokal, nätverksnära lagring av dina containeravbildningar genom att skapa ett register på samma Azure-plats som dina distributioner. Använd funktionen [geo-replikering](container-registry-geo-replication.md) i Premium-register för avancerad replikering och distributionsscenarier för containeravbildningar. 

  Du [styr åtkomsten](container-registry-authentication.md) till en container med hjälp av en Azure-identitet, ett Azure Active Directory-kopplat [tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md) eller ett angivet administratörskonto. Log in to the registry using the Azure CLI or the standard `docker login` command.

* **Supported images and artifacts** - Grouped in a repository, each image is a read-only snapshot of a Docker-compatible container. Azure-containerregister kan innehålla både Windows- och Linux-avbildningar. Du styr avbildningsnamnen för alla containerdistributioner. Använd [Docker-standardkommandon](https://docs.docker.com/engine/reference/commandline/) för att skicka avbildningar till en lagringsplats, eller för att hämta en avbildning från en lagringsplats. In addition to Docker container images, Azure Container Registry stores [related content formats](container-registry-image-formats.md) such as [Helm charts](container-registry-helm-repos.md) and images built to the [Open Container Initiative (OCI) Image Format Specification](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Azure Container Registry Tasks** - Use [Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks) to streamline building, testing, pushing, and deploying images in Azure. For example, use ACR Tasks to extend your development inner-loop to the cloud by offloading `docker build` operations to Azure. Konfigurera skaparuppgifter för att automatisera din korrigeringspipeline för operativsystems- och ramverkscontainrar och skapa avbildningar automatiskt när ditt team checkar in kod för källkontroll.

  [Multi-step tasks](container-registry-tasks-overview.md#multi-step-tasks) provide step-based task definition and execution for building, testing, and patching container images in the cloud. Uppgiftsstegen definierar enskilda containeravbildningars bygg- och push-åtgärder. De kan också definiera körningen av en eller flera container så varje steg använder containern som sin körningsmiljö.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett containerregister med hjälp av Azure Portal](container-registry-get-started-portal.md)
* [Skapa ett containerregister med hjälp av Azure CLI](container-registry-get-started-azure-cli.md)
* [Automate container builds and maintenance with ACR Tasks](container-registry-tasks-overview.md)
