---
title: Så här använder du kubectl med Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Lär dig hur du använder kubectl-kommandon i ett utvecklingsutrymme i ett Azure Kubernetes Service-kluster med Azure Dev Spaces aktiverat
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438371"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Använda kubectl med ett Azure Dev Space

Du kan komma åt Kubernetes-klustret i ett Azure Dev `kubectl`Space och använda befintliga Kubernetes-verktyg som .

Kommandot `az aks use-dev-spaces` Köra lägger `kubectl` automatiskt till en konfigurationskontext åt dig, så kubectl bör redan vara ansluten till ditt Azure Dev Spaces Kubernetes-kluster. Exempel:
- Bekräfta den aktuella kontexten:`kubectl config current-context`
- Lista alla tillgängliga `kubectl config get-contexts`sammanhang: . 
- Ändra sammanhang:`kubectl config use-context <context-name>`
- Visa kubernetes-instrumentpanelen: kör `kubectl proxy`och öppna sedan webbläsaren till den `/ui` adress som det här kommandot avger (lägg till i URL:en för att navigera till Kubernetes instrumentpanelen).
- Lista de tjänster som körs i standardutrymmet för Azure Dev Spaces med namnet *standard:*`kubectl get services --namespace=default`

