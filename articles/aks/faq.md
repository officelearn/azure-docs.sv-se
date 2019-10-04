---
title: Vanliga frågor och svar om Azure Kubernetes service (AKS)
description: Hitta svar på några vanliga frågor om Azure Kubernetes service (AKS).
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 10/02/2019
ms.author: mlearned
ms.openlocfilehash: 4d736556147797bcd007bdab1b5328deeadea712
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827358"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Vanliga frågor och svar om Azure Kubernetes service (AKS)

Den här artikeln behandlar vanliga frågor om Azure Kubernetes service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Vilka Azure-regioner tillhandahåller AKS för närvarande?

En fullständig lista över tillgängliga regioner finns i [AKS regioner och tillgänglighet][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Stöder AKS autoskalning av noder?

Ja, möjligheten att automatiskt skala agent noder vågrätt i AKS är för närvarande tillgänglig i för hands versionen. Se [automatisk skalning av ett kluster för att uppfylla program kraven i AKS][aks-cluster-autoscaler] för instruktioner. AKS autoskalning baseras på [Kubernetes autoskalning][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Kan jag distribuera AKS till mitt befintliga virtuella nätverk?

Ja, du kan distribuera ett AKS-kluster till ett befintligt virtuellt nätverk med hjälp av [funktionen avancerade nätverksfunktioner][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kan jag begränsa vem som har åtkomst till Kubernetes API-servern?

Ja, du kan begränsa åtkomsten till Kubernetes-API-servern med hjälp av [tillåtna IP-intervall för API-servern][api-server-authorized-ip-ranges], som för närvarande är en för hands version

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Kan jag bara göra Kubernetes-API-servern tillgänglig i mitt virtuella nätverk?

Inte just nu, men det är planerat. Du kan följa förloppet för [AKS GitHub-lagrings platsen][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Kan jag ha olika storlekar på virtuella datorer i ett enda kluster?

Ja, du kan använda olika storlekar för virtuella datorer i ditt AKS-kluster genom att skapa [flera noder][multi-node-pools], som för närvarande finns i för hands version.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Används säkerhets uppdateringar för AKS-agent-noder?

Azure tillämpar automatiskt säkerhets korrigeringar på Linux-noderna i klustret enligt ett natt schema. Du är dock ansvarig för att se till att de Linux-noderna startas om efter behov. Du har flera alternativ för att starta om noder:

- Manuellt, via Azure Portal eller Azure CLI.
- Genom att uppgradera ditt AKS-kluster. Klustret uppgraderar automatiskt [Cordon och tömmer noderna][cordon-drain] och ansluter sedan en ny nod online med den senaste Ubuntu-avbildningen och en ny korrigerings version eller en lägre Kubernetes-version. Mer information finns i [uppgradera ett AKS-kluster][aks-upgrade].
- Med hjälp av [Kured](https://github.com/weaveworks/kured), en daemon för omstart med öppen källkod för Kubernetes. Kured körs som en [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) och övervakar varje nod för förekomst av en fil som anger att en omstart krävs. I klustret hanteras OS-omstarter av samma [Cordon och tömnings processen][cordon-drain] som en kluster uppgradering.

Mer information om hur du använder kured finns i [tillämpa säkerhets-och kernel-uppdateringar på noder i AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Windows Server-noder

För Windows Server-noder (för närvarande i för hands version i AKS) körs inte Windows Update automatiskt och tillämpar de senaste uppdateringarna. I ett reguljärt schema kring Windows Updates lanserings cykel och din egen verifierings process bör du utföra en uppgradering på klustret och Windows Server-pool (er) i ditt AKS-kluster. Den här uppgraderings processen skapar noder som kör den senaste Windows Server-avbildningen och uppdateringar och tar sedan bort de äldre noderna. Mer information om den här processen finns [i uppgradera en Node-pool i AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Varför skapas två resurs grupper med AKS?

AKS bygger på ett antal Azure-infrastruktur resurser, inklusive skalnings uppsättningar för virtuella datorer, virtuella nätverk och hanterade diskar. På så sätt kan du utnyttja många av kärn funktionerna i Azure-plattformen i den hanterade Kubernetes-miljön som tillhandahålls av AKS. De flesta typer av virtuella Azure-datorer kan till exempel användas direkt med AKS och Azure Reservations kan användas för att ta emot rabatter på dessa resurser automatiskt.

För att aktivera den här arkitekturen omfattar varje AKS-distribution två resurs grupper:

1. Du skapar den första resurs gruppen. Den här gruppen innehåller endast Kubernetes-Tjänsteresursen. AKS Resource Provider skapar automatiskt den andra resurs gruppen under distributionen. Ett exempel på den andra resurs gruppen är *MC_myResourceGroup_myAKSCluster_eastus*. Information om hur du anger namnet på den här andra resurs gruppen finns i nästa avsnitt.
1. Den andra resurs gruppen, som kallas *resurs gruppen för noden*, innehåller alla infrastruktur resurser som är associerade med klustret. Dessa resurser omfattar Kubernetes-nodens virtuella datorer, virtuella nätverk och lagring. Som standard har noden resurs grupp ett namn som *MC_myResourceGroup_myAKSCluster_eastus*. AKS tar automatiskt bort nodens resurs när klustret tas bort, så den bör endast användas för resurser som delar klustrets livs cykel.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Kan jag ange mitt eget namn för AKS-nodens resurs grupp?

Ja. Som standard namnger AKS resurs gruppen *MC_resourcegroupname_clustername_location*, men du kan också ange ett eget namn.

Om du vill ange ett eget namn på en resurs grupp installerar du [AKS-Preview][aks-preview-cli] Azure CLI-tillägget version *0.3.2* eller senare. När du skapar ett AKS-kluster med hjälp av kommandot [AZ AKS Create][az-aks-create] använder du parametern *--Node-Resource-Group* och anger ett namn för resurs gruppen. Om du [använder en Azure Resource Manager-mall][aks-rm-template] för att distribuera ett AKS-kluster kan du definiera resurs gruppens namn genom att använda egenskapen *nodeResourceGroup* .

* Den sekundära resurs gruppen skapas automatiskt av Azure Resource Provider i din egen prenumeration.
* Du kan bara ange ett namn på en anpassad resurs grupp när du skapar klustret.

När du arbetar med resurs gruppen för noden bör du tänka på att du inte kan:

* Ange en befintlig resurs grupp för resurs gruppen för noden.
* Ange en annan prenumeration för resurs gruppen för noden.
* Ändra resurs grupp namnet för noden när klustret har skapats.
* Ange namn för de hanterade resurserna i resurs gruppen för noden.
* Ändra eller ta bort taggar för hanterade resurser i resurs gruppen för noden. (Mer information finns i nästa avsnitt.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Kan jag ändra Taggar och andra egenskaper för AKS-resurserna i nodens resurs grupp?

Om du ändrar eller tar bort Azure-skapade Taggar och andra resurs egenskaper i resurs gruppen resurs kan du få oväntade resultat som skalning och uppgradering av fel. Med AKS kan du skapa och ändra anpassade taggar. Du kanske vill skapa eller ändra anpassade taggar, till exempel för att tilldela en affär senhet eller ett kostnads ställe. Genom att ändra resurserna under resurs gruppen nod i AKS-klustret, bryter du ned service nivå målet (service nivå målet). Mer information finns i [AKS erbjuder ett service nivå avtal?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Vilka Kubernetes-kontrollanter stöder AKS? Kan åtkomst kontrol Lanterna läggas till eller tas bort?

AKS stöder följande styrenheter för [åtkomst][admission-controllers]kontroll:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

För närvarande kan du inte ändra listan över åtkomst kontrol listor i AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Är Azure Key Vault integrerat med AKS?

AKS är för närvarande inte inbyggt i Azure Key Vault. Men [Azure Key Vault FlexVolume för Kubernetes-projekt][keyvault-flexvolume] gör det möjligt att direkt integrera från Kubernetes poddar till Key Vault hemligheter.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kan jag köra Windows Server-behållare på AKS?

Ja, Windows Server-behållare är tillgängliga i för hands versionen. Om du vill köra Windows Server-behållare i AKS skapar du en resurspool som kör Windows Server som gäst operativ system. Windows Server-behållare kan endast använda Windows Server 2019. Information om hur du kommer igång finns i [skapa ett AKS-kluster med en pool för Windows Server-noder][aks-windows-cli].

Windows Server-stöd för Node-pool innehåller vissa begränsningar som ingår i den överordnade Windows Server i Kubernetes-projektet. Mer information om dessa begränsningar finns i [Windows Server-behållare i AKS-begränsningar][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Erbjuder AKS ett service nivå avtal?

I ett service avtal (SLA) accepterar providern att återbetala kunden till kostnaden för tjänsten om den publicerade Service nivån inte är uppfylld. Eftersom AKS är kostnads fritt är ingen kostnad tillgänglig för åter betalning, så AKS har inget formellt service avtal. Men AKS strävar efter att behålla tillgänglighet för minst 99,5 procent för Kubernetes API-servern.

Det är viktigt att känna till skillnaden mellan AKS-tjänstens tillgänglighet som avser drift tid av Kubernetes-kontroll planet och tillgängligheten för din speciella arbets belastning som körs på Azure Virtual Machines. Kontroll planet kan vara otillgängligt om kontroll planet inte är klart, men dina kluster arbets belastningar som körs på virtuella Azure-datorer kan fortfarande fungera. De virtuella Azure-datorerna är betalda resurser som de backas upp av ett finansiellt service avtal. Läs [här om du vill ha mer information](https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_8/) om service avtalet för Azure VM och hur du ökar tillgängligheten med funktioner som [Tillgänglighetszoner][availability-zones].

## <a name="why-cant-i-set-maxpods-below-30"></a>Varför kan jag inte ange maxPods under 30?

I AKS kan du ange `maxPods` värdet när du skapar klustret med hjälp av Azure CLI och Azure Resource Manager mallar. Både Kubernetes och Azure CNI kräver dock ett *minsta värde* (verifieras vid skapande):

| Nätverk | Minimum | Maximal |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubernetes | 30 | 110 |

Eftersom AKS är en hanterad tjänst distribuerar vi och hanterar tillägg och poddar som en del av klustret. Tidigare kunde användare definiera ett `maxPods` värde som är lägre än värdet som de hanterade poddar krävde för att köra (till exempel 30). AKS beräknar nu det minsta antalet poddar med hjälp av följande formel: ((maxPods eller (maxPods * vm_count)) > hanterat tillägg poddar minimum.

Användare kan inte åsidosätta `maxPods` den lägsta verifieringen.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Kan jag använda Azure reservation-rabatter på mina AKS-agent-noder?

AKS agent-noder faktureras som standard virtuella Azure-datorer, så om du har köpt [Azure-reservationer][reservation-discounts] för den VM-storlek som du använder i AKS tillämpas dessa rabatter automatiskt.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Kan jag flytta/migrera mitt kluster mellan Azure-klienter?

`az aks update-credentials` Kommandot kan användas för att flytta ett AKS-kluster mellan Azure-klienter. Följ anvisningarna i [Välj för att uppdatera eller skapa ett huvud namn för tjänsten](https://docs.microsoft.com/azure/aks/update-credentials) och [uppdatera sedan AKS-kluster med nya autentiseringsuppgifter](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Kan jag flytta/migrera mitt kluster mellan prenumerationer?

Det finns för närvarande inte stöd för att flytta kluster mellan prenumerationer.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Kan jag flytta mina AKS-kluster från den aktuella Azure-prenumerationen till en annan? 

Det finns inte stöd för att flytta AKS-klustret och dess tillhör ande resurser mellan Azure-prenumerationer.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Varför tar min kluster borttagning att ta lång tid? 

De flesta kluster tas bort vid användar förfrågan. i vissa fall, särskilt när kunder tar emot sin egen resurs grupp, eller om du utför RG uppgifter kan borttagningen ta ytterligare tid eller misslyckande. Om du har problem med borttagningarna, kontrol lera att du inte har lås på RG, att inga resurser utanför RG är kopplade till RG osv.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Om jag har Pod/distributioner i status "NodeLost" eller "okänd" kan jag fortfarande uppgradera mitt kluster?

Du kan, men AKS rekommenderar inte detta. Uppgraderingar bör helst utföras när kluster tillståndet är känt och felfritt.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Kan jag utföra en uppgradering om jag har ett kluster med en eller flera noder i ett ohälsosamt tillstånd eller stänger av?

Nej, ta bort/ta bort alla noder i ett felaktigt tillstånd eller ta bort dem från klustret innan du uppgraderar.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Det gick inte att ta bort klustret, men du kan se felet `[Errno 11001] getaddrinfo failed` 

Oftast orsakas detta av användare som har en eller flera nätverks säkerhets grupper (NSG: er) som fortfarande används och som är kopplade till klustret.  Ta bort dem och försök att ta bort dem igen.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Jag körde en uppgradering, men nu finns det poddar i krascher och det går inte att söka efter beredskap?

Kontrol lera att tjänstens huvud namn inte har upphört att gälla.  Se: Autentiseringsuppgifter för [AKS-tjänstens huvud namn](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) och [AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Mitt kluster fungerade, men det gick plötsligt inte att etablera belastningsutjämnare, montera PVC: er osv. 

Kontrol lera att tjänstens huvud namn inte har upphört att gälla.  Se: Autentiseringsuppgifter för [AKS-tjänstens huvud namn](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) och [AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Kan jag använda API: erna för skalnings uppsättningen för virtuella datorer för att skala manuellt?

Nej, skalnings åtgärder med hjälp av API: er för skalnings uppsättningen för virtuella datorer stöds inte. Använd AKS-API: erna (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Kan jag använda skalnings uppsättningar för virtuella datorer för att manuellt skala till 0 noder?

Nej, skalnings åtgärder med hjälp av API: er för skalnings uppsättningen för virtuella datorer stöds inte.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Kan jag stoppa eller ta bort alla mina virtuella datorer?

Även om AKS har återhämtnings metoder för att motstå sådan konfiguration och återställa från den, är detta inte en rekommenderad konfiguration.

## <a name="can-i-use-custom-vm-extensions"></a>Kan jag använda anpassade VM-tillägg?

Ingen AKS är en hanterad tjänst och manipulering av IaaS-resurser stöds inte. Så här installerar du anpassade komponenter osv. Använd Kubernetes-API: er och mekanismer. Du kan till exempel utnyttja DaemonSets för att installera nödvändiga komponenter.

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
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
