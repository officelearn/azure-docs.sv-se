---
title: Hur du använder kubectl med Azure Dev blanksteg | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Snabb utveckling av Kubernetes med behållare och mikrotjänster på Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
manager: douge
ms.openlocfilehash: 38a433a14ab977fb56a8331a057d27241f1d9783
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Använda kubectl med ett utrymme för Azure-utveckling

Du kan komma åt Kubernetes klustret i ett Azure Dev utrymme och Använd befintlig Kubernetes verktyg som `kubectl`.

Kör `azds resource create` eller `azds resource select` automatiskt lägger till en `kubectl` configuration kontext för dig, så kubectl redan är ansluten till din Azure Dev blanksteg Kubernetes-klustret. Exempel:
- Kontrollera den aktuella kontexten: `kubectl config current-context`
- Visa en lista över alla tillgängliga kontexter: `kubectl config get-contexts`. 
- Ändra kontexten: `kubectl config use-context <context-name>`
- Visa instrumentpanelen för Kubernetes: kör `kubectl proxy`, öppna webbläsaren till den adress som det här kommandot genererar (Lägg till `/ui` till URL-Adressen att navigera till instrumentpanelen Kubernetes).
- Listan för att köra tjänster i standard-Azure Dev blanksteg-utrymme med namnet *standard*: `kubectl get services --namespace=default`

