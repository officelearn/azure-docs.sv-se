---
title: "Vanliga frågor om Azure Container Service"
description: "Innehåller svar på några vanliga frågor om Azure Container Service."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/01/2018
ms.author: nepeters
ms.openlocfilehash: 2b78479c257930669729a7781b3893b3e2064bab
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>Vanliga frågor och svar om Azure-behållaren (AKS)

Den här artikeln adresser vanliga frågor om Azure-behållaren (AKS).

## <a name="which-azure-regions-will-have-azure-container-service-aks"></a>Vilka regioner som Azure kommer att ha Azure Container Service (AKS)? 

- Centrala Kanada 
- Östra Kanada 
- Centrala USA 
- Östra USA 
- Sydostasien 
- Västra Europa 
- Västra USA 2 

## <a name="when-will-additional-regions-be-added"></a>När läggs ytterligare regioner? 

Ytterligare regioner läggs till som ökar.

## <a name="are-security-updates-applied-to-aks-nodes"></a>Tillämpas säkerhetsuppdateringar AKS noder? 

OS-säkerhetskorrigeringar som tillämpas på noderna i klustret enligt ett schema som automatiskt varje natt, men en omstart inte utförs. Om det behövs kan noder startas via portalen eller Azure CLI. När du uppgraderar ett kluster, senaste Ubuntu avbildningen används och alla säkerhetskorrigeringar tillämpas (med en omstart).

## <a name="do-you-recommend-customers-use-acs-or-akss"></a>Rekommenderar du kunder Använd ACS eller AKSs? 

Med tanke på att Azure Container Service (AKS) kommer GA vid ett senare tillfälle, rekommenderar vi att du skapar Pocs utvecklings- och -kluster i AKS men produktions-kluster i ACS-Kubernetes.  

## <a name="when-will-acs-be-deprecated"></a>När kommer ACS inaktuell? 

ACS att bli inaktuell vid ungefär samma tidpunkt som AKS blir GA. Har du 12 månader efter det att migrera kluster till AKS. Du kan köra alla ACS-åtgärder under 12 månader.

## <a name="does-aks-support-node-autoscaling"></a>Stöder AKS nod autoskalning? 

Noden autoskalning stöds inte men på Översikt. Du kanske vill titta på den här öppen källkod [autoskalning implementering][auto-scaler].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Varför skapas två resursgrupper med AKS? 

Andra resursgruppen har skapats automatiskt för enkelt borttagningen av alla resurser som är associerade med en AKS distribution.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault är integrerad med AKS? 

Nej, det är inte men den här integreringen planeras. Under tiden kan du prova följande lösningar från [Hexadite][hexadite]. 

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  