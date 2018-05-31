---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Snabb Kubernetes-utveckling med behållare och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198766"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Med Azure Dev Spaces får du hjälp att utveckla snabbt på Kubernetes. Dessutom kan du med Azure Dev Spaces lägga till fullständiga utvecklingsfunktioner såsom felsökning till Azure Kubernetes-behållare. Det går även att iterativt utveckla behållare i molnet med hjälp av välbekanta verktyg som VS Code, Visual Studio och kommandoraden. Azure Dev Spaces är särskilt användbart för teamutveckling där isolering av enskilda kodgrenar i egna utrymmen är en viktig del i utvecklingslivscykeln.

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Så förenklas Kubernetes-utveckling av Azure Dev Spaces 

Det finns flera fördelar med den här metoden:

* Få en infrastrukturlös utvecklingsmiljö som är representativ för produktionen, med fullständig åtkomst till molnresurser.
* Felsök Node.js och .NET Core-behållare direkt i Kubernetes med VS Code eller Visual Studio. Alla andra språk kan användas för utveckling med kommandoradsgränssnittet.
* Dela en Kubernetes-instans med utvecklingsteamet för att spara kostnader och minimera konfiguration av lokala datorer för nya teammedlemmar.
* Utveckla koden isolerat och utför testning från slutpunkt till slutpunkt med andra komponenter utan replikerings- eller simuleringsberoenden.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)