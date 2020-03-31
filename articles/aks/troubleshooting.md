---
title: Felsöka vanliga Problem med Azure Kubernetes-tjänsten
description: Lär dig hur du felsöker och löser vanliga problem när du använder Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 7bdabf2ec109fe96c28185bd1a2a680ce19c2650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368340"
---
# <a name="aks-troubleshooting"></a>AKS-felsökning

När du skapar eller hanterar AKS-kluster (Azure Kubernetes Service) kan du ibland stöta på problem. I den här artikeln beskrivs några vanliga problem och felsökningssteg.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>I allmänhet, var hittar jag information om felsökning Kubernetes problem?

Prova den [officiella guiden för felsökning av Kubernetes-kluster](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Det finns också en [felsökningsguide](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)som publiceras av en Microsoft-tekniker för felsökning av poddar, noder, kluster och andra funktioner.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Jag får ett "kvot överskridet" fel under skapandet eller uppgraderingen. Vad ska jag göra? 

Du måste [begära kärnor](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Vilken är den maximala inställningen för poddar per nod för AKS?

Den maximala pods-per-nod-inställningen är 30 som standard om du distribuerar ett AKS-kluster i Azure-portalen.
Den maximala pods-per-nod-inställningen är 110 som standard om du distribuerar ett AKS-kluster i Azure CLI. (Kontrollera att du använder den senaste versionen av Azure CLI). Den här standardinställningen `–-max-pods` kan ändras `az aks create` med hjälp av flaggan i kommandot.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Jag får ett otillräckligtUbnetSize-fel när jag distribuerar ett AKS-kluster med avancerade nätverk. Vad ska jag göra?

Om Azure CNI (avancerade nätverk) används allokerar AKS IP-adresser baserat på "max-pods" per nod konfigurerad. Baserat på de konfigurerade max pods per nod måste undernätsstorleken vara större än produkten av antalet noder och max pod per nodinställning. Följande ekvation beskriver detta:

Undernätsstorlek > antal noder i klustret (med hänsyn till de framtida skalningskraven) * max poddar per noduppsättning.

Mer information finns i [Planera IP-adressering för klustret](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Min pod har fastnat i CrashLoopBackOff-läge. Vad ska jag göra?

Det kan finnas olika orsaker till att podden sitter fast i det läget. Du kan titta på:

* Själva kapseln, `kubectl describe pod <pod-name>`genom att använda .
* Loggarna, med `kubectl logs <pod-name>`hjälp av .

Mer information om hur du felsöker podsproblem finns i [Felsökningsprogram](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Jag försöker aktivera RBAC i ett befintligt kluster. Hur kan jag göra det?

Tyvärr stöds inte att aktivera rollbaserad åtkomstkontroll (RBAC) på befintliga kluster just nu. Du måste uttryckligen skapa nya kluster. Om du använder CLI är RBAC aktiverat som standard. Om du använder AKS-portalen är en växlingsknapp för att aktivera RBAC tillgänglig i arbetsflödet för att skapa.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Jag har skapat ett kluster med RBAC aktiverat med hjälp av antingen Azure CLI med standardvärden eller Azure-portalen, och nu ser jag många varningar på Kubernetes-instrumentpanelen. Instrumentpanelen som används för att fungera utan några varningar. Vad ska jag göra?

Anledningen till varningarna på instrumentpanelen är att klustret nu är aktiverat med RBAC och åtkomst till det har inaktiverats som standard. I allmänhet är den här metoden god praxis eftersom standardexponeringen för instrumentpanelen för alla användare av klustret kan leda till säkerhetshot. Om du fortfarande vill aktivera instrumentpanelen följer du stegen i [det här blogginlägget](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Det går inte att ansluta till instrumentpanelen. Vad ska jag göra?

Det enklaste sättet att komma åt `kubectl proxy`din tjänst utanför klustret är att köra , som proxyservrar skickas till din localhost port 8001 till Kubernetes API-server. Därifrån kan API-servern proxy till `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`din tjänst: .

Om du inte ser kubernetes instrumentpanelen `kube-proxy` kontrollerar du `kube-system` om podden körs i namnområdet. Om den inte är i körläge tar du bort podden så startas den om.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Jag kan inte hämta loggar med kubectl loggar eller jag kan inte ansluta till API-servern. Jag får "Fel från servern: fel att ringa backend: ring tcp ...". Vad ska jag göra?

Kontrollera att standardnätverkssäkerhetsgruppen inte ändras och att både port 22 och 9000 är öppna för anslutning till API-servern. Kontrollera om `tunnelfront` podden körs i *namnområdet kube-system* med `kubectl get pods --namespace kube-system` kommandot. Om den inte är det, tvinga borttagning av pod och det kommer att starta om.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Jag försöker uppgradera eller skala och jag får ett "meddelande: Ändra egenskap 'imageReference' är inte tillåtet" fel. Hur åtgärdar jag problemet?

Du kanske får det här felet eftersom du har ändrat taggarna i agentnoderna i AKS-klustret. Om du ändrar och tar bort taggar och andra egenskaper för resurser i resursgruppen MC_* kan det leda till oväntade resultat. Om du ändrar resurserna under gruppen MC_* i AKS-klustret bryts målnivån på servicenivå (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Jag får fel som klustret är i feltillstånd och uppgradering eller skalning fungerar inte förrän det är åtgärdat

*Den här felsökningshjälpen dirigeras frånhttps://aka.ms/aks-cluster-failed*

Det här felet uppstår när kluster går in i ett misslyckat tillstånd av flera skäl. Följ stegen nedan för att lösa ditt kluster misslyckades innan du försökte den tidigare misslyckade åtgärden:

1. Tills klustret `failed` är `upgrade` `scale` ur läge och åtgärder inte lyckas. Vanliga grundläggande problem och lösningar är:
    * Skalning med **otillräcklig beräkningskvot (CRP).** Lös genom att först skala tillbaka klustret till ett stabilt målläge inom kvoten. Följ sedan dessa [steg för att begära en ökning av beräkningskvoten](../azure-portal/supportability/resource-manager-core-quotas-request.md) innan du försöker skala upp igen utöver de ursprungliga kvotgränserna.
    * Skala ett kluster med avancerade nätverk och **otillräckliga undernätsresurser (nätverk).** Lös genom att först skala tillbaka klustret till ett stabilt målläge inom kvoten. Följ sedan [dessa steg för att begära en resurskvotökning](../azure-resource-manager/templates/error-resource-quota.md#solution) innan du försöker skala upp igen utöver de ursprungliga kvotgränserna.
2. När den underliggande orsaken till uppgraderingsfel har lösts bör klustret vara i ett efterföljande tillstånd. När ett efterföljande tillstånd har verifierats försöker du igen den ursprungliga åtgärden.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Jag får fel när jag försöker uppgradera eller skala det tillståndet som mitt kluster för närvarande uppgraderas eller har misslyckats med uppgraderingen

*Den här felsökningshjälpen dirigeras frånhttps://aka.ms/aks-pending-upgrade*

Uppgraderings- och skalningsåtgärder på ett kluster med en enda nodpool eller ett kluster med [flera nodpooler](use-multiple-node-pools.md) utesluter varandra. Du kan inte ha en kluster- eller nodpool samtidigt uppgradera och skala. I stället måste varje åtgärdstyp slutföras på målresursen före nästa begäran på samma resurs. Därför begränsas åtgärderna när aktiva uppgraderings- eller skalningsåtgärder inträffar eller försök görs och misslyckades därefter. 

För att diagnostisera `az aks show -g myResourceGroup -n myAKSCluster -o table` problemet köras för att hämta detaljerad status i klustret. Baserat på resultatet:

* Om klustret aktivt uppgraderar väntar du tills åtgärden avslutas. Om det lyckades, försök igen den tidigare misslyckade åtgärden igen.
* Om klustret har misslyckats med uppgraderingen följer du stegen i föregående avsnitt.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kan jag flytta mitt kluster till en annan prenumeration eller min prenumeration med mitt kluster till en ny klient?

Om du har flyttat AKS-klustret till en annan prenumeration eller kluster som äger prenumerationen på en ny klient, förlorar klustret funktioner på grund av att rolltilldelningar och tjänsthuvudnamn har förlorats. **AKS stöder inte att kluster flyttas över prenumerationer eller klienter** på grund av det här villkoret.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Jag får fel när jag försöker använda funktioner som kräver skaluppsättningar för virtuella datorer

*Den här felsökningshjälpen dirigeras från aka.ms/aks-vmss-enablement*

Du kan få felmeddelanden som indikerar att AKS-klustret inte finns med i en skalningsuppsättning för virtuella datorer, till exempel följande exempel:

**AgentPool 'agentpool' har angett automatisk skalning som aktiverat men finns inte på skalningsuppsättningar för virtuella datorer**

Om du vill använda funktioner som klusterutskalningshanteraren eller flera nodpooler måste AKS-kluster skapas som använder skaluppsättningar för virtuella datorer. Fel returneras om du försöker använda funktioner som är beroende av skaluppsättningar för virtuella datorer och du inriktar dig på ett vanligt, icke-virtuellt datorskaleuppsättning AKS-kluster.

Följ stegen *Innan du börjar* i rätt dokument för att skapa ett AKS-kluster på rätt sätt:

* [Använda kluster automatisk skalning](cluster-autoscaler.md)
* [Skapa och använda flera nodpooler](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Vilka namngivningsbegränsningar tillämpas för AKS-resurser och parametrar?

*Den här felsökningshjälpen dirigeras från aka.ms/aks-naming-rules*

Namngivningsbegränsningar implementeras av både Azure-plattformen och AKS. Om ett resursnamn eller en parameter bryter mot någon av dessa begränsningar returneras ett fel som ber dig ange en annan indata. Följande gemensamma namngivningsriktlinjer gäller:

* Klusternamn måste vara 1-63 tecken. De enda tillåtna tecknen är bokstäver, siffror, streck och understreck. Det första och sista tecknet måste vara en bokstav eller ett tal.
* *Aks-resursgruppnamnet MC_* kombinerar resursgruppsnamn och resursnamn. Den automatiskt genererade `MC_resourceGroupName_resourceName_AzureRegion` syntaxen får inte vara större än 80 tecken. Om det behövs kan du minska längden på resursgruppsnamnet eller AKS-klusternamnet.
* *DnsPrefixet* måste börja och sluta med alfanumeriska värden och måste vara mellan 1-54 tecken. Giltiga tecken innehåller alfanumeriska värden och bindestreck (-). *DnsPrefixet* kan inte innehålla specialtecken, till exempel en punkt (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Jag får fel när jag försöker skapa, uppdatera, skala, ta bort eller uppgradera kluster, den åtgärden är inte tillåten eftersom en annan åtgärd pågår.

*Den här felsökningshjälpen dirigeras från aka.ms/aks-pending-operation*

Klusteråtgärder är begränsade när en tidigare åtgärd fortfarande pågår. Om du vill hämta en detaljerad `az aks show -g myResourceGroup -n myAKSCluster -o table` status för klustret använder du kommandot. Använd din egen resursgrupp och AKS-klusternamn efter behov.

Baserat på utdata från klusterstatus:

* Om klustret är i något *etableringstillstånd* annat än lyckades eller *misslyckades,* vänta tills åtgärden *(Uppgradering / Uppdatering / Skapande / Skalning / Borttagning / Migrering)* avslutas. När den föregående åtgärden har slutförts, försök igen den senaste klusteråtgärden.

* Om klustret har en misslyckad uppgradering följer du stegen som [beskrivs jag får fel som klustret är i feltillstånd och uppgradering eller skalning fungerar inte förrän det är åtgärdat](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Jag får fel som min tjänst huvudnamn inte hittades när jag försöker skapa ett nytt kluster utan att passera i en befintlig.

När du skapar ett AKS-kluster krävs ett tjänsthuvudnamn för att skapa resurser för din räkning. AKS erbjuder möjligheten att ha en ny som skapats vid klusterskapande tid, men detta kräver Azure Active Directory för att helt sprida den nya tjänsten huvudnamn i rimlig tid för att få klustret lyckas skapa. När spridningen tar för lång tid misslyckas klustret att skapa eftersom det inte går att hitta ett tillgängligt tjänsthuvudnamn för att göra det. 

Använd följande lösningar för detta:
1. Använd ett befintligt tjänsthuvudnamn som redan har spridits över regioner och finns för att gå vidare till AKS vid klusterskapande tid.
2. Om du använder automatiseringsskript lägger du till tidsfördröjningar mellan att skapa tjänstens huvudnamn och att skapa AKS-kluster.
3. Om du använder Azure-portalen går du tillbaka till klusterinställningarna under att skapa och försöka igen verifieringssidan efter några minuter.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Jag får fel efter att ha begränsat min utgående trafik

När du begränsar utgående trafik från ett AKS-kluster krävs [och rekommenderas för rekommenderade utgående](limit-egress-traffic.md) portar/nätverksregler och FQDN/-programregler för AKS. Om dina inställningar står i konflikt med någon av dessa `kubectl` regler kanske du inte kan köra vissa kommandon. Du kan också se fel när du skapar ett AKS-kluster.

Kontrollera att dina inställningar inte står i konflikt med någon av de nödvändiga eller valfria rekommenderade utgående portar / nätverksregler och FQDN / programregler.

## <a name="azure-storage-and-aks-troubleshooting"></a>Felsökning av Azure Storage och AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Vilka är de rekommenderade stabila versionerna av Kubernetes för Azure-disk? 

| Kubernetes version | Rekommenderad version |
| -- | :--: |
| 1.12 | 1.12.9 eller senare |
| 1.13 | 1.13.6 eller senare |
| 1.14 | 1.14.2 eller senare |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Vilka versioner av Kubernetes har Azure Disk-stöd på Sovereign Cloud?

| Kubernetes version | Rekommenderad version |
| -- | :--: |
| 1.12 | 1.12.0 eller senare |
| 1.13 | 1.13.0 eller senare |
| 1.14 | 1.14.0 eller senare |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach misslyckades för Azure Disk: parsning "/dev/disk/azure/scsi1/lun1": ogiltig syntax

I Kubernetes version 1.10 kan MountVolume.WaitForAttach misslyckas med en Azure Disk-återmontering.

På Linux kan ett felaktigt DevicePath-formatfel visas. Ett exempel:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

I Windows kan ett fel Enhetsfel(LUN) visas. Ett exempel:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Det här problemet har åtgärdats i följande versioner av Kubernetes:

| Kubernetes version | Fast version |
| -- | :--: |
| 1,10 | 1.10.2 eller senare |
| 1.11 | 1.11.0 eller senare |
| 1.12 och senare | Ej tillämpligt |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Fel vid inställning av uid och gid i mountOptions för Azure Disk

Azure Disk använder filsystemet ext4,xfs som standard och mountOptions som uid=x,gid=x kan inte ställas in vid monteringstid. Till exempel om du försökte ställa in mountOptions uid = 999, gid = 999, skulle se ett fel som:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Du kan minska problemet genom att göra något av följande:

* [Konfigurera säkerhetskontexten för en pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) genom att ange uid i runAsUser och gid i fsGroup. Följande inställning anger till exempel podkörning som rot, vilket gör den tillgänglig för alla filer:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Eftersom gid och uid är monterade som rot eller 0 som standard. Om gid eller uid anges som icke-rot, till exempel 1000, kommer Kubernetes att använda `chown` för att ändra alla kataloger och filer under den disken. Denna operation kan vara tidskrävande och kan göra monteringen av disken mycket långsam.

* Använd `chown` i initContainers för att ställa in gid och uid. Ett exempel:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Fel vid borttagning av Azure Disk PersistentVolumeClaim som används av en pod

Om du försöker ta bort en Azure Disk PersistentVolumeClaim som används av en pod, kan du se ett fel. Ett exempel:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

I Kubernetes version 1.10 och senare finns det en BeständigVolumeClaim-skyddsfunktion aktiverad som standard för att förhindra det här felet. Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet kan du minska problemet genom att ta bort podden med PersistentVolumeClaim innan du tar bort PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Fel "Det går inte att hitta Lun för disk" när en disk ansluts till en nod

När du ansluter en disk till en nod kan följande felmeddelande visas:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Det här problemet har åtgärdats i följande versioner av Kubernetes:

| Kubernetes version | Fast version |
| -- | :--: |
| 1,10 | 1.10.10 eller senare |
| 1.11 | 1.11.5 eller senare |
| 1.12 | 1.12.3 eller senare |
| 1.13 | 1.13.0 eller senare |
| 1.14 och senare | Ej tillämpligt |

Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet kan du minska problemet genom att vänta flera minuter och försöka igen.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Azure Disk-anslutning/koppla från fel, montera problem eller I/O-fel under flera bifoga/koppla från åtgärder

Från och med Kubernetes version 1.9.2, när du kör flera koppla/koppla bort åtgärder parallellt, kan följande diskproblem visas på grund av en smutsig VM-cache:

* Diskansatta/koppla bort fel
* Disk-I/O-fel
* Oväntad diskfrånlagring från virtuell dator
* Den virtuella datorn som körs i feltillstånd på grund av att den icke-befintliga disken har bifogats

Det här problemet har åtgärdats i följande versioner av Kubernetes:

| Kubernetes version | Fast version |
| -- | :--: |
| 1,10 | 1.10.12 eller senare |
| 1.11 | 1.11.6 eller senare |
| 1.12 | 1.12.4 eller senare |
| 1.13 | 1.13.0 eller senare |
| 1.14 och senare | Ej tillämpligt |

Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet kan du minska problemet genom att prova nedanstående:

* Om det finns en disk väntar på att kopplas bort under en lång tid, prova att koppla bort disken manuellt

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Azure Disk väntar på att kopplas bort på obestämd tid

I vissa fall, om en Azure Disk-fristående åtgärd misslyckas vid det första försöket, kommer den inte att försöka koppla från åtgärden och förblir kopplad till den ursprungliga noden VM. Det här felet kan uppstå när du flyttar en disk från en nod till en annan. Ett exempel:

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

Det här problemet har åtgärdats i följande versioner av Kubernetes:

| Kubernetes version | Fast version |
| -- | :--: |
| 1.11 | 1.11.9 eller senare |
| 1.12 | 1.12.7 eller senare |
| 1.13 | 1.13.4 eller senare |
| 1.14 och senare | Ej tillämpligt |

Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet kan du minska problemet genom att koppla bort disken manuellt.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Fel på Azure Disk-frånskilja vilket leder till potentiella problem med rastillstånd och ogiltig datadisklista

När en Azure-disk inte kan tas bort försöker den igen upp till sex gånger för att koppla från disken med exponentiell backa. Det kommer också att innehålla en nod-nivå lås på datadisklistan i ca 3 minuter. Om disklistan uppdateras manuellt under den tidsperioden, till exempel en manuell bifoga eller koppla bort åtgärden, kommer detta att orsaka att disklistan som innehas av nodnivålåset är föråldrad och orsakar instabilitet på noddas.If the disk list is updated manually during that period of time, such as a manual attach or lossach operation, this will cause the disk list held by the node-level lock to be obsolete and cause instability on the node VM.

Det här problemet har åtgärdats i följande versioner av Kubernetes:

| Kubernetes version | Fast version |
| -- | :--: |
| 1.12 | 1.12.9 eller senare |
| 1.13 | 1.13.6 eller senare |
| 1.14 | 1.14.2 eller senare |
| 1.15 och senare | Ej tillämpligt |

Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet och den virtuella noden har en föråldrad disklista, kan du minska problemet genom att koppla bort alla icke-befintliga diskar från den virtuella datorn som en enda, massåtgärd. **Det kan misslyckas med att koppla från icke-befintliga diskar individuellt.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Stort antal Azure-diskar orsakar långsam bifoga/koppla från

När antalet Azure-diskar som är kopplade till en virtuell nod är större än 10 kan det vara långsamt att koppla och koppla från åtgärder. Det här problemet är ett känt problem och det finns inga lösningar just nu.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Azure Disk-bortkopplat fel som leder till potentiell virtuell virtuell nod i feltillstånd

I vissa kantfall kan en Azure Disk-ta bort delvis misslyckas och lämna noden VM i ett misslyckat tillstånd.

Det här problemet har åtgärdats i följande versioner av Kubernetes:

| Kubernetes version | Fast version |
| -- | :--: |
| 1.12 | 1.12.10 eller senare |
| 1.13 | 1.13.8 eller senare |
| 1.14 | 1.14.4 eller senare |
| 1.15 och senare | Ej tillämpligt |

Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet och noden VM är i ett misslyckat tillstånd, kan du minska problemet genom att manuellt uppdatera VM-status med hjälp av en av nedanstående:

* För ett setbaserat kluster för tillgänglighet:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* För ett VMSS-baserat kluster:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Felsökning av Azure-filer och AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Vilka är de rekommenderade stabila versionerna av Kubernetes för Azure-filer?
 
| Kubernetes version | Rekommenderad version |
| -- | :--: |
| 1.12 | 1.12.6 eller senare |
| 1.13 | 1.13.4 eller senare |
| 1.14 | 1.14.0 eller senare |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Vilka versioner av Kubernetes har Azure Files-stöd på Sovereign Cloud?

| Kubernetes version | Rekommenderad version |
| -- | :--: |
| 1.12 | 1.12.0 eller senare |
| 1.13 | 1.13.0 eller senare |
| 1.14 | 1.14.0 eller senare |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Vilka är standardfästealternativen när du använder Azure Files?

Rekommenderade inställningar:

| Kubernetes version | fileMode och dirMode värde|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 och senare | 0777 |

Om du använder ett kluster med Kubernetes version 1.8.5 eller senare och dynamiskt skapar den beständiga volymen med en lagringsklass, kan monteringsalternativ anges på lagringsklassobjektet. Följande exempel anger *0777:*

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Några ytterligare användbara *mountOptions-inställningar:*

* *mfsymlinks* kommer att göra Azure Files mount (cifs) stöd symboliska länkar
* *nobrl* förhindrar att byteintervallslåsbegäranden skickas till servern. Den här inställningen är nödvändig för vissa program som bryter med cifs-stil obligatoriska byte intervall lås. De flesta cifs-servrar har ännu inte stöd för att begära rådgivande byteintervalllås. Om du inte använder *nobrl*kan program som bryter med obligatoriska bytesintervalllås orsaka felmeddelanden som liknar:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Fel "kunde inte ändra behörigheter" när Du använder Azure Files

När du kör PostgreSQL på Azure Files plugin kan du se ett fel som liknar:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Det här felet orsakas av Azure Files-insticksprogrammet med cifs/SMB-protokollet. När cifs/SMB-protokollet används kunde fil- och katalogbehörigheterna inte ändras efter monteringen.

LÃ¶s problemet genom att *anvÃ¤nde subPath* tillsammans med azure disk-insticksprogrammet. 

> [!NOTE] 
> För ext3/4 disktyp finns det en försvunnen+hittade katalog efter att disken har formaterats.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure Files har hög latens jämfört med Azure Disk vid hantering av många små filer

I vissa fall, till exempel hantering av många små filer, kan du uppleva hög latens när du använder Azure-filer jämfört med Azure Disk.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Fel vid aktivering av inställningen Tillåt "Tillåt åtkomst tillåt åtkomst från valt nätverk" på lagringskontot

Om du aktiverar *tillåt åtkomst från det valda nätverket* på ett lagringskonto som används för dynamisk etablering i AKS får du ett felmeddelande när AKS skapar en filresurs:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Det här felet beror på att Kubernetes *persistentvolume-controller* inte finns i det nätverk som valts när inställningen *tillåter åtkomst från det valda nätverket*.

Du kan minska problemet genom att använda [statisk etablering med Azure Files](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure-filer kan inte återmonteras i Windows-pod

Om en Windows-pod med en Azure Files-montering tas bort och sedan schemaläggs för att återskapas på samma nod, misslyckas fästet. Det här felet `New-SmbGlobalMapping` beror på att kommandot inte fungerar eftersom Azure Files-fästet redan är monterat på noden.

Du kan till exempel se ett fel som liknar:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Det här problemet har åtgärdats i följande versioner av Kubernetes:

| Kubernetes version | Fast version |
| -- | :--: |
| 1.12 | 1.12.6 eller senare |
| 1.13 | 1.13.4 eller senare |
| 1.14 och senare | Ej tillämpligt |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Azure Files-monteringen misslyckas på grund av att lagringskontonyckeln har ändrats

Om nyckeln för lagringskonto har ändrats kan du se Azure Files montera fel.

Du kan minska problemet genom att manuellt uppdatera fältet *azurestorageaccountkey* manuellt i Azure-filhemlighet med din base64-kodade lagringskontonyckel.

Om du vill koda lagringskontonyckeln `base64`i base64 kan du använda . Ett exempel:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Om du vill uppdatera `kubectl edit secret`din hemliga Azure-fil använder du . Ett exempel:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Efter några minuter försöker agentnoden igen azure-filmonteringen med den uppdaterade lagringsnyckeln.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Automatisk skalning av kluster kan inte skalas med fel som inte kunde åtgärda nodgruppstorlekar

Om klustrets automatiska skalning inte skalas upp/ned och du ser ett fel som nedan på [klustrets automatiska skalbara loggar][view-master-logs].

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Det här felet beror på ett konkurrensvillkor för automatisk skalning av uppströms kluster där klusteranvändaren slutar med ett annat värde än det som faktiskt finns i klustret. Om du vill komma ur det här tillståndet inaktiverar och aktiverar du [automatiskt kluster.][cluster-autoscaler]

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Långsam diskbilaga, GetAzureDiskLun tar 10 till 15 minuter och du får ett felmeddelande

I Kubernetes-versioner **som är äldre än 1.15.0** kan ett felmeddelande visas som Fel **waitForAttach kan inte hitta Lun för disk**.  Lösningen för detta är att vänta cirka 15 minuter och försöka igen.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
