---
title: Vad är Azure Dev Spaces?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Lär dig mer om hur Azure Dev Spaces ger en snabb, iterativ kubernetes-utvecklingsupplevelse för team i Azure Kubernetes Service-kluster
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 8b22181bcddda9e4156c0e0dbe61d7d813498d96
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529719"
---
# <a name="what-is-azure-dev-spaces"></a>Vad är Azure Dev Spaces?

Azure Dev Spaces ger en snabb, iterativ Kubernetes utvecklingsupplevelse för team i AKS-kluster (Azure Kubernetes Service). Azure Dev Spaces kan du också felsöka och testa alla komponenter i ditt program i AKS med minimal utveckling maskin setup, utan att replikera eller håna upp beroenden.

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Så förenklas Kubernetes-utveckling av Azure Dev Spaces

Azure Dev Spaces hjälper team att fokusera på utveckling och snabb iteration av sina mikrotjänstprogram genom att låta teamen arbeta direkt med hela sin mikrotjänstarkitektur eller program som körs i AKS. Azure Dev Spaces är också ett sätt att självständigt uppdatera delar av din mikrotjänstarkitektur isolerat utan att påverka resten av AKS-klustret eller andra utvecklare. Azure Dev Spaces är för utveckling och testning i utvecklings- och testmiljöer på lägre nivå och är inte avsett att köras på AKS-kluster för produktion.

Eftersom team kan arbeta med hela programmet och samarbeta direkt i AKS, Azure Dev Spaces:

* Minimerar installation av lokala datorer
* Minskar installationstiden för nya utvecklare i teamet
* Ökar ett teams hastighet genom snabbare iteration
* Minskar antalet redundanta utvecklings- och integrationsmiljöer eftersom gruppmedlemmar kan dela ett kluster
* Tar bort behovet av att replikera eller skapa beroenden
* Förbättrar samarbetet mellan utvecklingsteam och de team de arbetar med, till exempel DevOps-team

Azure Dev Spaces tillhandahåller verktyg för att generera Docker- och Kubernetes-tillgångar för dina projekt. Med det här verktyget kan du enkelt lägga till nya och befintliga program i både ett utvecklingsutrymme och andra AKS-kluster.

Mer information om hur Azure Dev Spaces fungerar finns i [Så här fungerar Azure Dev Spaces och är konfigurerat][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Regioner och konfigurationer som stöds

Azure Dev Spaces stöds endast av AKS-kluster i [vissa regioner][supported-regions]. Azure Dev Spaces stöder användning av [Azure CLI-](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Visual Studio-koden](https://code.visualstudio.com/download) med [Azure Dev Spaces-tillägget](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) installerat på Linux, macOS eller Windows 8 eller senare för att skapa och köra dina program på AKS. Den stöder också användning av [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) installerat på Windows 8 eller senare. För Visual Studio 2019 behöver du Azure Development-arbetsbelastningen. För Visual Studio 2017 behöver du arbetsbelastningen webbutveckling och [Visual Studio Tools för Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Nästa steg

Läs mer om snabb och iterativ utveckling för team med Azure Dev Spaces med snabbstart för [teamutveckling][team-development-quickstart].

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md
