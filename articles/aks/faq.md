---
title: Vanliga frågor om Azure Kubernetes Service
description: Innehåller svar på några vanliga frågor om Azure Kubernetes Service.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/25/2018
ms.author: nepeters
ms.openlocfilehash: 5155d0c85e5b3698b0a13d2d5256a235858f0e82
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938514"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Vanliga frågor och svar om Azure-Kubernetes (AKS)

Den här artikeln adresser vanliga frågor om Azure-Kubernetes (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Vilka regioner som Azure tillhandahåller Azure Kubernetes Service (AKS) idag?

- Östra Australien
- Centrala Kanada
- Östra Kanada
- Centrala USA
- Östra USA
- Östra US2
- Norra Europa
- Storbritannien, södra
- Västra Europa
- Västra USA
- Västra USA 2

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Tillämpas säkerhetsuppdateringar AKS agent noder?

Azure tillämpas automatiskt säkerhetskorrigeringar på noderna i klustret enligt ett schema som automatiskt varje natt. Men du är ansvarig för att säkerställa att noderna startas om efter behov. Du har flera alternativ för att utföra omstarter av noden:

- Manuellt via Azure-portalen eller Azure CLI.
- Genom att uppgradera AKS klustret. Klustret uppgraderingar automatiskt [cordon och tömmer noder](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), anpassa dem igen med senaste Ubuntu avbildningen. Uppdatera OS-avbildningen på noderna utan att ändra Kubernetes versioner genom att ange den aktuella versionen för klustret i `az aks upgrade`.
- Med hjälp av [Kured](https://github.com/weaveworks/kured), en öppen källkod omstart daemon för Kubernetes. Kured körs som en [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) och övervakar varje nod för förekomsten av en fil som anger att en omstart krävs. Den samordnar sedan startas om i klustret, följa samma cordon och tömning process som beskrivs ovan.

## <a name="does-aks-support-node-autoscaling"></a>Stöder AKS nod autoskalning?

Ja, autoskalning är tillgänglig via den [Kubernetes autoscaler] [ auto-scaler] från och med Kubernetes 1.10.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Stöder AKS Kubernetes rollbaserad åtkomstkontroll (RBAC)?

Ja, RBAC kan aktiveras när du distribuerar ett AKS kluster från Azure CLI eller Azure Resource Manager-mall. Den här funktionen kommer snart att Azure-portalen.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>Vilka Kubernetes åtkomstkontroll domänkontrollanter stöder AKS? Detta konfigureras?

AKS har stöd för följande [åtkomstkontroll domänkontrollanter][admission-controllers]:

* NamespaceLifecycle
* LimitRanger
* Tjänstkonto
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook 
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

Det går för närvarande inte att ändra listan över åtkomstkontroll domänkontrollanter i AKS.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Kan jag distribuera AKS i Mina befintliga virtuella nätverk?

Ja, du kan distribuera ett AKS kluster till ett befintligt virtuellt nätverk med hjälp av den [funktionen för avancerade](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault är integrerad med AKS?

AKS integrerat inte internt med Azure Key Vault just nu. Det finns dock community lösningar som [acs-keyvault-agenten från Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kan jag köra Windows Server-behållare på AKS?

Om du vill köra Windows Server-behållare som du behöver köra Windows Server-baserade noder. Windows Server-baserade noder är inte tillgängliga i AKS just nu. Om du behöver köra Windows Server-behållare på Kubernetes i Azure finns i [dokumentationen för acs-motorn](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Varför skapas två resursgrupper med AKS?

Varje AKS distribution omfattar två resursgrupper. Först skapas av dig och innehåller endast tjänstresurs Kubernetes. Resursprovidern AKS skapar automatiskt det andra under distributionen med namnet *MC_myResourceGroup_myAKSCluster_eastus*. Andra resursgruppen som innehåller alla infrastrukturresurser som är associerade med klustret, till exempel virtuella datorer, nätverk och lagring. Den har skapats för att förenkla rensning av resursen.

Om du skapar resurser som ska användas med AKS klustret, till exempel storage-konton eller reserverade offentliga IP-adressen bör du placera dem i den automatiskt genererade resursgruppen.

## <a name="does-aks-offer-a-service-level-agreement"></a>Erbjuder AKS ett servicenivåavtal?

I ett servicenivåavtal (SLA) samtycker providern till att ersätta kunden för kostnaden för tjänsten inte ska publicerade servicenivån uppfyllas. Eftersom AKS själva är ledigt, är det utan kostnad som är tillgängliga att ersätta och därför inga formella SLA. Men försöka vi Underhåll tillgängligheten för minst 99,5% för Kubernetes API-servern.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
