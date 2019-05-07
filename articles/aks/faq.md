---
title: Vanliga frågor för Azure Kubernetes Service (AKS)
description: Innehåller svar på några vanliga frågor om Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: f365fcd61944fbae131ab79a1c3660aaf02fa8d7
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073937"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Vanliga frågor och svar om Azure Kubernetes Service (AKS)

Den här artikeln adresser oftare frågor om Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Vilka Azure-regioner tillhandahåller Azure Kubernetes Service (AKS) i dag?

En fullständig lista över tillgängliga regioner finns i [AKS regioner och tillgänglighet][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>AKS som har stöd för automatisk skalning nod?

Ja, automatisk skalning är tillgängligt via den [Kubernetes autoskalningen] [ auto-scaler] från och med Kubernetes 1.10. Läs mer om hur du manuellt konfigurerar och använder klustret autoskalningen [autoskalning av kluster i AKS][aks-cluster-autoscale].

Du kan också använda inbyggda kluster autoskalningen (för närvarande i förhandsversion i AKS) för att hantera skalning av noder. Mer information finns i [automatiskt skala ett kluster för att uppfylla krav på program i AKS][aks-cluster-autoscaler].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Stöder AKS Kubernetes rollbaserad åtkomstkontroll (RBAC)?

Ja, Kubernetes RBAC är aktiverad som standard när kluster skapas med Azure CLI. RBAC kan aktiveras för kluster som skapas med hjälp av Azure portal eller mallar.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Kan jag distribuera AKS till min befintliga virtuellt nätverk?

Ja, du kan distribuera ett AKS-kluster till ett befintligt virtuellt nätverk med hjälp av den [avancerade funktionen][aks-advanced-networking].

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Kan jag begränsa Kubernetes API-servern för att endast nås i mitt virtuella nätverk?

Inte just nu. Kubernetes API-servern visas som en offentlig fullständigt kvalificerade domännamnet (FQDN). Du kan kontrollera åtkomsten till ditt kluster med hjälp av [Kubernetes rollbaserad åtkomst till kontroll (RBAC) och Azure Active Directory (AAD)][aks-rbac-aad]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Är säkerhetsuppdateringar som tillämpas på AKS agentnoder?

Azure tillämpar automatiskt säkerhetsuppdateringar på Linux-noder i klustret enligt ett schema som varje natt. Men är du ansvarig för att säkerställa att dessa Linux noder startas om som krävs. Har du flera alternativ för att utföra omstarter av noden:

- Manuellt via Azure portal eller Azure CLI.
- Genom att uppgradera AKS-klustret. Uppgradering av kluster automatiskt [här och tömmer noderna][cordon-drain], ansluta varje nod säkerhetskopiera med den senaste Ubuntu-avbildningen och en ny Uppdateringsversion eller en mindre Kubernetes-version. Mer information finns i [uppgradera ett AKS-kluster][aks-upgrade].
- Med hjälp av [Kured](https://github.com/weaveworks/kured), en omstart för öppen källkod-daemon för Kubernetes. Kured körs som en [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) och övervakar varje nod för förekomsten av en fil som anger att en omstart krävs. OS-omstarter hanteras i klustret med samma [här och tömma processen] [ cordon-drain] som en uppgradering av klustret.

Läs mer om hur du använder kured [tillämpa säkerhets- och kernel-uppdateringar på noderna i AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Windows Server-noder

Windows Server-noder (för närvarande i förhandsversion i AKS), Windows Update inte automatiskt köra och gäller för de senaste uppdateringarna. Med jämna mellanrum kring Windows-versionen uppdateringscykeln och egna verifieringsprocessen, bör du utföra en uppgradering på Windows Server-nod lagringspoolerna AKS-klustret. Den här uppgraderingsprocessen skapar noderna som kör den senaste Windows Server-avbildning och de uppdateringar och sedan tar bort äldre noderna. Mer information om den här processen finns i [uppgradera en nodpool i AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Varför skapas två resursgrupper med AKS?

Varje AKS-distributionen omfattar två resursgrupper:

- Första resursgrupp skapas av dig och innehåller endast tjänstresurs Kubernetes. AKS-resursprovidern skapar automatiskt den andra mallen under distributionen, till exempel *MC_myResourceGroup_myAKSCluster_eastus*. Information om hur du kan ange namnet på den här andra resursgruppen finns i nästa avsnitt.
- Den här andra resursgrupp, till exempel *MC_myResourceGroup_myAKSCluster_eastus*, innehåller alla de infrastrukturresurser som är kopplat till klustret. Dessa resurser inkluderar den Kubernetes noden virtuella datorer, virtuella nätverk och lagring. Den här separat resursgrupp skapas för att förenkla rensning av resurser.

Om du skapar resurser som ska användas med AKS-klustret, till exempel lagringskonton eller reserverade offentliga IP-adresser kan du placera dem i resursgruppen skapas automatiskt.

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>Kan jag ge mitt eget namn för resursgruppen för AKS infrastruktur?

Ja. Som standard resursprovidern AKS automatiskt skapas en sekundär resursgrupp under distributionen, till exempel *MC_myResourceGroup_myAKSCluster_eastus*. För att uppfylla företagets policy kan du ange ditt eget namn för den här hanterade kluster (*MC_*) resursgrupp.

Om du vill ange egna resursgruppens namn, installera den [förhandsversionen av aks] [ aks-preview-cli] versionen av Azure CLI-tillägget *0.3.2* eller senare. När du skapar ett AKS-kluster med den [az aks skapa] [ az-aks-create] kommandot, använda den *--noden resursgrupp* parametern och ange ett namn för resursgruppen. Om du [använder en Azure Resource Manager-mall] [ aks-rm-template] för att distribuera ett AKS-kluster, kan du definiera resource group namn med den *nodeResourceGroup* egenskapen.

* Den här resursgruppen skapas automatiskt av Azure-resursprovidern i din egen prenumeration.
* Du kan bara ange en anpassad resursgruppens namn när klustret har skapats.

Följande scenarier stöds inte:

* Du kan inte ange en befintlig resursgrupp för *MC_* grupp.
* Du kan inte ange en annan prenumeration för den *MC_* resursgrupp.
* Du kan inte ändra den *MC_* resursgruppens namn när klustret har skapats.
* Du kan inte ange namn för de hantera resurserna inom den *MC_* resursgrupp.
* Du kan inte ändra eller ta bort taggar hanterade resurser inom den *MC_* resursgrupp (se ytterligare information i nästa avsnitt).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Kan jag ändra taggar och andra egenskaper för AKS-resurser i resursgruppen MC_ *?

Ändra och ta bort de Azure-skapade taggarna och andra egenskaper för resurser i den *MC_** resursgrupp kan leda till oväntade resultat, till exempel skalning och uppgradera fel. Det går att skapa och ändra ytterligare anpassade taggar, till exempel tilldela en business unit eller kostnaden center. Ändra resurser under den *MC_** i AKS kluster delar servicenivåmål (SLO). Mer information finns i [gör AKS erbjuder ett servicenivåavtal?](#does-aks-offer-a-service-level-agreement)

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

Det går för närvarande inte att ändra listan över åtkomst domänkontrollanter i AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault är integrerad med AKS?

AKS är inte för närvarande internt integrerad med Azure Key Vault. Men den [Azure Key Vault FlexVolume för Kubernetes-projektet] [ keyvault-flexvolume] aktiverar direkta integrering från Kubernetes-poddar till KeyVault-hemligheter.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kan jag köra Windows Server-behållare i AKS?

Ja, Windows Server-behållare finns i förhandsversion. Om du vill köra Windows Server-behållare i AKS kan du skapa en pool för noden som kör Windows Server som gäst-OS. Windows Server-behållare kan bara använda Windows Server 2019. Du kommer igång [skapa ett AKS-kluster med en Windows Server-nodpool][aks-windows-cli].

Fönstret serverstöd noden poolen innehåller vissa begränsningar som ingår i den överordnade Windows-servern i Kubernetes-projektet. Mer information om dessa begränsningar finns i [Windows Server-behållare i AKS begränsningar][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Erbjuder ett servicenivåavtal i AKS?

I ett servicenivåavtal (SLA) samtycker providern till att ersätta kunden för kostnaden för tjänsten om den publicerade servicenivån inte uppfylls. Eftersom AKS själva är kostnadsfria, är utan kostnad som är tillgängliga för att betala tillbaka och därför inget formella serviceavtal. Dock AKS strävar efter att upprätthålla tillgänglighet på minst 99,5% för Kubernetes API-servern.

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
[aks-rm-template]: /rest/api/aks/managedclusters/createorupdate#managedcluster
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
