---
title: Vanliga frågor och svar för Azure Kubernetes Service (AKS)
description: Hitta svar på några av de vanligaste frågorna om Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: c4bb4328af5df7f729967c7b249847b2ab098770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497764"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Vanliga frågor om Azure Kubernetes Service (AKS)

Den här artikeln innehåller ofta frågor om Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Vilka Azure-regioner tillhandahåller för närvarande AKS?

En fullständig lista över tillgängliga regioner finns i [AKS-regioner och tillgänglighet][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Stöder AKS automatisk skalning av nod?

Ja, möjligheten att automatiskt skala agentnoder horisontellt i AKS är för närvarande tillgänglig i förhandsversionen. Se [Skala automatiskt ett kluster för att uppfylla programkraven i AKS][aks-cluster-autoscaler] för instruktioner. AKS autoskalning baseras på [Kubernetes autoskalning][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Kan jag distribuera AKS till mitt befintliga virtuella nätverk?

Ja, du kan distribuera ett AKS-kluster till ett befintligt virtuellt nätverk med hjälp av den [avancerade nätverksfunktionen][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kan jag begränsa vem som har åtkomst till Kubernetes API-server?

Ja, du kan begränsa åtkomsten till Kubernetes API-server med [API Server Auktoriserade IP-intervall][api-server-authorized-ip-ranges].

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Kan jag göra Kubernetes API-server endast tillgänglig i mitt virtuella nätverk?

Inte just nu, men det här är planerat. Du kan spåra förloppet på [AKS GitHub repo][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Kan jag ha olika vm-storlekar i ett enda kluster?

Ja, du kan använda olika storlekar för virtuella datorer i AKS-klustret genom att skapa [flera nodpooler][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Tillämpas säkerhetsuppdateringar på AKS-agentnoder?

Azure tillämpar automatiskt säkerhetskorrigeringar på Linux-noderna i klustret enligt ett nattligt schema. Du är dock ansvarig för att se till att dessa Linux-noder startas om efter behov. Du har flera alternativ för att starta om noder:

- Manuellt, via Azure-portalen eller Azure CLI.
- Genom att uppgradera AKS-klustret. Klustret uppgraderar [avspärrning och tömningsnoder][cordon-drain] automatiskt och tar sedan en ny nod online med den senaste Ubuntu-avbildningen och en ny patchversion eller en mindre Kubernetes-version. Mer information finns i [Uppgradera ett AKS-kluster][aks-upgrade].
- Genom att använda [Kured](https://github.com/weaveworks/kured), en öppen källkod omstart demon för Kubernetes. Kured körs som en [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) och övervakar varje nod för förekomsten av en fil som anger att en omstart krävs. I klustret hanteras omstarter av operativsystemet av samma [avspärrning och tömningsprocess][cordon-drain] som en klusteruppgradering.

Mer information om hur du använder kured finns i [Tillämpa säkerhets- och kerneluppdateringar på noder i AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Windows Server-noder

För Windows Server-noder (för närvarande i förhandsversion i AKS) körs inte windows update automatiskt och de senaste uppdateringarna. På ett regelbundet schema runt windows update-utgivningscykeln och din egen valideringsprocess bör du utföra en uppgradering på klustret och Windows Server-nodpoolerna i AKS-klustret. Den här uppgraderingsprocessen skapar noder som kör den senaste Windows Server-avbildningen och korrigeringsfilerna och tar sedan bort de äldre noderna. Mer information om den här processen finns [i Uppgradera en nodpool i AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Varför skapas två resursgrupper med AKS?

AKS bygger på ett antal Azure-infrastrukturresurser, inklusive skaluppsättningar för virtuella datorer, virtuella nätverk och hanterade diskar. På så sätt kan du utnyttja många av kärnfunktionerna i Azure-plattformen i den hanterade Kubernetes-miljön som tillhandahålls av AKS. De flesta azure-typer av virtuella datorer kan till exempel användas direkt med AKS och Azure-reservationer kan användas för att få rabatter på dessa resurser automatiskt.

Om du vill aktivera den här arkitekturen omfattar varje AKS-distribution två resursgrupper:

1. Du skapar den första resursgruppen. Den här gruppen innehåller endast kubernetes-tjänstresursen. AKS-resursprovidern skapar automatiskt den andra resursgruppen under distributionen. Ett exempel på den andra resursgruppen är *MC_myResourceGroup_myAKSCluster_eastus*. Information om hur du anger namnet på den andra resursgruppen finns i nästa avsnitt.
1. Den andra resursgruppen, känd som *nodresursgruppen,* innehåller alla infrastrukturresurser som är associerade med klustret. Dessa resurser omfattar virtuella datorer i Kubernetes-nod, virtuella nätverk och lagring. Som standard har nodresursgruppen ett namn som *MC_myResourceGroup_myAKSCluster_eastus*. AKS tar automatiskt bort nodresursen när klustret tas bort, så den bör endast användas för resurser som delar klustrets livscykel.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Kan jag ange mitt eget namn för AKS-nodresursgruppen?

Ja. Som standard namnger AKS nodresursgruppen *MC_resourcegroupname_clustername_location,* men du kan också ange ditt eget namn.

Om du vill ange ditt eget resursgruppsnamn installerar du [azure CLI-tilläggsversionen][aks-preview-cli] *0.3.2* eller senare. När du skapar ett AKS-kluster med kommandot [az aks create][az-aks-create] använder du parametern *--nod-resource-group* och anger ett namn för resursgruppen. Om du [använder en Azure Resource Manager-mall][aks-rm-template] för att distribuera ett AKS-kluster kan du definiera resursgruppsnamnet med hjälp av egenskapen *nodeResourceGroup.*

* Den sekundära resursgruppen skapas automatiskt av Azure-resursleverantören i din egen prenumeration.
* Du kan bara ange ett anpassat resursgruppsnamn när du skapar klustret.

När du arbetar med nodresursgruppen bör du tänka på att du inte kan:

* Ange en befintlig resursgrupp för nodresursgruppen.
* Ange en annan prenumeration för nodresursgruppen.
* Ändra nodresursgruppnamnet när klustret har skapats.
* Ange namn för hanterade resurser inom nodresursgruppen.
* Ändra eller ta bort taggar för hanterade resurser i nodresursgruppen. (Se ytterligare information i nästa avsnitt.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Kan jag ändra taggar och andra egenskaper för AKS-resurserna i nodresursgruppen?

Om du ändrar eller tar bort Azure-skapade taggar och andra resursegenskaper i nodresursgruppen kan du få oväntade resultat som skalning och uppgradering av fel. MED AKS kan du skapa och ändra anpassade taggar. Du kanske vill skapa eller ändra anpassade taggar, till exempel för att tilldela en affärsenhet eller kostnadsställe. Genom att ändra resurserna under nodresursgruppen i AKS-klustret bryter du mål på servicenivå (SLO). Mer information finns i [Erbjuder AKS ett servicenivåavtal?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Vad Kubernetes antagningskontrollanter stöder AKS? Kan antagningskontrollanter läggas till eller tas bort?

AKS stöder följande [antagningskontrollanter:][admission-controllers]

- *NamnområdeLivscykel*
- *LimitRanger (3000)*
- *ServiceKonto*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MuteraAdmissionWebhook*
- *ValideraLäggarenWebhook*
- *ResourceQuota (ResourceQuota)*

För närvarande kan du inte ändra listan över antagningskontroller i AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Är Azure Key Vault integrerat med AKS?

AKS är för närvarande inte inbyggt integrerat med Azure Key Vault. [Azure Key Vault FlexVolume for Kubernetes-projektet][keyvault-flexvolume] möjliggör dock direkt integrering från Kubernetes poddar till Key Vault-hemligheter.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kan jag köra Windows Server-behållare på AKS?

Ja, Windows Server-behållare är tillgängliga i förhandsversionen. Om du vill köra Windows Server-behållare i AKS skapar du en nodpool som kör Windows Server som gästoperativsystem. Windows Server-behållare kan bara använda Windows Server 2019. Kom igång finns i [Skapa ett AKS-kluster med en Windows Server-nodpool][aks-windows-cli].

Windows Server-stöd för nodpool innehåller vissa begränsningar som ingår i projektet Windows Server i Kubernetes. Mer information om dessa begränsningar finns [i Windows Server-behållare i AKS-begränsningar][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Erbjuder AKS ett servicenivåavtal?

I ett servicenivåavtal (SLA) samtycker leverantören till att ersätta kunden för kostnaden för tjänsten om den publicerade servicenivån inte uppfylls. Eftersom AKS är gratis är det ingen kostnad att ersätta, så AKS har inget formellt serviceavtal. AKS försöker dock behålla tillgängligheten på minst 99,5 procent för Kubernetes API-server.

Det är viktigt att känna igen skillnaden mellan AKS-tjänsttillgänglighet som refererar till drifttid för Kubernetes kontrollplan och tillgängligheten för din specifika arbetsbelastning som körs på Virtuella Azure-datorer. Även om kontrollplanet kanske inte är tillgängligt om kontrollplanet inte är klart, kan klusterarbetsbelastningarna som körs på virtuella Azure-datorer fortfarande fungera. Med tanke på Azure virtuella datorer är betalda resurser de backas upp av en ekonomisk SLA. Läs [här för mer information](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) om Azure VM SLA och hur du ökar tillgängligheten med funktioner som [tillgänglighetszoner][availability-zones].

## <a name="why-cant-i-set-maxpods-below-30"></a>Varför kan jag inte ställa maxPods under 30?

I AKS kan du `maxPods` ange värdet när du skapar klustret med hjälp av Azure CLI- och Azure Resource Manager-mallarna. Både Kubenet och Azure CNI kräver dock ett *minimivärde* (validerat vid skapande):

| Nätverk | Minimum | Maximal |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet (på) | 30 | 110 |

Eftersom AKS är en hanterad tjänst distribuerar och hanterar vi tillägg och poddar som en del av klustret. Tidigare kan användarna definiera `maxPods` ett värde som är lägre än det värde som de hanterade poddar som krävs för att köra (till exempel 30). AKS beräknar nu det minsta antalet poddar med hjälp av denna formel: ((maxPods eller (maxPods * vm_count)) > hanterade tilläggspoddar minimum.

Användare kan inte åsidosätta den minsta `maxPods` valideringen.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Kan jag tillämpa Azure-reservationsrabatter på mina AKS-agentnoder?

AKS-agentnoder faktureras som vanliga virtuella Azure-datorer, så om du har köpt [Azure-reservationer][reservation-discounts] för den virtuella datorns storlek som du använder i AKS tillämpas dessa rabatter automatiskt.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Kan jag flytta/migrera mitt kluster mellan Azure-klienter?

Kommandot `az aks update-credentials` kan användas för att flytta ett AKS-kluster mellan Azure-klienter. Följ instruktionerna i [Välj för att uppdatera eller skapa ett huvudnamn för tjänsten](https://docs.microsoft.com/azure/aks/update-credentials) och uppdatera sedan [AKS-klustret med nya autentiseringsuppgifter](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Kan jag flytta/migrera mitt kluster mellan prenumerationer?

Förflyttning av kluster mellan prenumerationer stöds för närvarande inte.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Kan jag flytta mina AKS-kluster från den aktuella azure-prenumerationen till en annan? 

Det går inte att flytta AKS-klustret och dess associerade resurser mellan Azure-prenumerationer.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Varför tar det så lång tid att ta bort mitt kluster? 

De flesta kluster tas bort på användarbegäran. I vissa fall, särskilt där kunderna tar med sig en egen resursgrupp, eller gör borttagning av RG-uppgifter mellan RG-uppgifter kan det ta ytterligare tid eller misslyckas. Om du har ett problem med borttagningar, dubbelkolla att du inte har lås på RG, att alla resurser utanför RG är separerade från RG, etc.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Om jag har pod/distributioner i tillståndet NodeLost eller "Okänd" kan jag fortfarande uppgradera mitt kluster?

Det kan du, men AKS rekommenderar inte detta. Uppgraderingar bör helst utföras när klustrets tillstånd är känt och felfritt.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Om jag har ett kluster med en eller flera noder i feltillstånd eller stängs av, kan jag utföra en uppgradering?

Nej, ta bort/ta bort alla noder i ett misslyckat tillstånd eller på annat sätt borttaget från klustret innan du uppgraderar.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Jag körde en klusterborttagning, men ser felet`[Errno 11001] getaddrinfo failed` 

Oftast beror detta på att användare som har en eller flera NSG-grupper (Network Security Groups) fortfarande används och associeras med klustret.  Ta bort dem och försök ta bort igen.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Jag körde en uppgradering, men nu min pods är i krasch loopar, och beredskap sonder misslyckas?

Bekräfta att ditt huvudnamn för tjänsten inte har upphört att gälla.  Se: [AKS-tjänstens huvudnamn](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) och [AKS-uppdateringsautentiseringsuppgifter](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Mitt kluster fungerade, men plötsligt kan inte etablera LoadBalancers, montera PVC, etc.? 

Bekräfta att ditt huvudnamn för tjänsten inte har upphört att gälla.  Se: [AKS-tjänstens huvudnamn](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) och [AKS-uppdateringsautentiseringsuppgifter](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Kan jag använda API:erna för skalningsuppsättning för virtuell dator för att skala manuellt?

Nej, skalningsåtgärder med hjälp av API:er för virtuell datorskalauppsättning stöds inte. Använd AKS API:er (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Kan jag använda skalningsuppsättningar för virtuella datorer för att manuellt skala till 0 noder?

Nej, skalningsåtgärder med hjälp av API:er för virtuell datorskalauppsättning stöds inte.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Kan jag stoppa eller avfördela alla mina virtuella datorer?

Medan AKS har resiliensmekanismer för att motstå en sådan config och återhämta sig från den, är detta inte en rekommenderad konfiguration.

## <a name="can-i-use-custom-vm-extensions"></a>Kan jag använda anpassade VM-tillägg?

Ingen AKS är en hanterad tjänst och manipulering av IaaS-resurserna stöds inte. För att installera anpassade komponenter, etc. utnyttja Kubernetes API:er och mekanismer. Utnyttja till exempel DaemonSets för att installera nödvändiga komponenter.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
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
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
