---
title: Introduktion till Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Lär dig hur Azure dev Spaces erbjuder en snabb, iterativ Kubernetes utvecklings upplevelse för team i Azure Kubernetes service-kluster
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, kubectl, K8s
manager: gwallace
ms.openlocfilehash: 586b19070ec36517add21f7aac86ddf15121be2d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771197"
---
# <a name="introduction-to-azure-dev-spaces"></a>Introduktion till Azure dev Spaces

Med Azure dev Spaces får du en snabb, iterativ Kubernetes utvecklings upplevelse för team i Azure Kubernetes service (AKS)-kluster. Med Azure dev Spaces kan du också felsöka och testa alla komponenter i ditt program i AKS med minimal installation av utvecklings datorer, utan att replikera eller modellera beroenden.

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Så förenklas Kubernetes-utveckling av Azure Dev Spaces

Med Azure dev Spaces kan grupper fokusera på utveckling och snabb iteration av deras mikrotjänstprogram genom att låta grupper arbeta direkt med sin hela mikrotjänsters arkitektur eller program som körs i AKS. Azure dev Spaces är också ett sätt att uppdatera delar av mikrotjänsternas arkitektur i isolering utan att påverka resten av AKS-klustret eller andra utvecklare. Azure dev Spaces är för utveckling och testning i utvecklings-och testnings miljöer på lägre nivå och är inte avsedd att köras på produktions AKS-kluster.

Eftersom team kan arbeta med hela programmet och samar beta direkt i AKS, Azure dev Spaces:

* Minimerar installationen av den lokala datorn
* Minskar installations tiden för nya utvecklare i teamet
* Ökar en Teams hastighet genom snabbare upprepning
* Minskar antalet redundanta miljöer för utveckling och integrering eftersom team medlemmar kan dela ett kluster
* Tar bort behovet av att replikera eller modellera beroenden
* Förbättrar samarbetet mellan utvecklings grupper och de team som de arbetar med, till exempel DevOps Teams

Azure dev Spaces innehåller verktyg för att skapa Docker-och Kubernetes-tillgångar för dina projekt. Med det här verktyget kan du enkelt lägga till nya och befintliga program i både ett dev-utrymme och andra AKS-kluster.

Mer information om hur Azure dev Spaces fungerar finns i [så här fungerar Azure dev Spaces och har kon figurer ATS][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Regioner och konfigurationer som stöds

Azure dev Spaces stöds endast av AKS-kluster i [vissa regioner][supported-regions]. Azure Dev Spaces stöder användning av [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Visual Studio Code](https://code.visualstudio.com/download) med [Azure Dev Spaces-tillägget](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) som är installerat på Linux, MacOS och Windows 8 eller senare för att skapa och köra program på AKS. Det stöder också användning av [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) som är installerat på Windows 8 eller senare. För Visual Studio 2019 behöver du arbets belastningen Azure Development. För Visual Studio 2017 behöver du arbets belastningen webb utveckling och [Visual Studio Tools för Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om snabb och iterativ utveckling för team med Azure dev Spaces med snabb start för team utveckling.

> [!div class="nextstepaction"]
> [Snabb start för team utveckling](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md
