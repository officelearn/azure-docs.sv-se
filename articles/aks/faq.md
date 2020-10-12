---
title: Vanliga frågor och svar om Azure Kubernetes service (AKS)
description: Hitta svar på några vanliga frågor om Azure Kubernetes service (AKS).
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 4150f850263aed7b8aa4317028386dc285f06ade
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905340"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Vanliga frågor om Azure Kubernetes Service (AKS)

Den här artikeln behandlar vanliga frågor om Azure Kubernetes service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Vilka Azure-regioner tillhandahåller AKS för närvarande?

En fullständig lista över tillgängliga regioner finns i [AKS regioner och tillgänglighet][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Kan jag sprida ett AKS-kluster över flera regioner?

Nej. AKS-kluster är regionala resurser och kan inte omfatta regioner. Se [metod tips för verksamhets kontinuitet och haveri beredskap][bcdr-bestpractices] för vägledning om hur du skapar en arkitektur som innehåller flera regioner.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Kan jag sprida ett AKS-kluster mellan tillgänglighets zoner?

Ja. Du kan distribuera ett AKS-kluster över en eller flera [tillgänglighets zoner][availability-zones] i [regioner som stöder dem][az-regions].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kan jag begränsa vem som har åtkomst till Kubernetes API-servern?

Ja. Det finns två alternativ för att begränsa åtkomsten till API-servern:

- Använd [tillåtna IP-intervall för API-Server][api-server-authorized-ip-ranges] om du vill behålla en offentlig slut punkt för API-servern men begränsa åtkomsten till en uppsättning betrodda IP-intervall.
- Använd [ett privat kluster][private-clusters] om du vill begränsa API-servern till att *endast* vara tillgänglig från det virtuella nätverket.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Kan jag ha olika storlekar på virtuella datorer i ett enda kluster?

Ja, du kan använda olika storlekar för virtuella datorer i AKS-klustret genom att skapa [flera noder][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Används säkerhets uppdateringar för AKS-agent-noder?

Azure tillämpar automatiskt säkerhets korrigeringar på Linux-noderna i klustret enligt ett natt schema. Du är dock ansvarig för att se till att de Linux-noderna startas om efter behov. Du har flera alternativ för att starta om noder:

- Manuellt, via Azure Portal eller Azure CLI.
- Genom att uppgradera ditt AKS-kluster. Klustret uppgraderar automatiskt [Cordon och tömmer noderna][cordon-drain] och ansluter sedan en ny nod online med den senaste Ubuntu-avbildningen och en ny korrigerings version eller en lägre Kubernetes-version. Mer information finns i [uppgradera ett AKS-kluster][aks-upgrade].
- Med hjälp av [Kured](https://github.com/weaveworks/kured), en daemon för omstart med öppen källkod för Kubernetes. Kured körs som en [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) och övervakar varje nod för förekomst av en fil som anger att en omstart krävs. I klustret hanteras OS-omstarter av samma [Cordon och tömnings processen][cordon-drain] som en kluster uppgradering.

Mer information om hur du använder kured finns i [tillämpa säkerhets-och kernel-uppdateringar på noder i AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Windows Server-noder

För Windows Server-noder körs Windows Update inte automatiskt och tillämpar de senaste uppdateringarna. I ett reguljärt schema kring Windows Updates lanserings cykel och din egen verifierings process bör du utföra en uppgradering på klustret och Windows Server-pool (er) i ditt AKS-kluster. Den här uppgraderings processen skapar noder som kör den senaste Windows Server-avbildningen och uppdateringar och tar sedan bort de äldre noderna. Mer information om den här processen finns [i uppgradera en Node-pool i AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Varför skapas två resurs grupper med AKS?

AKS bygger på ett antal Azure-infrastruktur resurser, inklusive skalnings uppsättningar för virtuella datorer, virtuella nätverk och hanterade diskar. På så sätt kan du utnyttja många av kärn funktionerna i Azure-plattformen i den hanterade Kubernetes-miljön som tillhandahålls av AKS. De flesta typer av virtuella Azure-datorer kan till exempel användas direkt med AKS och Azure Reservations kan användas för att ta emot rabatter på dessa resurser automatiskt.

För att aktivera den här arkitekturen omfattar varje AKS-distribution två resurs grupper:

1. Du skapar den första resurs gruppen. Den här gruppen innehåller endast Kubernetes-Tjänsteresursen. AKS Resource Provider skapar automatiskt den andra resurs gruppen under distributionen. Ett exempel på den andra resurs gruppen är *MC_myResourceGroup_myAKSCluster_eastus*. Information om hur du anger namnet på den här andra resurs gruppen finns i nästa avsnitt.
1. Den andra resurs gruppen, som kallas *resurs gruppen för noden*, innehåller alla infrastruktur resurser som är associerade med klustret. Dessa resurser omfattar Kubernetes-nodens virtuella datorer, virtuella nätverk och lagring. Som standard har resurs gruppen ett namn som *MC_myResourceGroup_myAKSCluster_eastus*. AKS tar automatiskt bort nodens resurs när klustret tas bort, så den bör endast användas för resurser som delar klustrets livs cykel.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Kan jag ange mitt eget namn för AKS-nodens resurs grupp?

Ja. Som standard namnger AKS resurs *MC_resourcegroupname_clustername_location*gruppen för noden, men du kan också ange ett eget namn.

Om du vill ange ett eget namn på en resurs grupp installerar du [AKS-Preview][aks-preview-cli] Azure CLI-tillägget version *0.3.2* eller senare. När du skapar ett AKS-kluster med hjälp av kommandot [AZ AKS Create][az-aks-create] använder du parametern *--Node-Resource-Group* och anger ett namn för resurs gruppen. Om du [använder en Azure Resource Manager-mall][aks-rm-template] för att distribuera ett AKS-kluster kan du definiera resurs gruppens namn genom att använda egenskapen *nodeResourceGroup* .

* Den sekundära resurs gruppen skapas automatiskt av Azure Resource Provider i din egen prenumeration.
* Du kan bara ange ett namn på en anpassad resurs grupp när du skapar klustret.

När du arbetar med resurs gruppen för noden bör du tänka på att du inte kan:

* Ange en befintlig resurs grupp för resurs gruppen för noden.
* Ange en annan prenumeration för resurs gruppen för noden.
* Ändra resurs grupp namnet för noden när klustret har skapats.
* Ange namn för de hanterade resurserna i resurs gruppen för noden.
* Ändra eller ta bort Azure-skapade Taggar av hanterade resurser i resurs gruppen för noden. (Mer information finns i nästa avsnitt.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Kan jag ändra Taggar och andra egenskaper för AKS-resurserna i nodens resurs grupp?

Om du ändrar eller tar bort Azure-skapade Taggar och andra resurs egenskaper i resurs gruppen resurs kan du få oväntade resultat som skalning och uppgradering av fel. Med AKS kan du skapa och ändra anpassade taggar som skapats av slutanvändare och du kan lägga till dessa taggar när du [skapar en Node-pool](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool). Du kanske vill skapa eller ändra anpassade taggar, till exempel för att tilldela en affär senhet eller ett kostnads ställe. Detta kan också uppnås genom att du skapar Azure-principer med ett omfång i den hanterade resurs gruppen.

Att ändra alla **Azure-skapade Taggar** för resurser under nodens resurs grupp i AKS-klustret är dock en åtgärd som inte stöds och som bryter service nivå målet (service nivå mål). Mer information finns i [AKS erbjuder ett service nivå avtal?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Vilka Kubernetes-kontrollanter stöder AKS? Kan åtkomst kontrol Lanterna läggas till eller tas bort?

AKS stöder följande [styrenheter för åtkomst][admission-controllers]kontroll:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*

För närvarande kan du inte ändra listan över åtkomst kontrol listor i AKS.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Kan jag använda Webhooks för Admission Controller på AKS?

Ja, du kan använda Webhooks för Admission Controller på AKS. Vi rekommenderar att du exkluderar interna AKS-namnområden som är markerade med **kontroll Plans etiketten.** Till exempel genom att lägga till följande i webhook-konfigurationen:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Kan inspelade Webhooks påverkar Kube-system och interna AKS-namnområden?

För att skydda systemets stabilitet och förhindra att anpassade kontroll enheter påverkar de interna tjänsterna i Kube-systemet, har namn området AKS en- **tvång**, som automatiskt undantar Kube-system och AKS interna namn områden. Den här tjänsten säkerställer att de anpassade åtkomst kontrol Lanterna inte påverkar de tjänster som körs i Kube-system.

Om du har ett kritiskt användnings fall där något har distribuerats på Kube (rekommenderas inte) som du behöver omfattas av din anpassade-webhook, kan du lägga till nedanstående etikett eller antecknings anteckning så att inträde-tvång ignorerar den.

Etikett: ```"admissions.enforcer/disabled": "true"``` eller anteckning: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Är Azure Key Vault integrerat med AKS?

AKS är för närvarande inte inbyggt i Azure Key Vault. [Azure Key Vault leverantören för CSI hemligheter-butiken][csi-driver] gör det dock möjligt att direkt integrera från Kubernetes poddar till Key Vault hemligheter.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kan jag köra Windows Server-behållare på AKS?

Ja, Windows Server-behållare finns på AKS. Om du vill köra Windows Server-behållare i AKS skapar du en resurspool som kör Windows Server som gäst operativ system. Windows Server-behållare kan endast använda Windows Server 2019. Information om hur du kommer igång finns i [skapa ett AKS-kluster med en pool för Windows Server-noder][aks-windows-cli].

Windows Server-stöd för Node-pool innehåller vissa begränsningar som ingår i den överordnade Windows Server i Kubernetes-projektet. Mer information om dessa begränsningar finns i [Windows Server-behållare i AKS-begränsningar][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Erbjuder AKS ett service nivå avtal?

AKS tillhandahåller SLA-garantier som en valfri extra tilläggs funktion med [SLA för drift tid][uptime-sla].

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Kan jag använda Azure reservation-rabatter på mina AKS-agent-noder?

AKS agent-noder faktureras som standard virtuella Azure-datorer, så om du har köpt [Azure-reservationer][reservation-discounts] för den VM-storlek som du använder i AKS tillämpas dessa rabatter automatiskt.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Kan jag flytta/migrera mitt kluster mellan Azure-klienter?

Det finns för närvarande inte stöd för att flytta AKS-kluster mellan klient organisationer.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Kan jag flytta/migrera mitt kluster mellan prenumerationer?

Det finns för närvarande inte stöd för att flytta kluster mellan prenumerationer.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Kan jag flytta mina AKS-kluster från den aktuella Azure-prenumerationen till en annan? 

Det går inte att flytta ditt AKS-kluster och dess associerade resurser mellan Azure-prenumerationer.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Kan jag flytta mitt AKS-kluster eller AKS-infrastruktur resurser till andra resurs grupper eller byta namn på dem?

Det går inte att flytta eller byta namn på ditt AKS-kluster och dess associerade resurser.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Varför tar min kluster borttagning att ta lång tid? 

De flesta kluster tas bort vid användar förfrågan. i vissa fall, särskilt när kunder tar emot sin egen resurs grupp, eller om du utför RG uppgifter kan borttagningen ta ytterligare tid eller misslyckande. Om du har problem med borttagningarna, kontrol lera att du inte har lås på RG, att inga resurser utanför RG är kopplade till RG osv.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Om jag har Pod/distributioner i status "NodeLost" eller "okänd" kan jag fortfarande uppgradera mitt kluster?

Du kan, men AKS rekommenderar inte detta. Uppgraderingar bör helst utföras när kluster tillståndet är känt och felfritt.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Kan jag utföra en uppgradering om jag har ett kluster med en eller flera noder i ett ohälsosamt tillstånd eller stänger av?

Nej, ta bort/ta bort alla noder i ett felaktigt tillstånd eller ta bort dem från klustret innan du uppgraderar.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Jag har kört ett kluster borttagnings fel men ser felet `[Errno 11001] getaddrinfo failed` 

Oftast orsakas detta av användare som har en eller flera nätverks säkerhets grupper (NSG: er) som fortfarande används och som är kopplade till klustret.  Ta bort dem och försök att ta bort dem igen.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Jag körde en uppgradering, men nu finns det poddar i krascher och det går inte att söka efter beredskap?

Kontrol lera att tjänstens huvud namn inte har upphört att gälla.  Se: [AKS-tjänstens huvud namn](./kubernetes-service-principal.md) och [AKS uppdatera autentiseringsuppgifter](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cannot-provision-loadbalancers-mount-pvcs-etc"></a>Mitt kluster fungerade men det går inte att etablera belastningsutjämnare, montera PVC: er osv. 

Kontrol lera att tjänstens huvud namn inte har upphört att gälla.  Se: [AKS-tjänstens huvud namn](./kubernetes-service-principal.md)  och [AKS uppdatera autentiseringsuppgifter](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Kan jag skala mitt AKS-kluster till noll?
Du kan helt [stoppa ett pågående AKS-kluster](start-stop-cluster.md)och Spara på respektive beräknings kostnader. Dessutom kan du också välja att [skala eller automatiskt skala alla eller vissa `User` resurspooler](scale-cluster.md#scale-user-node-pools-to-0) till 0, och endast underhålla den nödvändiga kluster konfigurationen.
Du kan inte skala [system-nodkonfigurationer](use-system-pools.md) direkt till 0.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Kan jag använda API: erna för skalnings uppsättningen för virtuella datorer för att skala manuellt?

Nej, skalnings åtgärder med hjälp av API: er för skalnings uppsättningen för virtuella datorer stöds inte. Använd AKS-API: er ( `az aks scale` ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Kan jag använda skalnings uppsättningar för virtuella datorer för att manuellt skala till 0 noder?

Nej, skalnings åtgärder med hjälp av API: er för skalnings uppsättningen för virtuella datorer stöds inte.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Kan jag stoppa eller ta bort alla mina virtuella datorer?

Även om AKS har återhämtnings metoder för att motstå sådan konfiguration och återställa från den, är detta inte en rekommenderad konfiguration.

## <a name="can-i-use-custom-vm-extensions"></a>Kan jag använda anpassade VM-tillägg?

Ingen AKS är en hanterad tjänst och manipulering av IaaS-resurser stöds inte. Så här installerar du anpassade komponenter osv. Använd Kubernetes-API: er och mekanismer. Du kan till exempel utnyttja DaemonSets för att installera nödvändiga komponenter.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Lagrar AKS kund information utanför klustrets region?

Funktionen för att aktivera lagring av kund information i en enda region är för närvarande bara tillgänglig i Sydostasien region (Singapore) för Asien och stillahavsområdet geo. För alla andra regioner lagras kund information på Geo.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
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
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
