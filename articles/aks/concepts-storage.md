---
title: Begrepp - lagring i Azure Kubernetes-tjänster (AKS)
description: Lär dig mer om lagring i Azure Kubernetes Service (AKS), inklusive volymer, beständiga volymer, lagringsklasser och anspråk
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: fd301967800f67d95c12f1689981b2dfd8eb2d80
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56452771"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Lagringsalternativ för program i Azure Kubernetes Service (AKS)

Program som körs i Azure Kubernetes Service (AKS) kan behöva lagra och hämta data. För vissa arbetsbelastningar för program, kan den här datalagring använda lokal, snabb lagring på den nod som inte längre behövs när poddarna har tagits bort. Andra arbetsbelastningar för program kan kräva lagring som kvarstår i jämnare data volymer i Azure-plattformen. Flera poddar kan behöva dela samma datavolymer eller återansluta datavolymer om schemaläggs en pod på en annan nod. Slutligen kan du behöva mata in känsliga data eller program konfigurationsinformation i poddar.

![Lagringsalternativ för program i ett kluster i Azure Kubernetes Services (AKS)](media/concepts-storage/aks-storage-options.png)

Den här artikeln innehåller grundläggande begrepp som tillhandahåller lagring för dina program i AKS:

- [Volymer](#volumes)
- [Beständiga volymer](#persistent-volumes)
- [Storage-klasser](#storage-classes)
- [Beständig volym anspråk](#persistent-volume-claims)

## <a name="volumes"></a>Volymer

Program behöver ofta för att kunna lagra och hämta data. Som Kubernetes behandlar vanligtvis enskilda poddar som tillfälliga, disponibla resurser, finns olika metoder för program att använda och bevara data efter behov. En *volym* representerar ett sätt att lagra, hämta och bevara data över poddar och genom programmets livscykel.

Traditionella volymer för att lagra och hämta data skapas som Kubernetes-resurser som backas upp av Azure Storage. Du kan manuellt skapa datavolymerna som ska tilldelas till poddar direkt eller ha Kubernetes skapa dem automatiskt. Dessa datavolymer kan använda Azure-diskar eller Azure Files:

- *Azure-diskar* kan användas för att skapa ett Kubernetes *DataDisk* resurs. Diskar kan använda Azure Premium storage kan backas upp av högpresterande SSD eller Azure Standard lagringsresurs som backas upp av vanliga hårddiskar. Använda Premium storage för de flesta produktion och av utvecklingsarbetsbelastningar. Azure-diskar har monterats som *ReadWriteOnce*, så är bara tillgängliga för en enda nod. Använd Azure Files för lagringsvolymer som kan användas av flera noder samtidigt.
- *Azure Files* kan användas för att montera en SMB 3.0-resurs backas upp av ett Azure Storage-konto till poddar. Filer kan du dela data över flera noder och poddar. Filer kan för närvarande kan bara använda Azure Standard lagringsresurs som backas upp av vanliga hårddiskar.

I Kubernetes representera volymer mer än bara en traditionell disk där information kan lagras och hämtas. Kubernetes volymer kan också användas som ett sätt att mata in data i en pod för användning av behållarna. Vanliga ytterligare volymtyper i Kubernetes är:

- *emptyDir* -den här volymen används som tillfälligt utrymme för en pod. Alla behållare i en pod kan komma åt data på volymen. Data som skrivs till den här volymtyp kvarstår endast för livslängden för en pod - när en pod tas bort volymen tas bort. Den här volymen vanligtvis använder den underliggande lokala noden disklagringen, men kan också finnas endast i nodens minne.
- *hemlighet* – den här volymen används för att mata in känsliga data i poddar, till exempel lösenord. Först skapar du en hemlighet med hjälp av Kubernetes-API. När du definierar din pod eller distribution av kan en särskild hemlighet begäras. Hemligheter ges endast till noder som har en schemalagd pod som kräver och hemligheten lagras i *tmpfs*, inte skriftliga till disk. När den sista pod på en nod som kräver en hemlighet tas bort raderas hemligheten från nodens tmpfs. Hemligheter lagras i ett visst namnområde och kan bara användas av poddar inom samma namnområde.
- *configMap* – den här volymtypen används för att mata in egenskaper för nyckel / värde-par i poddar, till exempel konfigurationsinformation för programmet. I stället för att definiera konfigurationsinformation för program i en behållaravbildning kan definiera du den som en Kubernetes-resurs som enkelt kan uppdateras och tillämpas på nya instanser av poddar som de har distribuerats. T.ex. använder en hemlighet skapa du först en ConfigMap med hjälp av Kubernetes-API. Den här ConfigMap kan sedan begäras när du definierar en pod eller distribution. ConfigMaps lagras i ett visst namnområde och kan bara användas av poddar inom samma namnområde.

## <a name="persistent-volumes"></a>Beständiga volymer

Volymer som har definierats och skapat som en del av pod-livscykeln finns bara tills en pod tas bort. Poddar händer ofta lagring ska förbli om schemaläggs en pod på en annan värd under en underhållshändelse, särskilt i StatefulSets. En *permanent volym* (PV) är en storage-resurs skapas och hanteras av Kubernetes-API som kan finnas utöver livslängden för en enskild pod.

Azure-diskar eller filer används för att tillhandahålla PersistentVolume. Enligt vad som anges i föregående avsnitt på volymer, bestäms ofta val av diskar eller filer av behovet av samtidig åtkomst till data- eller prestandanivå.

![Beständiga volymer i ett kluster i Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volumes.png)

En PersistentVolume kan vara *statiskt* skapats av en Klusteradministratör eller *dynamiskt* skapats av Kubernetes API-servern. Om en pod har schemalagts och begär lagring som inte är tillgänglig, Kubernetes skapa det underliggande lagringsutrymmet för Azure Disk eller filer och koppla den till din pod. Dynamisk etablering använder en *StorageClass* att identifiera vilken typ av Azure storage måste skapas.

## <a name="storage-classes"></a>Storage-klasser

Om du vill definiera olika nivåer av lagring, till exempel Premium och Standard, kan du skapa en *StorageClass*. StorageClass definierar också den *reclaimPolicy*. Den här reclaimPolicy styr beteendet för underliggande Azure-lagringsresursen när poden tas bort och permanent volym kanske inte längre är nödvändiga. Den underliggande lagringsresursen kan tas bort eller bevaras för användning med en framtida pod.

I AKS skapas två inledande StorageClasses:

- *standard* -använder Azure standardlagring att skapa en hanterad Disk. Frigöra principen anger att den underliggande Azure-disken tas bort när pod som använde det tas bort.
- *hanterade premium* -använder Azure Premium storage för att skapa Managed Disk. Frigöra principen igen anger att den underliggande Azure-disken tas bort när pod som använde det tas bort.

Om inga StorageClass har angetts för en permanent volym, används standardvärdet StorageClass. Var noga när du begär beständiga volymer så att de använder rätt lagringsutrymme du behöver. Du kan skapa en StorageClass för ytterligare behov med hjälp av `kubectl`. I följande exempel använder Premium Managed Disks och anger att den underliggande Azure-disken ska vara *bevaras* när poden tas bort:

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

## <a name="persistent-volume-claims"></a>Beständig volym anspråk

En PersistentVolumeClaim begär antingen Disk eller lagring av en viss StorageClass, åtkomstläge och storlek. Kubernetes API-servern kan dynamiskt etablera underliggande lagringsresurs i Azure, om det finns ingen befintlig resurs att uppfylla de anspråk baserat på den definierade StorageClass. Pod-definition innehåller volymmonteringen när volymen har anslutits till en pod.

![Beständig volym anspråk i ett kluster i Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

En PersistentVolume är *bunden* till en PersistentVolumeClaim när en resurs med tillgänglig lagring har tilldelats till pod som begär den. Det finns en 1:1-mappning av beständiga volymer anspråk.

Följande exempel YAML manifestet visar ett permanent volym-anspråk som använder den *hanteras premium* StorageClass och begär en Disk *5Gi* i storlek:

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

När du skapar en pod-definition anges permanent volym-anspråk för att begära önskade lagringsutrymmen. Du också ange den *volumeMount* för dina program att läsa och skriva data. Följande exempel YAML manifestet visar hur tidigare permanent volym-anspråk kan användas för att montera en volym i */mnt/azure*:

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

Om du vill se hur du skapar dynamiska och statiska volymer som använder Azure-diskar eller Azure Files finns i följande artiklar:

- [Skapa en statisk volym med hjälp av Azure-diskar][aks-static-disks]
- [Skapa en statisk volym med Azure Files][aks-static-files]
- [Skapa en dynamisk volym med hjälp av Azure-diskar][aks-dynamic-disks]
- [Skapa en dynamisk volym med hjälp av Azure Files][aks-dynamic-files]

Mer information om core Kubernetes och AKS-begrepp finns i följande artiklar:

- [Kubernetes / AKS-kluster och arbetsbelastningar][aks-concepts-clusters-workloads]
- [Kubernetes / AKS-identiteten][aks-concepts-identity]
- [Kubernetes / AKS-säkerhet][aks-concepts-security]
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
