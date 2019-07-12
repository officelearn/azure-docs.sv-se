---
title: Vanliga frågor för Azure Kubernetes Service (AKS)
description: Hitta svar på några vanliga frågor om Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 495f182ed450d0fac69b31ea2996bacc60863fea
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672768"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Vanliga frågor och svar om Azure Kubernetes Service (AKS)

Den här artikeln adresser oftare frågor om Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Vilka Azure-regioner för närvarande erbjuder AKS?

En fullständig lista över tillgängliga regioner finns i [AKS regioner och tillgänglighet][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>AKS som har stöd för automatisk skalning nod?

Ja, möjlighet att automatiskt skala horisontellt agentnoder i AKS är tillgängligt i förhandsversionen. Se [automatiskt skala ett kluster för att uppfylla krav på program i AKS][aks-cluster-autoscaler] for instructions. AKS autoscaling is based on the [Kubernetes autoscaler][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Kan jag distribuera AKS till min befintliga virtuellt nätverk?

Ja, du kan distribuera ett AKS-kluster till ett befintligt virtuellt nätverk med hjälp av den [avancerade funktionen][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kan jag begränsa vem som har åtkomst till Kubernetes API-servern?

Ja, du kan begränsa åtkomsten till Kubernetes API med [API-servern behörighet IP-intervall][api-server-authorized-ip-ranges], vilket är för närvarande i förhandsversion.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Kan jag göra Kubernetes API-servern tillgänglig endast inom mitt virtuella nätverk?

Inte just nu, men detta är planerat. Du kan följa förloppet på den [AKS GitHub-lagringsplatsen][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Kan jag ha olika storlekar på Virtuella datorer i ett kluster?

Ja, du kan använda olika virtuella datorstorlekar i AKS-klustret genom att skapa [flera nodpooler][multi-node-pools], vilket är för närvarande i förhandsversion.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Är säkerhetsuppdateringar som tillämpas på AKS agentnoder?

Azure tillämpar automatiskt säkerhetsuppdateringar på Linux-noder i klustret enligt ett schema som varje natt. Men är du ansvarig för att säkerställa att dessa Linux noder startas om som krävs. Har du flera alternativ för att starta om noderna:

- Manuellt via Azure portal eller Azure CLI.
- Genom att uppgradera AKS-klustret. Klusteruppgradering [här och tömmer noderna][cordon-drain] automatically and then bring a new node online with the latest Ubuntu image and a new patch version or a minor Kubernetes version. For more information, see [Upgrade an AKS cluster][aks-upgrade].
- Med hjälp av [Kured](https://github.com/weaveworks/kured), en omstart för öppen källkod-daemon för Kubernetes. Kured körs som en [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) och övervakar varje nod för förekomsten av en fil som anger att en omstart krävs. I klustret, OS omstarter som hanteras av samma [här och tömma processen][cordon-drain] som en uppgradering av klustret.

Läs mer om hur du använder kured [tillämpa säkerhets- och kernel-uppdateringar på noderna i AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Windows Server-noder

Windows Server-noder (för närvarande i förhandsversion i AKS), Windows Update inte automatiskt köra och gäller för de senaste uppdateringarna. Med jämna mellanrum kring Windows-versionen uppdateringscykeln och egna verifieringsprocessen, bör du utföra en uppgradering på Windows Server-nod lagringspoolerna AKS-klustret. Den här uppgraderingsprocessen skapar noderna som kör den senaste Windows Server-avbildning och de uppdateringar och sedan tar bort äldre noderna. Mer information om den här processen finns i [uppgradera en nodpool i AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Varför skapas två resursgrupper med AKS?

Varje AKS-distributionen omfattar två resursgrupper:

1. Du skapar den första resursgruppen. Den här gruppen innehåller endast Kubernetes-tjänstresursen. AKS-resursprovidern skapar automatiskt den andra resursgruppen under distributionen. Ett exempel på andra resursgruppen är *MC_myResourceGroup_myAKSCluster_eastus*. Information om hur du anger namnet på den här andra resursgruppen finns i nästa avsnitt.
1. Andra resursgruppen, kallas de *noden resursgrupp*, innehåller alla de infrastrukturresurser som är kopplat till klustret. Dessa resurser inkluderar den Kubernetes noden virtuella datorer, virtuella nätverk och lagring. Som standard resursgruppen noden har ett namn som liknar *MC_myResourceGroup_myAKSCluster_eastus*. AKS tar automatiskt bort noden resursen när klustret tas bort, så den bör endast användas för resurser som delar klustrets livscykel.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Kan jag ge mitt eget namn för resursgruppen för AKS-nod?

Ja. Som standard AKS kommer noden ge resursgruppen namnet *MC_clustername_resourcegroupname_location*, men du kan också tillhandahålla ditt eget namn.

Om du vill ange egna resursgruppens namn, installera den [förhandsversionen av aks][aks-preview-cli] versionen av Azure CLI-tillägget *0.3.2* eller senare. När du skapar ett AKS-kluster med hjälp av den [az aks skapa][az-aks-create] kommandot, använda den *--noden resursgrupp* parametern och ange ett namn för resursgruppen. Om du [använder en Azure Resource Manager-mall][aks-rm-template] för att distribuera ett AKS-kluster, kan du definiera resursgruppens namn med hjälp av den *nodeResourceGroup* egenskapen.

* Sekundär resursgruppen skapas automatiskt av Azure-resursprovidern i din egen prenumeration.
* Du kan ange en anpassad resursgruppens namn endast när du skapar klustret.

Tänk på att du kan inte när du arbetar med resursgruppen nod:

* Ange en befintlig resursgrupp för resursgruppen för noden.
* Ange en annan prenumeration på noden för resursgruppen.
* Ändra noden resursgruppens namn när klustret har skapats.
* Ange namn för de hantera resurserna i resursgruppen noden.
* Ändra eller ta bort taggar hanterade resurser i resursgruppen noden. (Se ytterligare information i nästa avsnitt.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Kan jag ändra taggar och andra egenskaper för AKS-resurser i resursgruppen nod?

Om du ändrar eller tar bort Azure-skapade taggar och andra egenskaper för resursen i noden resursgruppen, kan du få oväntade resultat, till exempel skalning och uppgradera fel. AKS kan du skapa och ändra anpassade taggar. Du kanske vill skapa eller ändra anpassade taggar, till exempel, för att tilldela en business unit eller kostnaden center. Genom att ändra resurser under den nod resursgruppen i AKS-kluster kan dela du mål för servicenivå (SLO). Mer information finns i [gör AKS erbjuder ett serviceavtal?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Vilka Kubernetes åtkomst domänkontrollanter stöder AKS? Kan åtkomst domänkontrollanter läggs till eller tas bort?

AKS har stöd för följande [åtkomst domänkontrollanter][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *Tjänstkonto*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

För närvarande kan ändra du inte listan över åtkomst domänkontrollanter i AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault är integrerad med AKS?

AKS inte är för närvarande internt integrerat med Azure Key Vault. Men den [Azure Key Vault FlexVolume för Kubernetes-projektet][keyvault-flexvolume] aktiverar direkta integrering från Kubernetes-poddar till Key Vault-hemligheter.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kan jag köra Windows Server-behållare i AKS?

Ja, Windows Server-behållare finns i förhandsversion. Om du vill köra Windows Server-behållare i AKS kan du skapa en pool för noden som kör Windows Server som gäst-OS. Windows Server-behållare kan använda endast Windows Server-2019. Kom igång genom att se [skapa ett AKS-kluster med en Windows Server-nodpool][aks-windows-cli].

Windows Server-stöd för nodpoolen innehåller vissa begränsningar som ingår i den överordnade Windows-servern i Kubernetes-projektet. Mer information om dessa begränsningar finns i [Windows Server-behållare i AKS begränsningar][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Erbjuder ett serviceavtal i AKS?

I ett servicenivåavtal (SLA) samtycker providern till att ersätta kunden för kostnaden för tjänsten om den publicerade servicenivån inte uppfylls. Eftersom AKS är kostnadsfritt, är utan kostnad tillgängligt för betala tillbaka, så AKS har inget formella serviceavtal. Dock AKS strävar efter att upprätthålla tillgänglighet på minst 99,5 procent för Kubernetes API-servern.

## <a name="why-cant-i-set-maxpods-below-30"></a>Varför kan jag ställa maxPods under 30?

I AKS kan du ange den `maxPods` värdet när du skapar klustret med hjälp av Azure CLI och Azure Resource Manager-mallar. Dock både Kubenet och Azure CNI kräver en *minimivärdet* (verifieras vid skapandet):

| Nätverk | Minimum | Maximal |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Eftersom AKS är en hanterad tjänst kan vi distribuera och hantera tilläggsprogram och poddar som en del av klustret. Tidigare användare definiera en `maxPods` värde som är lägre än värdet som hanterade poddar som krävs för att köra (till exempel, 30). AKS beräknar nu det minsta antalet poddar med hjälp av den här formeln: ((maxPods eller (maxPods * vm_count)) > hanterade tillägg poddar minimum.

Användare kan inte åsidosätta minst `maxPods` verifiering.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Kan jag använda Azure reservation rabatter min AKS-agentnoder?

AKS-agentnoder faktureras som standard Azure-datorer, så om du har köpt [Azure reservationer][reservation-discounts] för VM-storleken som används i AKS, används automatiskt de gör.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948