---
title: Introduktion till Azure Dev blanksteg
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 05/07/2019
ms.topic: overview
description: Introduktion till Azure Dev blanksteg
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 33ac5a7aa6d823105b87325ba52aa77cd9b9b3a3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706307"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces

Azure Dev blanksteg är en snabb, repetitiv Kubernetes utvecklingsupplevelse för team i Azure Kubernetes Service (AKS)-kluster. Du kan samarbeta med ditt team i ett delat AKS-kluster. Azure Dev blanksteg kan du testa alla komponenter i ditt program i AKS utan att replikera eller simulerade upp beroenden. Du kan iterativt köra och felsöka behållare direkt i AKS med minimal utveckling datorn installationen.

![](media/azure-dev-spaces/collaborate-graphic.gif)


## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Så förenklas Kubernetes-utveckling av Azure Dev Spaces

Azure Dev blanksteg hjälper olika team kan fokusera på utveckling och snabb iteration av mikrotjänst programmet genom att låta folk att arbeta direkt med sina hela mikrotjänstarkitektur eller program som körs i AKS. Azure Dev blanksteg innehåller också ett sätt att uppdatera delar av din arkitektur för mikrotjänster avskilt oberoende av varandra utan att påverka resten av AKS-klustret eller andra utvecklare. Azure Dev blanksteg är för utveckling och testning i på lägre nivå utveckling och testning miljöer och är inte avsedd som körs i produktion AKS-kluster.

Eftersom team kan arbeta med hela programmet och samarbeta direkt i AKS, Azure Dev blanksteg:

* Minimerar installationsprogrammet för lokal dator
* Minskar konfigurera tid för nya utvecklare i teamet
* Ökar ett team hastighet via snabbare iteration
* Minskar antalet redundant miljöer för utveckling och integration eftersom teammedlemmar kan dela ett kluster
* Eliminerar behovet av att replikera eller skapa simulerade beroenden
* Ger bättre samarbete mellan utvecklingsteam samt de team som de arbetar med, till exempel DevOps-team

Azure Dev blanksteg innehåller verktyg för att generera Docker och Kubernetes tillgångar för dina projekt. Verktygen kan du enkelt lägga till nya och befintliga program till både ett utrymme för utveckling och andra AKS-kluster.

Mer information om hur Azure Dev blanksteg fungerar finns i [hur Azure Dev blanksteg fungerar och är konfigurerad][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Regioner som stöds och konfigurationer

Azure Dev Spaces stöds endast av AKS-kluster i regionerna **USA, östra**, **USA, östra 2**, **USA, centrala**, **USA, västra 2**, **Europa, norra**, **Europa, västra**, **Storbritannien, södra**, **Asien, sydöstra**, **Australien, östra**, **Kanada, centrala** och **Kanada, östra**. Azure Dev Spaces stöder användning av [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Visual Studio Code](https://code.visualstudio.com/download) med [Azure Dev Spaces-tillägget](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) som är installerat på Linux, MacOS och Windows 8 eller senare för att skapa och köra program på AKS. Det har också stöd för [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) installeras på Windows 8 eller senare. Du behöver arbetsbelastningen Azure Development för Visual Studio-2019. För Visual Studio 2017, behöver du arbetsbelastningen webbutveckling och [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Nästa steg

Läs mer om snabb, iterativ utveckling för team med Azure Dev blanksteg med Snabbstart för team-utveckling.

> [!div class="nextstepaction"]
> [Snabbstart för team-utveckling](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md
