---
title: Begrepp – Lagring i Azure Kubernetes Services (AKS)
description: Lär dig mer om lagring i Azure Kubernetes Service (AKS), inklusive volymer, beständiga volymer, lagringsklasser och anspråk
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 4bb19d7da971a82aef9c0e1fc092cc648ac49c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596002"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Lagringsalternativ för program i Azure Kubernetes Service (AKS)

Program som körs i Azure Kubernetes Service (AKS) kan behöva lagra och hämta data. För vissa programarbetsbelastningar kan den här datalagringen använda lokal, snabb lagring på noden som inte längre behövs när poddar tas bort. Andra programarbetsbelastningar kan kräva lagring som finns kvar på mer vanliga datavolymer inom Azure-plattformen. Flera poddar kan behöva dela samma datavolymer eller återansluta datavolymer om podden schemaläggs om på en annan nod. Slutligen kan du behöva injicera känslig data eller programkonfigurationsinformation i poddar.

![Lagringsalternativ för program i ett AKS-kluster (Azure Kubernetes Services)](media/concepts-storage/aks-storage-options.png)

Den här artikeln introducerar de grundläggande begreppen som ger lagring till dina program i AKS:

- [Volymer](#volumes)
- [Beständiga volymer](#persistent-volumes)
- [Lagringsklasser](#storage-classes)
- [Beständiga volymanspråk](#persistent-volume-claims)

## <a name="volumes"></a>Volymer

Program måste ofta kunna lagra och hämta data. Eftersom Kubernetes vanligtvis behandlar enskilda poddar som efemära, disponibla resurser, är olika metoder tillgängliga för program att använda och bevara data vid behov. En *volym* representerar ett sätt att lagra, hämta och bevara data över poddar och genom programmets livscykel.

Traditionella volymer för att lagra och hämta data skapas som Kubernetes-resurser som backas upp av Azure Storage. Du kan manuellt skapa dessa datavolymer som ska tilldelas poddar direkt, eller låta Kubernetes automatiskt skapa dem. Dessa datavolymer kan använda Azure Disks eller Azure Files:

- *Azure Disks* kan användas för att skapa en Kubernetes *DataDisk-resurs.* Diskar kan använda Azure Premium-lagring, som backas upp av högpresterande SSD-enheter eller Azure Standard-lagring, som backas upp av vanliga hårddiskar. Använd Premium-lagring för de flesta produktions- och utvecklingsarbetsbelastningar. Azure-diskar är monterade som *ReadWriteOnce*, så är endast tillgängliga för en enda pod. För lagringsvolymer som kan nås av flera poddar samtidigt använder du Azure-filer.
- *Azure-filer* kan användas för att montera en SMB 3.0-resurs som backas upp av ett Azure Storage-konto till poddar. Med filer kan du dela data mellan flera noder och poddar. Filer kan använda Azure Standard-lagring som backas upp av vanliga hårddiskar eller Azure Premium-lagring, som backas upp av högpresterande SSD-enheter.
> [!NOTE] 
> Azure Files stöder premiumlagring i AKS-kluster som kör Kubernetes 1.13 eller senare.

I Kubernetes kan volymer representera mer än bara en traditionell disk där information kan lagras och hämtas. Kubernetes volymer kan också användas som ett sätt att injicera data i en pod för användning av behållarna. Vanliga ytterligare volymtyper i Kubernetes är:

- *emptyDir* - Den här volymen används ofta som tillfälligt utrymme för en pod. Alla behållare i en pod kan komma åt data på volymen. Data som skrivs till den här volymtypen kvarstår endast under poddens livslängd - när podden tas bort tas volymen bort. Den här volymen använder vanligtvis den underliggande lokala noddisklagringen, men den kan också bara finnas i nodens minne.
- *hemlighet* - Den här volymen används för att injicera känsliga data i poddar, till exempel lösenord. Du skapar först en hemlighet med kubernetes-API:et. När du definierar din pod eller distribution kan en viss hemlighet begäras. Hemligheter tillhandahålls endast till noder som har en schemalagd pod som kräver det, och hemligheten lagras i *tmpfs*, inte skriven till disk. När den sista podden på en nod som kräver en hemlighet tas bort tas hemligheten bort från nodens tmpfs. Hemligheter lagras inom ett visst namnområde och kan endast nås av poddar inom samma namnområde.
- *configMap* - Den här volymtypen används för att injicera nyckelvärdesparegenskaper i poddar, till exempel information om programkonfiguration. I stället för att definiera programkonfigurationsinformation i en behållaravbildning kan du definiera den som en Kubernetes-resurs som enkelt kan uppdateras och tillämpas på nya instanser av poddar när de distribueras. Precis som att använda en hemlighet skapar du först en ConfigMap med Kubernetes API. Den här ConfigMap kan sedan begäras när du definierar en pod eller distribution. ConfigMaps lagras inom ett visst namnområde och kan endast nås av poddar inom samma namnområde.

## <a name="persistent-volumes"></a>Beständiga volymer

Volymer som definieras och skapas som en del av pod-livscykeln finns bara tills podden tas bort. Poddar förväntar sig ofta att deras lagring ska finnas kvar om en pod omplaneras på en annan värd under en underhållshändelse, särskilt i StatefulSets. En *beständig volym* (PV) är en lagringsresurs som skapats och hanteras av Kubernetes API som kan finnas längre än livstid för en enskild pod.

Azure-diskar eller filer används för att tillhandahålla PersistentVolume. Som anges i föregående avsnitt om volymer bestäms valet av diskar eller filer ofta av behovet av samtidig åtkomst till data eller prestandanivån.

![Beständiga volymer i ett AKS-kluster (Azure Kubernetes Services)](media/concepts-storage/persistent-volumes.png)

Ett PersistentVolume kan *skapas statiskt* av en klusteradministratör eller *skapas dynamiskt* av Kubernetes API-server. Om en pod är schemalagd och begär lagring som för närvarande inte är tillgänglig kan Kubernetes skapa den underliggande Azure Disk- eller Files-lagringen och bifoga den till podden. Dynamisk etablering använder en *StorageClass* för att identifiera vilken typ av Azure-lagring som behöver skapas.

## <a name="storage-classes"></a>Lagringsklasser

Om du vill definiera olika lagringsnivåer, till exempel Premium och Standard, kan du skapa en *StorageClass*. StorageClass definierar också *reclaimPolicy*. Den här reclaimPolicy styr beteendet för den underliggande Azure-lagringsresursen när podden tas bort och den beständiga volymen kanske inte längre krävs. Den underliggande lagringsresursen kan tas bort eller behållas för användning med en framtida pod.

I AKS skapas två första StorageClasses:

- *standard* - Använder Azure Standard-lagring för att skapa en hanterad disk. Återkravsprincipen anger att den underliggande Azure Disk tas bort när den beständiga volymen som använde den tas bort.
- hanterad premium – Använder Azure *Premium-lagring* för att skapa hanterad disk. Återkravsprincipen anger igen att den underliggande Azure Disk tas bort när den beständiga volymen som använde den tas bort.

Om ingen StorageClass har angetts för en beständig volym används standard StorageClass. Var försiktig när du begär beständiga volymer så att de använder lämplig lagring du behöver. Du kan skapa en StorageClass `kubectl`för ytterligare behov med . I följande exempel används Premium-hanterade diskar och anger att den underliggande Azure Disk ska *behållas* när podden tas bort:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

## <a name="persistent-volume-claims"></a>Beständiga volymanspråk

En PersistentVolumeClaim begär antingen disk- eller fillagring av en viss StorageClass, åtkomstläge och storlek. Kubernetes API-server kan dynamiskt etablera den underliggande lagringsresursen i Azure om det inte finns någon befintlig resurs för att uppfylla anspråket baserat på den definierade StorageClass. Pod-definitionen innehåller volymfästet när volymen har anslutits till podden.

![Beständiga volymanspråk i ett AKS-kluster (Azure Kubernetes Services)](media/concepts-storage/persistent-volume-claims.png)

Ett PersistentVolume är *bundet* till ett PersistentVolumeClaim när en tillgänglig lagringsresurs har tilldelats den pod som begär det. Det finns en 1:1-mappning av beständiga volymer till anspråk.

I följande exempel visar YAML-manifestet ett beständigt volymanspråk som använder den *hanterade premium-StorageClass och* begär en disk *5Gi* i storlek:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

När du skapar en pod-definition anges det beständiga volymanspråket för att begära önskad lagring. Du anger också *sedan volymenMount* för dina program att läsa och skriva data. Följande exempel YAML manifest visar hur den tidigare beständiga volymen anspråk kan användas för att montera en volym på */mnt/azure:*

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Nästa steg

Information om associerade metodtips finns [i Metodtips för lagring och säkerhetskopiering i AKS][operator-best-practices-storage].

Information om hur du skapar dynamiska och statiska volymer som använder Azure Disks eller Azure Files finns i följande instruktioner:

- [Skapa en statisk volym med Azure Diskar][aks-static-disks]
- [Skapa en statisk volym med Azure Files][aks-static-files]
- [Skapa en dynamisk volym med Azure Diskar][aks-dynamic-disks]
- [Skapa en dynamisk volym med Azure Files][aks-dynamic-files]

Mer information om kubernetes och AKS-huvudbegrepp finns i följande artiklar:

- [Kubernetes / AKS kluster och arbetsbelastningar][aks-concepts-clusters-workloads]
- [Kubernetes / AKS-identitet][aks-concepts-identity]
- [Kubernetes / AKS säkerhet][aks-concepts-security]
- [Kubernetes / AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes / AKS skala][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
