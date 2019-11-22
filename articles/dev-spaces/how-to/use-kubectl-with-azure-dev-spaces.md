---
title: Använda kubectl med Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
ms.openlocfilehash: fea14668c35eddcd01417b417e2239788eddd6d1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279930"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Använda kubectl med ett Azure dev-utrymme

Du kan komma åt Kubernetes-klustret i ett Azure dev-utrymme och använda befintliga Kubernetes-verktyg som `kubectl`.

Om du kör `az aks use-dev-spaces` kommandot läggs automatiskt en `kubectl` konfigurations kontext åt dig, så kubectl bör redan vara ansluten till ditt Azure dev Spaces Kubernetes-kluster. Exempel:
- Bekräfta den aktuella kontexten: `kubectl config current-context`
- Visa en lista med alla tillgängliga kontexter: `kubectl config get-contexts`. 
- Ändrings kontext: `kubectl config use-context <context-name>`
- Visa Kubernetes-instrumentpanelen: kör `kubectl proxy`och öppna sedan webbläsaren till den adress som kommandot avger (Lägg till `/ui` till URL: en för att navigera till Kubernetes-instrumentpanelen).
- Visa en lista med de tjänster som körs i standard utrymmet för Azure dev Spaces med namnet *standard*: `kubectl get services --namespace=default`

