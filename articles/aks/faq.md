---
title: Vanliga frågor om Azure Kubernetes Service
description: Innehåller svar på några vanliga frågor om Azure Kubernetes Service.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/11/2018
ms.author: iainfou
ms.openlocfilehash: 915f74df69596b1677a0e03770e076ae50efc609
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001253"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Vanliga frågor och svar om Azure Kubernetes Service (AKS)

Den här artikeln adresser oftare frågor om Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Vilka Azure-regioner tillhandahåller Azure Kubernetes Service (AKS) i dag?

Se Azure Kubernetes Service [regioner och tillgänglighet] [ aks-regions] dokumentationen för en fullständig lista.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Är säkerhetsuppdateringar som tillämpas på AKS agentnoder?

Azure tillämpar automatiskt säkerhetsuppdateringar på noderna i klustret enligt ett schema som varje natt. Men du är ansvarig för att säkerställa att noderna startas om efter behov. Har du flera alternativ för att utföra omstarter av noden:

- Manuellt via Azure portal eller Azure CLI.
- Genom att uppgradera AKS-klustret. Uppgradering av kluster automatiskt [här och tömmer noderna](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), anpassa dem säkerhetskopiera med den senaste Ubuntu-avbildningen. Uppdatera operativsystemavbildning på noderna utan att ändra Kubernetes-versioner genom att ange den aktuella versionen för klustret i `az aks upgrade`.
- Med hjälp av [Kured](https://github.com/weaveworks/kured), en omstart för öppen källkod-daemon för Kubernetes. Kured körs som en [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) och övervakar varje nod för förekomsten av en fil som anger att en omstart krävs. Den arrangerar sedan omstarter i klustret, följa samma cordon och drain processen som beskrivs tidigare.

## <a name="does-aks-support-node-autoscaling"></a>AKS som har stöd för automatisk skalning nod?

Ja, automatisk skalning är tillgängligt via den [Kubernetes autoskalningen] [ auto-scaler] från och med Kubernetes 1.10.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Stöder AKS Kubernetes rollbaserad åtkomstkontroll (RBAC)?

Ja, RBAC kan aktiveras när du distribuerar ett AKS-kluster från Azure CLI eller Azure Resource Manager-mall. Den här funktionen kommer snart att Azure-portalen.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>Vilka Kubernetes åtkomst domänkontrollanter stöder AKS? Detta konfigureras?

AKS har stöd för följande [åtkomst domänkontrollanter][admission-controllers]:

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

Det går för närvarande inte att ändra listan över åtkomst domänkontrollanter i AKS.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Kan jag distribuera AKS till min befintliga virtuellt nätverk?

Ja, du kan distribuera ett AKS-kluster till ett befintligt virtuellt nätverk med hjälp av den [avancerade funktionen](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault är integrerad med AKS?

AKS är inte internt integrerat med Azure Key Vault just nu. Det finns dock community-lösningar som [acs-keyvault-agenten från Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kan jag köra Windows Server-behållare i AKS?

Du måste köra Windows Server-baserade noder för att köra Windows Server-behållare. Windows Server-baserade noder är inte tillgängliga i AKS just nu. Du kan dock använda Virtual Kubelet för att schemalägga Windows-behållare i Azure Container Instances och hantera dem som en del av AKS-klustret. Mer information finns i [använda Virtual Kubelet med AKS][virtual-kubelet].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Varför skapas två resursgrupper med AKS?

Varje AKS-distributionen omfattar två resursgrupper. Först har skapats av dig och innehåller endast Kubernetes service-resurs. AKS-resursprovidern skapar automatiskt den andra mallen under distributionen med ett namn som liknar *MC_myResourceGroup_myAKSCluster_eastus*. Andra resursgruppen innehåller alla de infrastrukturresurser som är kopplat till klustret, till exempel virtuella datorer, nätverk och lagring. Den har skapats för att förenkla rensning av resurser.

Om du skapar resurser som ska användas med AKS-klustret, till exempel lagringskonton eller reserverade offentliga IP-adress, bör du placera dem i resursgruppen skapas automatiskt.

## <a name="does-aks-offer-a-service-level-agreement"></a>Erbjuder ett servicenivåavtal i AKS?

I ett servicenivåavtal (SLA) samtycker providern till att ersätta kunden för kostnaden för tjänsten publicerade servicenivån inte vara uppfyllda. Eftersom AKS själva är kostnadsfria, är utan kostnad som är tillgängliga för att betala tillbaka och därför inget formella serviceavtal. Men begära vi att bibehålla tillgänglighet på minst 99,5% för Kubernetes API-servern.

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md
[virtual-kubelet]: virtual-kubelet.md

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
