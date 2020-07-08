---
title: Använda kubectl med Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Lär dig hur du använder kubectl-kommandon inom ett dev-utrymme på ett Azure Kubernetes service-kluster med Azure dev-utrymmen aktiverade
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75438371"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Använda kubectl med ett Azure dev-utrymme

Du kan komma åt Kubernetes-klustret i ett Azure dev-utrymme och använda befintliga Kubernetes-verktyg som `kubectl` .

`az aks use-dev-spaces`Om du kör kommandot läggs automatiskt en `kubectl` konfigurations kontext åt dig, så kubectl bör redan vara ansluten till ditt Azure dev Spaces Kubernetes-kluster. Exempel:
- Bekräfta den aktuella kontexten:`kubectl config current-context`
- Lista alla tillgängliga kontexter: `kubectl config get-contexts` . 
- Ändrings kontext:`kubectl config use-context <context-name>`
- Visa Kubernetes-instrumentpanelen: kör `kubectl proxy` och öppna sedan webbläsaren till den adress som kommandot avger (Lägg till i `/ui` URL: en för att navigera till Kubernetes-instrumentpanelen).
- Visa en lista med de tjänster som körs i standard utrymmet för Azure dev Spaces som *standard*:`kubectl get services --namespace=default`

