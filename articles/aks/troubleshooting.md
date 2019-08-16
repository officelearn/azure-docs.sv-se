---
title: Felsök vanliga problem med Azure Kubernetes-tjänsten
description: Lär dig hur du felsöker och löser vanliga problem när du använder Azure Kubernetes service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 00fadd8a98ec4f58783ed8b407e2621a7c107149
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533532"
---
# <a name="aks-troubleshooting"></a>AKS-felsökning

När du skapar eller hanterar AKS-kluster (Azure Kubernetes service) kan du ibland stöta på problem. I den här artikeln beskrivs några vanliga problem och fel söknings steg.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>I allmänhet hittar jag information om fel sökning av Kubernetes-problem?

Testa den [officiella guiden för att felsöka Kubernetes-kluster](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Det finns också en [fel söknings guide](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)som publicerats av en Microsoft-tekniker för att felsöka poddar, noder, kluster och andra funktioner.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Jag får ett fel meddelande om att kvoten överskreds vid skapandet eller uppgraderingen. Vad ska jag göra? 

Du måste [begära kärnor](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Vad är den maximala inställningen för poddar per nod för AKS?

Den maximala inställningen för poddar per nod är 30 som standard om du distribuerar ett AKS-kluster i Azure Portal.
Den maximala inställningen för poddar per nod är 110 som standard om du distribuerar ett AKS-kluster i Azure CLI. (Kontrol lera att du använder den senaste versionen av Azure CLI). Den här standardinställningen kan ändras med hjälp `–-max-pods` av flaggan `az aks create` i kommandot.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Jag får ett insufficientSubnetSize-fel när jag distribuerar ett AKS-kluster med avancerade nätverksfunktioner. Vad ska jag göra?

Om Azure CNI (avancerade nätverk) används, allokerar AKS IP-adresser baserat på "Max-poddar" per nod som kon figurer ATS. Antalet noder i ett AKS-kluster kan vara överallt mellan 1 och 110. Under näts storleken måste vara större än den konfigurerade Max poddar per nod än "produkten av antalet noder och Max Pod per nod". Följande grundläggande ekvation beskriver detta:

Under näts storlek > antalet noder i klustret (beakta framtida skalnings krav) * Max poddar per nod.

Mer information finns i [planera IP-adresser för klustret](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mitt Pod fastnar i CrashLoopBackOff-läge. Vad ska jag göra?

Det kan finnas olika orsaker till att Pod har fastnat i det läget. Du kan titta på:

* Själva pod, med hjälp `kubectl describe pod <pod-name>`av.
* Loggarna med hjälp `kubectl log <pod-name>`av.

Mer information om hur du felsöker Pod-problem finns i [Felsöka program](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Jag försöker aktivera RBAC i ett befintligt kluster. Hur kan jag göra det?

Det går tyvärr inte att aktivera rollbaserad åtkomst kontroll (RBAC) i befintliga kluster för tillfället. Du måste uttryckligen skapa nya kluster. Om du använder CLI är RBAC aktiverat som standard. Om du använder AKS-portalen är en växlings knapp för att aktivera RBAC tillgänglig i arbets flödet för skapande.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Jag skapade ett kluster med RBAC aktiverat genom att antingen använda Azure CLI med standardinställningar eller Azure Portal, och nu kan jag se många varningar på Kubernetes-instrumentpanelen. Instrument panelen som används för att fungera utan varningar. Vad ska jag göra?

Orsaken till varningarna på instrument panelen är att klustret nu är aktiverat med RBAC och till gång till det har inaktiverats som standard. I allmänhet är den här metoden en bra idé eftersom standard exponeringen för instrument panelen för alla användare av klustret kan leda till säkerhetshot. Om du fortfarande vill aktivera instrument panelen följer du stegen i [det här blogg inlägget](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Jag kan inte ansluta till instrument panelen. Vad ska jag göra?

Det enklaste sättet att komma åt din tjänst utanför klustret är att köra `kubectl proxy`, vilka proxyservrar som begär att skickas till din localhost port 8001 till Kubernetes-API-servern. Därifrån kan API-servern proxy till din tjänst: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Om du inte ser Kubernetes-instrumentpanelen kontrollerar du om `kube-proxy` Pod körs `kube-system` i namn området. Om den inte är i ett körnings tillstånd tar du bort Pod så att den startas om.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Jag kan inte hämta loggar med kubectl-loggar eller så kan jag inte ansluta till API-servern. Jag får "fel från servern: fel vid uppringning av Server del: slå TCP...". Vad ska jag göra?

Kontrol lera att standard nätverks säkerhets gruppen inte har ändrats och att både port 22 och 9000 är öppna för anslutning till API-servern. Kontrol lera om `tunnelfront` Pod körs i *Kube-systemets* namnrymd med `kubectl get pods --namespace kube-system` kommandot. Om den inte är det, kan du framtvinga borttagning av Pod och startas om.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Jag försöker uppgradera eller skala och får ett meddelande: Det går inte att ändra egenskapen "imageReference". Hur gör jag för att åtgärda det här problemet?

Du kan få det här felet eftersom du har ändrat taggarna i agent-noderna i AKS-klustret. Att ändra och ta bort taggar och andra egenskaper för resurser i resurs gruppen MC_ * kan leda till oväntade resultat. Om du ändrar resurserna under gruppen MC_ * i AKS-klustret bryts service nivå målet (service nivå målet).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Jag får fel meddelanden om att mitt kluster är i felaktigt tillstånd och uppgradering eller skalning fungerar inte förrän det har åtgärd ATS

*Den här fel söknings hjälpen riktas mot https://aka.ms/aks-cluster-failed*

Felet uppstår när kluster anger ett felaktigt tillstånd av flera orsaker. Följ stegen nedan för att lösa ett tillstånd för misslyckad kluster innan du försöker igen den tidigare misslyckade åtgärden:

1. Tills klustret är i ett `failed` `upgrade` tillstånd `scale` där det inte går att utföra åtgärder. Vanliga problem och lösningar för roten är:
    * Skalning med **otillräcklig beräknings kvot (CRP)** . För att lösa problemet måste du först skala klustret till ett stabilt mål tillstånd inom kvoten. Följ sedan de här [stegen för att begära en ökad beräknings kvot](../azure-supportability/resource-manager-core-quotas-request.md) innan du försöker skala upp igen utöver de inledande kvot gränserna.
    * Skala ett kluster med avancerade nätverk och **otillräckliga undernät (nätverks resurser)** . För att lösa problemet måste du först skala klustret till ett stabilt mål tillstånd inom kvoten. Följ sedan [de här stegen för att begära en resurs kvot ökning](../azure-resource-manager/resource-manager-quota-errors.md#solution) innan du försöker skala upp igen utöver de inledande kvot gränserna.
2. När den underliggande orsaken till uppgraderings felet har lösts bör klustret ha statusen klar. När en lyckad status har verifierats kan du försöka utföra den ursprungliga åtgärden igen.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Jag får fel meddelanden när jag försöker uppgradera eller skala det tillstånd mitt kluster håller på att uppgraderas eller har inte uppgraderats

*Den här fel söknings hjälpen riktas mot https://aka.ms/aks-pending-upgrade*

Att uppgradera och skala åtgärder på ett kluster med en enda Node-pool eller ett kluster med [flera noder](use-multiple-node-pools.md) , är ömsesidigt uteslutande. Det går inte att ha ett kluster eller en Node-pool samtidigt för uppgradering och skalning. I stället måste varje åtgärds typ slutföras på mål resursen innan nästa begäran om samma resurs. Det innebär att åtgärder begränsas när aktiva uppgraderingar eller skalnings åtgärder inträffar eller görs och senare Miss lyckas. 

För att diagnostisera problemet `az aks show -g myResourceGroup -n myAKSCluster -o table` kan du Hämta detaljerad status för klustret. Baserat på resultatet:

* Om klustret aktivt uppgraderas väntar du tills åtgärden avslutas. Om det lyckades, gör om den tidigare misslyckade åtgärden igen.
* Om det inte går att uppgradera klustret följer du stegen som beskrivs i föregående avsnitt.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kan jag flytta mitt kluster till en annan prenumeration eller min prenumeration med mitt kluster till en ny klient?

Om du har flyttat AKS-klustret till en annan prenumeration eller klustrets ägande prenumeration till en ny klient, kommer klustret att förlora funktioner på grund av förlorade roll tilldelningar och tjänst huvud namns rättigheter. **AKS har inte stöd för att flytta kluster mellan prenumerationer eller klienter** på grund av den här begränsningen.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Jag får fel meddelanden vid försök att använda funktioner som kräver skalnings uppsättningar för virtuella datorer

*Den här fel söknings hjälpen dirigeras från aka.ms/aks-vmss-enablement*

Du kan få fel som indikerar att ditt AKS-kluster inte finns på en skal uppsättning för virtuella datorer, till exempel följande exempel:

**Agentpoolegenskap ' agentpoolegenskap ' har ställt in automatisk skalning som aktive rad men inte på Virtual Machine Scale Sets**

Om du vill använda funktioner som till exempel kluster autoskalning eller flera noder i pooler måste AKS-kluster skapas som använder skalnings uppsättningar för virtuella datorer. Fel returneras om du försöker använda funktioner som är beroende av den virtuella datorns skalnings uppsättningar och du riktar in ett vanligt AKS-kluster för skalnings uppsättningar som inte är virtuella datorer. Stöd för virtuell dators skalnings uppsättning är för närvarande en för hands version i AKS.

Följ stegen *innan du börjar* med att registrera dig för för hands versionen av den virtuella datorns skalnings uppsättning och skapa ett AKS-kluster:

* [Använd kluster autoskalning](cluster-autoscaler.md)
* [Skapa och Använd flera noder i pooler](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Vilka namngivnings begränsningar tillämpas för AKS-resurser och parametrar?

*Den här fel söknings hjälpen dirigeras från aka.ms/aks-naming-rules*

Namngivnings begränsningar implementeras av både Azure-plattformen och AKS. Om ett resurs namn eller en parameter delar någon av dessa begränsningar returneras ett fel som uppmanar dig att ange en annan Indatatyp. Följande rikt linjer gäller för namngivning:

* AKS *MC_* resurs grupp namn kombinerar resurs grupps namn och resurs namn. Den automatiskt genererade syntaxen `MC_resourceGroupName_resourceName_AzureRegion` för får inte vara större än 80 tecken. Om det behövs kan du minska längden på resurs gruppens namn eller AKS kluster namn.
* *DnsPrefix* måste börja och sluta med alfanumeriska värden. Giltiga tecken är alfanumeriska värden och bindestreck (-). *DnsPrefix* får inte innehålla specialtecken, till exempel en punkt (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Jag får fel meddelanden när jag försöker skapa, uppdatera, skala, ta bort eller uppgradera kluster, den åtgärden är inte tillåten eftersom en annan åtgärd pågår.

*Den här fel söknings hjälpen dirigeras från aka.ms/aks-pending-operation*

Kluster åtgärder är begränsade när en tidigare åtgärd fortfarande pågår. Om du vill hämta en detaljerad status för klustret använder du `az aks show -g myResourceGroup -n myAKSCluster -o table` kommandot. Använd din egen resurs grupp och AKS kluster namn efter behov.

Baserat på utdata från klustrets status:

* Om klustret är i ett annat etablerings tillstånd än lyckat eller *misslyckat*väntar du tills åtgärden (*Uppgradera/uppdatera/skapa/skala/ta bort/migrera*) avslutas. Försök att utföra den senaste kluster åtgärden igen när den tidigare åtgärden har slutförts.

* Om det finns en misslyckad uppgradering av klustret följer du stegen som beskrivs [i avsnittet Jag får fel meddelanden om att mitt kluster är i ett felaktigt tillstånd och uppgradering eller skalning fungerar inte förrän det har åtgärd ATS](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).
