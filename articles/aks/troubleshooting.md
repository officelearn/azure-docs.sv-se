---
title: Felsök vanliga problem med Azure Kubernetes-tjänsten
description: Lär dig hur du felsöker och löser vanliga problem när du använder Azure Kubernetes service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 663a1dc597493c7b534b54eab7ccc4bed0ff0e11
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209230"
---
# <a name="aks-troubleshooting"></a>AKS-felsökning

När du skapar eller hanterar AKS-kluster (Azure Kubernetes service) kan du ibland stöta på problem. I den här artikeln beskrivs några vanliga problem och fel söknings steg.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>I allmänhet hittar jag information om fel sökning av Kubernetes-problem?

Testa den [officiella guiden för att felsöka Kubernetes-kluster](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Det finns också en [fel söknings guide](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)som publicerats av en Microsoft-tekniker för att felsöka poddar, noder, kluster och andra funktioner.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Jag får ett fel meddelande om att kvoten överskreds vid skapandet eller uppgraderingen. Vad ska jag göra? 

Du måste [begära kärnor](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Vad är den maximala inställningen för poddar per nod för AKS?

Den maximala inställningen för poddar per nod är 30 som standard om du distribuerar ett AKS-kluster i Azure Portal.
Den maximala inställningen för poddar per nod är 110 som standard om du distribuerar ett AKS-kluster i Azure CLI. (Kontrol lera att du använder den senaste versionen av Azure CLI). Den här standardinställningen kan ändras med hjälp av flaggan `–-max-pods` i `az aks create` kommandot.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Jag får ett insufficientSubnetSize-fel när jag distribuerar ett AKS-kluster med avancerade nätverksfunktioner. Vad ska jag göra?

Om Azure-CNI (avancerade nätverk) används allokerar AKS IP-adresser baserat på "Max-poddar" per nod som kon figurer ATS. Under näts storleken måste vara större än produkten för antalet noder och inställningen Max Pod per nod, baserat på den konfigurerade Max poddar per nod. Följande ekvation beskriver detta:

Under näts storlek > antalet noder i klustret (beakta framtida skalnings krav) * maximalt antal poddar per nod.

Mer information finns i [planera IP-adresser för klustret](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mitt Pod fastnar i CrashLoopBackOff-läge. Vad ska jag göra?

Det kan finnas olika orsaker till att Pod har fastnat i det läget. Du kan titta på:

* Själva pod, med hjälp av `kubectl describe pod <pod-name>`.
* Loggarna med `kubectl logs <pod-name>`.

Mer information om hur du felsöker Pod-problem finns i [Felsöka program](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Jag försöker aktivera RBAC i ett befintligt kluster. Hur kan jag göra det?

Det går tyvärr inte att aktivera rollbaserad åtkomst kontroll (RBAC) i befintliga kluster för tillfället. Du måste uttryckligen skapa nya kluster. Om du använder CLI är RBAC aktiverat som standard. Om du använder AKS-portalen är en växlings knapp för att aktivera RBAC tillgänglig i arbets flödet för skapande.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Jag skapade ett kluster med RBAC aktiverat genom att antingen använda Azure CLI med standardinställningar eller Azure Portal, och nu kan jag se många varningar på Kubernetes-instrumentpanelen. Instrument panelen som används för att fungera utan varningar. Vad ska jag göra?

Orsaken till varningarna på instrument panelen är att klustret nu är aktiverat med RBAC och till gång till det har inaktiverats som standard. I allmänhet är den här metoden en bra idé eftersom standard exponeringen för instrument panelen för alla användare av klustret kan leda till säkerhetshot. Om du fortfarande vill aktivera instrument panelen följer du stegen i [det här blogg inlägget](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Jag kan inte ansluta till instrument panelen. Vad ska jag göra?

Det enklaste sättet att komma åt tjänsten utanför klustret är att köra `kubectl proxy`, vilka proxyservrar som begär att skickas till din localhost port 8001 till Kubernetes-API-servern. Därifrån kan API-servern proxy till din tjänst: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Om du inte ser Kubernetes-instrumentpanelen kontrollerar du om `kube-proxy` Pod körs i namn området `kube-system`. Om den inte är i ett körnings tillstånd tar du bort Pod så att den startas om.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Jag kan inte hämta loggar med kubectl-loggar eller så kan jag inte ansluta till API-servern. Jag får "fel från servern: fel vid uppringning av Server del: slå TCP...". Vad ska jag göra?

Kontrol lera att standard nätverks säkerhets gruppen inte har ändrats och att både port 22 och 9000 är öppna för anslutning till API-servern. Kontrol lera om `tunnelfront` Pod körs i *Kube-systemets* namnrymd med kommandot `kubectl get pods --namespace kube-system`. Om den inte är det, kan du framtvinga borttagning av Pod och startas om.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Jag försöker uppgradera eller skala och får ett meddelande om att det inte är tillåtet att ändra egenskapen imageReference. Hur gör jag för att åtgärda det här problemet?

Du kan få det här felet eftersom du har ändrat taggarna i agent-noderna i AKS-klustret. Att ändra och ta bort taggar och andra egenskaper för resurser i resurs gruppen MC_ * kan leda till oväntade resultat. Att ändra resurserna under MC_ *-gruppen i AKS-klustret delar service nivå målet (service nivå mål).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Jag får fel meddelanden om att mitt kluster är i felaktigt tillstånd och uppgradering eller skalning fungerar inte förrän det har åtgärd ATS

*Den här fel söknings hjälpen riktas mot https://aka.ms/aks-cluster-failed*

Felet uppstår när kluster anger ett felaktigt tillstånd av flera orsaker. Följ stegen nedan för att lösa ett tillstånd för misslyckad kluster innan du försöker igen den tidigare misslyckade åtgärden:

1. `upgrade` och `scale` åtgärder kan inte utföras förrän klustret har `failed` tillstånd. Vanliga problem och lösningar för roten är:
    * Skalning med **otillräcklig beräknings kvot (CRP)** . För att lösa problemet måste du först skala klustret till ett stabilt mål tillstånd inom kvoten. Följ sedan de här [stegen för att begära en ökad beräknings kvot](../azure-portal/supportability/resource-manager-core-quotas-request.md) innan du försöker skala upp igen utöver de inledande kvot gränserna.
    * Skala ett kluster med avancerade nätverk och **otillräckliga undernät (nätverks resurser)** . För att lösa problemet måste du först skala klustret till ett stabilt mål tillstånd inom kvoten. Följ sedan [de här stegen för att begära en resurs kvot ökning](../azure-resource-manager/templates/error-resource-quota.md#solution) innan du försöker skala upp igen utöver de inledande kvot gränserna.
2. När den underliggande orsaken till uppgraderings felet har lösts bör klustret ha statusen klar. När en lyckad status har verifierats kan du försöka utföra den ursprungliga åtgärden igen.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Jag får fel meddelanden när jag försöker uppgradera eller skala det tillstånd mitt kluster håller på att uppgraderas eller har inte uppgraderats

*Den här fel söknings hjälpen riktas mot https://aka.ms/aks-pending-upgrade*

Att uppgradera och skala åtgärder på ett kluster med en enda Node-pool eller ett kluster med [flera noder](use-multiple-node-pools.md) , är ömsesidigt uteslutande. Det går inte att ha ett kluster eller en Node-pool samtidigt för uppgradering och skalning. I stället måste varje åtgärds typ slutföras på mål resursen innan nästa begäran om samma resurs. Det innebär att åtgärder begränsas när aktiva uppgraderingar eller skalnings åtgärder inträffar eller görs och senare Miss lyckas. 

För att diagnostisera problemet kan du köra `az aks show -g myResourceGroup -n myAKSCluster -o table` för att hämta detaljerad status för klustret. Baserat på resultatet:

* Om klustret aktivt uppgraderas väntar du tills åtgärden avslutas. Om det lyckades, gör om den tidigare misslyckade åtgärden igen.
* Om det inte går att uppgradera klustret följer du stegen som beskrivs i föregående avsnitt.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kan jag flytta mitt kluster till en annan prenumeration eller min prenumeration med mitt kluster till en ny klient?

Om du har flyttat AKS-klustret till en annan prenumeration eller klustrets ägande prenumeration till en ny klient, kommer klustret att förlora funktioner på grund av förlorade roll tilldelningar och tjänst huvud namns rättigheter. **AKS har inte stöd för att flytta kluster mellan prenumerationer eller klienter** på grund av den här begränsningen.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Jag får fel meddelanden vid försök att använda funktioner som kräver skalnings uppsättningar för virtuella datorer

*Den här fel söknings hjälpen dirigeras från aka.ms/aks-vmss-enablement*

Du kan få fel som indikerar att ditt AKS-kluster inte finns på en skal uppsättning för virtuella datorer, till exempel följande exempel:

**Agentpoolegenskap ' agentpoolegenskap ' har ställt in automatisk skalning som aktive rad men inte på Virtual Machine Scale Sets**

Om du vill använda funktioner som till exempel kluster autoskalning eller flera noder i pooler måste AKS-kluster skapas som använder skalnings uppsättningar för virtuella datorer. Fel returneras om du försöker använda funktioner som är beroende av den virtuella datorns skalnings uppsättningar och du riktar in ett vanligt AKS-kluster för skalnings uppsättningar som inte är virtuella datorer.

Följ stegen *innan du börjar* i rätt dokument för att skapa ett AKS-kluster på rätt sätt:

* [Använd kluster autoskalning](cluster-autoscaler.md)
* [Skapa och Använd flera noder i pooler](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Vilka namngivnings begränsningar tillämpas för AKS-resurser och parametrar?

*Den här fel söknings hjälpen dirigeras från aka.ms/aks-naming-rules*

Namngivnings begränsningar implementeras av både Azure-plattformen och AKS. Om ett resurs namn eller en parameter delar någon av dessa begränsningar returneras ett fel som uppmanar dig att ange en annan Indatatyp. Följande rikt linjer gäller för namngivning:

* Kluster namn måste innehålla 1-63 tecken. De enda tillåtna tecknen är bokstäver, siffror, bindestreck och under streck. Det första och sista tecknet måste vara en bokstav eller en siffra.
* AKS- *MC_* resurs grupp namn kombinerar resurs grupps namn och resurs namn. Den automatiskt genererade syntaxen för `MC_resourceGroupName_resourceName_AzureRegion` får inte vara större än 80 tecken. Om det behövs kan du minska längden på resurs gruppens namn eller AKS kluster namn.
* *DnsPrefix* måste börja och sluta med alfanumeriska värden och måste vara mellan 1-54 tecken. Giltiga tecken är alfanumeriska värden och bindestreck (-). *DnsPrefix* får inte innehålla specialtecken, till exempel en punkt (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Jag får fel meddelanden när jag försöker skapa, uppdatera, skala, ta bort eller uppgradera kluster, den åtgärden är inte tillåten eftersom en annan åtgärd pågår.

*Den här fel söknings hjälpen dirigeras från aka.ms/aks-pending-operation*

Kluster åtgärder är begränsade när en tidigare åtgärd fortfarande pågår. Om du vill hämta en detaljerad status för klustret använder du kommandot `az aks show -g myResourceGroup -n myAKSCluster -o table`. Använd din egen resurs grupp och AKS kluster namn efter behov.

Baserat på utdata från klustrets status:

* Om klustret är i ett annat etablerings tillstånd än *lyckat* eller *misslyckat*väntar du tills åtgärden (*Uppgradera/uppdatera/skapa/skala/ta bort/migrera*) avslutas. Försök att utföra den senaste kluster åtgärden igen när den tidigare åtgärden har slutförts.

* Om det finns en misslyckad uppgradering av klustret följer du stegen som beskrivs [i avsnittet Jag får fel meddelanden om att mitt kluster är i ett felaktigt tillstånd och uppgradering eller skalning fungerar inte förrän det har åtgärd ATS](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Jag får fel meddelanden om att det inte gick att hitta mitt huvud namn för tjänsten när jag försöker skapa ett nytt kluster utan att skicka något befintligt.

När du skapar ett AKS-kluster kräver det ett huvud namn för tjänsten för att skapa resurser för din räkning. AKS ger möjlighet att ha en ny som skapats när klustret skapas, men det kräver Azure Active Directory att fullständigt sprida det nya tjänst huvud namnet på en rimlig tid för att klustret ska kunna skapas. När den här spridningen tar för lång tid kommer klustret inte att verifieras för att skapa eftersom det inte går att hitta ett tillgängligt huvud namn för tjänsten. 

Använd följande lösningar för detta:
1. Använd ett befintligt huvud namn för tjänsten som redan har spridits över regioner och som finns för att skicka in till AKS vid klustrets skapande tid.
2. Om du använder Automation-skript kan du lägga till tids fördröjningar mellan skapande av tjänstens huvud namn och AKS-kluster.
3. Om du använder Azure Portal återgår du till kluster inställningarna när du skapar och försöker sedan att köra verifierings sidan igen efter några minuter.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Jag får fel meddelanden när jag har begränsat min utgående trafik

Vid begränsning av utgående trafik från ett AKS-kluster [krävs och valfria rekommenderade](limit-egress-traffic.md) utgående portar/nätverks regler och FQDN/applikations regler för AKS. Om inställningarna är i konflikt med någon av dessa regler kanske du inte kan köra vissa `kubectl`-kommandon. Du kan också se fel när du skapar ett AKS-kluster.

Kontrol lera att inställningarna inte står i konflikt med några av de obligatoriska eller valfria rekommenderade utgående portarna/nätverks reglerna och reglerna för FQDN/program.

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure Storage-och AKS-felsökning

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Vilka är de rekommenderade stabila versionerna av Kubernetes för Azure disk? 

| Kubernetes-version | Rekommenderad version |
| -- | :--: |
| 1,12 | 1.12.9 eller senare |
| 1,13 | 1.13.6 eller senare |
| 1,14 | 1.14.2 eller senare |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Vilka versioner av Kubernetes har Azure disk support på det suveräna molnet?

| Kubernetes-version | Rekommenderad version |
| -- | :--: |
| 1,12 | 1.12.0 eller senare |
| 1,13 | 1.13.0 eller senare |
| 1,14 | 1.14.0 eller senare |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach misslyckades för Azure-disken: parsning av "/dev/disk/Azure/SCSI1/lun1": ogiltig syntax

I Kubernetes version 1,10 kan MountVolume. WaitForAttach Miss lyckas med en ommontering av Azure-disken.

I Linux kan du se ett felaktigt format fel för DevicePath. Några exempel:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

I Windows kan du se fel numret för DevicePath (LUN). Några exempel:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Det här problemet har åtgärd ATS i följande versioner av Kubernetes:

| Kubernetes-version | Fast version |
| -- | :--: |
| 1,10 | 1.10.2 eller senare |
| 1,11 | 1.11.0 eller senare |
| 1,12 och senare | Saknas |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Det gick inte att ställa in UID och GID i mountOptions för Azure disk

Azure-disken använder ext4, xfs-filsystem som standard och mountOptions, till exempel UID = x, GID = x kan inte ställas in vid monterings tiden. Om du till exempel försökte ange mountOptions UID = 999, GID = 999, ser du ett fel som:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Du kan åtgärda problemet genom att göra något av följande:

* [Konfigurera säkerhets kontexten för en POD](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) genom att ange UID i runAsUser och GID i fsGroup. Följande inställning kommer till exempel att ange Pod kör som-rot, vilket gör den tillgänglig för alla filer:

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
  > Eftersom GID och UID monteras som rot eller 0 som standard. Om GID eller UID anges som icke-rot, till exempel 1000, kommer Kubernetes att använda `chown` för att ändra alla kataloger och filer under den disken. Den här åtgärden kan ta lång tid och kan göra det mycket långsamt att montera disken.

* Använd `chown` i initContainers för att ange GID och UID. Några exempel:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Fel vid borttagning av Azure-PersistentVolumeClaim som används av en POD

Om du försöker ta bort en Azure-PersistentVolumeClaim som används av en POD kan du se ett fel meddelande. Några exempel:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

I Kubernetes version 1,10 och senare finns en PersistentVolumeClaim skydds funktion som är aktive rad som standard för att förhindra det här felet. Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet kan du åtgärda problemet genom att ta bort Pod med hjälp av PersistentVolumeClaim innan du tar bort PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Fel "det går inte att hitta LUN för disk" vid koppling av en disk till en nod

När du kopplar en disk till en nod kan du se följande fel:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Det här problemet har åtgärd ATS i följande versioner av Kubernetes:

| Kubernetes-version | Fast version |
| -- | :--: |
| 1,10 | 1.10.10 eller senare |
| 1,11 | 1.11.5 eller senare |
| 1,12 | 1.12.3 eller senare |
| 1,13 | 1.13.0 eller senare |
| 1,14 och senare | Saknas |

Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet kan du åtgärda problemet genom att vänta några minuter och försöka igen.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Azure disk Attach/avtagbara fel, Mount-problem eller I/O-fel vid flera åtgärder för att koppla/koppla från

Från och med Kubernetes version 1.9.2 kan du se följande disk problem på grund av en felaktig VM-cache när du kör flera åtgärder för att ansluta/koppla från.

* Disk anslutning/från kopplings problem
* Disk-I/O-fel
* Oväntad disk koppling från virtuell dator
* En virtuell dator som körs i felaktigt tillstånd på grund av anslutning till en icke-befintlig disk

Det här problemet har åtgärd ATS i följande versioner av Kubernetes:

| Kubernetes-version | Fast version |
| -- | :--: |
| 1,10 | 1.10.12 eller senare |
| 1,11 | 1.11.6 eller senare |
| 1,12 | 1.12.4 eller senare |
| 1,13 | 1.13.0 eller senare |
| 1,14 och senare | Saknas |

Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet kan du åtgärda problemet genom att försöka nedan:

* Om en disk väntar på att kopplas från under en lång tids period kan du försöka koppla bort disken manuellt

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Azure-disk väntar på att frånkopplas under obestämd tid

I vissa fall, om en Azure disk-startåtgärd Miss lyckas vid det första försöket, kommer den inte att försöka koppla från igen och förblir kopplad till den ursprungliga virtuella noden. Det här felet kan inträffa när du flyttar en disk från en nod till en annan. Några exempel:

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

Det här problemet har åtgärd ATS i följande versioner av Kubernetes:

| Kubernetes-version | Fast version |
| -- | :--: |
| 1,11 | 1.11.9 eller senare |
| 1,12 | 1.12.7 eller senare |
| 1,13 | 1.13.4 eller senare |
| 1,14 och senare | Saknas |

Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet kan du åtgärda problemet genom att manuellt koppla från disken.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Det gick inte att koppla från Azure-disken till ett potentiellt problem med konkurrens villkoret och ogiltig data disk lista

När en Azure-disk inte kan kopplas tillbaka, kommer den att försöka igen om till sex gånger för att koppla bort disken från exponentiellt. Det kommer också att innehålla ett lås på radnivå på data disk listan i ungefär 3 minuter. Om disk listan uppdateras manuellt under den tids perioden, till exempel en manuell koppling eller från koppling, kommer detta att göra att disk listan hålls kvar av låset på nodens nivå för att bli föråldrad och orsaka instabilitet på den virtuella noden.

Det här problemet har åtgärd ATS i följande versioner av Kubernetes:

| Kubernetes-version | Fast version |
| -- | :--: |
| 1,12 | 1.12.9 eller senare |
| 1,13 | 1.13.6 eller senare |
| 1,14 | 1.14.2 eller senare |
| 1,15 och senare | Saknas |

Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet och din nod-VM har en lista över föråldrade diskar kan du åtgärda problemet genom att koppla bort alla icke-befintliga diskar från den virtuella datorn som en enda Mass åtgärd. **En separat från koppling av icke-befintliga diskar kan Miss lyckas.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Ett stort antal Azure-diskar orsakar långsam anslutning/från koppling

När antalet Azure-diskar som är anslutna till en virtuell nod är större än 10 kan åtgärderna för att ansluta och koppla från vara långsamma. Det här problemet är ett känt problem och det finns inga lösningar för tillfället.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Det gick inte att koppla från Azure-disken till en möjlig nods VM i felaktigt tillstånd

I vissa fall kan en Azure disk-från koppling delvis Miss lyckas och lämna noden VM i ett felaktigt tillstånd.

Det här problemet har åtgärd ATS i följande versioner av Kubernetes:

| Kubernetes-version | Fast version |
| -- | :--: |
| 1,12 | 1.12.10 eller senare |
| 1,13 | 1.13.8 eller senare |
| 1,14 | 1.14.4 eller senare |
| 1,15 och senare | Saknas |

Om du använder en version av Kubernetes som inte har korrigeringen för det här problemet och den virtuella noden är i ett felaktigt tillstånd kan du undvika problemet genom att manuellt uppdatera VM-statusen med hjälp av någon av följande:

* För ett tillgänglighets uppsättnings kluster:
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* För ett VMSS-baserat kluster:
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Files-och AKS-felsökning

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Vilka är de rekommenderade stabila versionerna av Kubernetes för Azure Files?
 
| Kubernetes-version | Rekommenderad version |
| -- | :--: |
| 1,12 | 1.12.6 eller senare |
| 1,13 | 1.13.4 eller senare |
| 1,14 | 1.14.0 eller senare |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Vilka versioner av Kubernetes har Azure Files stöd för det suveräna molnet?

| Kubernetes-version | Rekommenderad version |
| -- | :--: |
| 1,12 | 1.12.0 eller senare |
| 1,13 | 1.13.0 eller senare |
| 1,14 | 1.14.0 eller senare |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Vad är standard-mountOptions när du använder Azure Files?

Rekommenderade inställningar:

| Kubernetes-version | fileMode-och dirMode-värde|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 och senare | 0777 |

Om du använder ett kluster med Kuberetes version 1.8.5 eller större och dynamiskt skapar den permanenta volymen med en lagrings klass, kan monterings alternativ anges för objektet lagrings klass. I följande exempel anges *0777*:

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

Några ytterligare användbara *mountOptions* -inställningar:

* *mfsymlinks* kommer att göra Azure Files montering (CIFS) stöder symboliska länkar
* *nobrl* förhindrar sändning av byte intervall lås begär anden till servern. Den här inställningen är nödvändig för vissa program som slutar med en CIFS-format som är obligatoriska byte intervall lås. De flesta CIFS-servrar stöder ännu inte begäran om att låsa byte intervall lås. Om du inte använder *nobrl*kan program som slutar med CIFS-format som är obligatoriska byte intervall lås orsaka fel meddelanden som liknar:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Fel "Det gick inte att ändra behörigheter" vid användning av Azure Files

När du kör PostgreSQL på Azure Files-plugin-programmet kan du se ett fel som liknar följande:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Det här felet orsakas av Azure Files-plugin-programmet som använder CIFS/SMB-protokollet. När du använder CIFS/SMB-protokollet, kunde inte fil-och katalog behörigheter ändras efter montering.

Lös problemet genom att använda under *Sök väg* tillsammans med Azures disk-plugin-programmet. 

> [!NOTE] 
> För ext3/4-disk typen finns en förlorad + found katalog efter att disken formaterats.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure Files har hög latens jämfört med Azure disk vid hantering av många små filer

I vissa fall, t. ex. hantering av många små filer, kan du uppleva hög fördröjning när du använder Azure Files jämfört med Azure disk.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Fel vid aktivering av inställningen Tillåt åtkomst Tillåt åtkomst från valt nätverk på lagrings kontot

Om du aktiverar *Tillåt åtkomst från det valda nätverket* på ett lagrings konto som används för dynamisk etablering i AKS, visas ett fel meddelande när AKS skapar en fil resurs:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Det här felet beror på att Kubernetes *-persistentvolume* inte finns på nätverket som valdes när du ställer in *Tillåt åtkomst från det valda nätverket*.

Du kan åtgärda problemet genom att använda [statisk etablering med Azure Files](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure Files kan inte montera om i Windows Pod

Om en Windows-Pod med en Azure Files-montering tas bort och sedan schemaläggs för att återskapas på samma nod, kommer monteringen att Miss sen. Det här felet beror på att `New-SmbGlobalMapping` kommandot Miss lyckas eftersom Azure Files montering redan har monterats på noden.

Du kan till exempel se ett fel som liknar:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Det här problemet har åtgärd ATS i följande versioner av Kubernetes:

| Kubernetes-version | Fast version |
| -- | :--: |
| 1,12 | 1.12.6 eller senare |
| 1,13 | 1.13.4 eller senare |
| 1,14 och senare | Saknas |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Azure Files monteringen Miss lyckas på grund av att lagrings konto nyckeln har ändrats

Om din lagrings konto nyckel har ändrats kan du se Azure Files Mount-felen.

Du kan åtgärda problemet genom att manuellt uppdatera fältet *azurestorageaccountkey* manuellt i Azure File Secret med din base64-kodade lagrings konto nyckel.

Om du vill koda lagrings konto nyckeln i base64 kan du använda `base64`. Några exempel:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Använd `kubectl edit secret`om du vill uppdatera din Azure-hemlig fil. Några exempel:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Efter några minuter kommer agent-noden att försöka montera Azure-filen igen med den uppdaterade lagrings nyckeln.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Det gick inte att skala kluster autoskalning med felet kunde inte åtgärda grupp storlekarna

Om klustrets autoskalning inte skalas upp/ned och du ser ett fel som det nedan på [loggarna för klustrets automatiska skalnings loggar][view-master-logs].

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Det här felet beror på ett överordnat kluster för autoskalning i klustret där klustrets autoskalning slutar med ett annat värde än det som faktiskt finns i klustret. Ta bort det här läget genom att bara inaktivera och återaktivera [klustrets autoskalning][cluster-autoscaler].

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
