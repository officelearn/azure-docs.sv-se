---
title: Operatorn bästa praxis - lagring i Azure Kubernetes Services (AKS)
description: Läs kluster operatorn metodtipsen för lagring, kryptering och säkerhetskopiering i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: iainfou
ms.openlocfilehash: 7476747de31819907cf144e5a6b33cb29e1f866f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60463950"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Metodtips för lagring och säkerhetskopiering i Azure Kubernetes Service (AKS)

När du skapar och hantera kluster i Azure Kubernetes Service (AKS), behöver ofta storage i dina program. Det är viktigt att förstå prestandabehov och få åtkomst till metoder för poddar så att du kan ange lämplig lagringen för program. Nodstorlek AKS kan påverka dessa lagringsalternativ. Du bör också planera för sätt att säkerhetskopiera och testa återställningsprocessen för direktansluten lagring.

Den här bästa praxis artikeln handlar om överväganden för lagring för klusteroperatörer. I den här artikeln lär du dig:

> [!div class="checklist"]
> * Vilka typer av lagring som är tillgängliga
> * Så här korrekt storlek AKS-noder för lagringsprestanda
> * Skillnader mellan dynamiska och statiska etablering av volymer
> * Sätt att säkerhetskopiera och skydda dina datavolymer

## <a name="choose-the-appropriate-storage-type"></a>Välja rätt lagringstyp

**Bästa praxis riktlinjer** – förstå i ditt programs behov av att välja rätt lagringen. Använda SSD-uppbackad lagring för produktionsarbetsbelastningar med hög prestanda. Planera för nätverk-baserad lagring när det finns ett behov av flera samtidiga anslutningar.

Program kräver ofta olika typer och hastigheter lagringsutrymme. Dina program behöver en lagringslösning som ansluter till enskilda poddar eller delas mellan flera poddar? Är lagring för skrivskyddad åtkomst till data och skriva stora mängder strukturerade data? Dessa lagring måste fastställa den mest lämpliga typ av lagring du använder.

I följande tabell beskrivs de tillgängliga lagringstyper och deras funktioner:

| Användningsfall | Volym-plugin-programmet | Läs/Skriv en gång | Skrivskyddad många | Läs/Skriv många |
|----------|---------------|-----------------|----------------|-----------------|
| Delad konfiguration       | Azure Files   | Ja | Ja | Ja |
| Strukturerade AppData        | Azure-diskar   | Ja | Nej  | Nej  |
| AppData, skrivskyddade resurser | [Dysk (förhandsversion)][dysk] | Ja | Ja | Nej  |
| Ostrukturerade data, filsystemsåtgärder | [BlobFuse (förhandsversion)][blobfuse] | Ja | Ja | Ja |

De två primära typerna av lagring för volymerna i AKS backas upp av Azure-diskar eller Azure Files. För att förbättra säkerheten, båda typer av lagring som använder Azure Storage Service Encryption (SSE) som standard som krypterar data i vila. Diskar kan för närvarande inte krypteras med Azure Disk Encryption den AKS nivån.

Azure Files är för närvarande tillgängliga i Standard-prestandanivån. Azure-diskar som är tillgängliga i Standard och Premium-prestandanivåer:

- *Premium* diskar backas upp av högpresterande SSD-diskar (SSD). Premium-diskar rekommenderas för alla produktionsarbetsbelastningar.
- *Standard* diskar backas upp av roterande diskar (HDD) och är bra för arkivering eller sällan använda data.

Förstå prestanda programbehov och åtkomstmönster för att välja rätt lagringsnivå. Läs mer om Managed Disks storlekar och prestandanivåer [översikt över Azure Managed Disks][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Skapa och använda lagringsklasser för att definiera programmets behov

Typ av lagring som du använder har definierats med Kubernetes *lagringsklasser*. Klassen storage refereras sedan i distribution eller pod-specifikationen. Dessa definitioner fungerar tillsammans för att skapa lämpliga lagringen och ansluter den till poddar. Mer information finns i [lagringsklasser i AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Ändra storlek på noderna för lagringsbehov

**Bästa praxis riktlinjer** -varje nodstorlek stöder maximalt antal diskar. Storleken på olika tillhandahåller även olika mängder bandbredd för lokal lagring och nätverk. Planera för dina behov att distribuera rätt storlek för noderna.

AKS-noder körs som virtuella Azure-datorer. Det finns olika typer och storlekar på virtuella datorer. Varje VM-storlek innehåller en annan mängd kärnresurser som CPU och minne. Dessa storlekar på Virtuella datorer har ett maximalt antal diskar som kan kopplas. Lagringsprestanda varierar även mellan VM-storlekar för den högsta lokal och ansluten disken IOPS (antal input/output-åtgärder per sekund).

Om dina program kräver Azure-diskar som lagringslösning, kan du planera för och välj en lämplig nod VM-storlek. Hur mycket processor och minne är inte den enda faktorn när du väljer en VM-storlek. Funktioner för lagring är också viktigt. Till exempel både den *Standard_B2ms* och *Standard_DS2_v2* VM-storlekar är samma mängd CPU-och minnesresurser. Deras potentiella lagringsprestanda är olika, som visas i följande tabell:

| Noden typ och storlek | Virtuell processor | Minne (GiB) | Maximalt antal datadiskar | Maximalt icke cachelagrat IOPs per disk | Maximalt icke cachelagrat dataflöde (Mbit/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

Här är den *Standard_DS2_v2* tillåter dubbelt så många anslutna diskar och tillhandahåller tre till fyra gånger mängden IOPS och disk dataflöde. Om du endast har tittat på core beräkningsresurser och jämföra kostnader kan du välja den *Standard_B2ms* VM storlek och har dålig lagringsprestanda och begränsningar. Arbeta med programutvecklingsteamet för att förstå deras lagringsbehov för kapacitet och prestanda. Välj rätt VM-storlek för AKS-noder att uppfylla eller överträffa sina prestandabehov. Regelbundet baslinje program att justera storleken efter behov.

Mer information om tillgängliga storlekar finns i [storlekar för Linux-datorer i Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dynamiskt etablera volymer

**Bästa praxis riktlinjer** – om du vill minska hanteringskostnader och låter dig skala, inte statiskt skapa och tilldela beständiga volymer. Använd dynamisk etablering. I din lagringsklasser definierar du lämpliga frigöra principen så att onödiga lagringskostnaderna minimeras när poddar tas bort.

När du vill koppla lagring till poddar kan använda du beständiga volymer. Dessa beständiga volymer kan skapas manuellt eller dynamiskt. Skapa beständiga volymer manuellt lägger till hanteringskostnader och begränsar möjligheten att skala. Använd dynamisk permanent volym etablering för att förenkla lagringshantering och gör att utöka och skala efter behov.

![Beständig volym anspråk i ett kluster i Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

Ett permanent volym-anspråk (PVC) kan du dynamiskt skapa lagring efter behov. De underliggande Azure-diskarna skapas som poddar begär dem. I pod-definition begära du en volym för att skapas och ansluts till en utformade monteringssökväg

Koncepten för att dynamiskt skapa och använda volymer, se [beständiga volymer anspråk][aks-concepts-storage-pvcs].

Dessa volymer i praktiken finns i hur du skapar och använder en permanent volym med dynamiskt [Azure Disks] [ dynamic-disks] eller [Azure Files][dynamic-files].

Som en del av din lagring, anger du lämplig *reclaimPolicy*. Den här reclaimPolicy styr beteendet för underliggande Azure-lagringsresursen när poden tas bort och permanent volym kanske inte längre är nödvändiga. Den underliggande lagringsresursen kan tas bort eller bevaras för användning med en framtida pod. ReclaimPolicy kan ange *behålla* eller *ta bort*. Förstå dina program behöver och implementera regelbundna kontroller för lagringsutrymme som finns kvar för att minimera icke använt lagringsutrymme som används och faktureras.

Mer information om klassen lagringsalternativ finns i [storage frigöra principer][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Skydda och säkerhetskopiera dina data

**Bästa praxis riktlinjer** – säkerhetskopiera dina data med ett lämpligt verktyg för din typ, till exempel Velero eller Azure Site Recovery. Kontrollera integriteten och säkerheten för dessa säkerhetskopior.

När dina program lagrar och använda data sparas på diskar eller i filer, du behöver göra regelbundna säkerhetskopior eller ögonblicksbilder av dessa data. Azure-diskar som kan använda inbyggda ögonblicksbild tekniker. Du kan behöva en hook för dina program att tömma skriver till disken innan du utför ögonblicksbildsåtgärden. [Velero] [ velero] kan säkerhetskopiera beständiga volymer och ytterligare klusterresurser och konfigurationer. Om du inte [ta bort tillstånd från dina program][remove-state], säkerhetskopiera data från beständiga volymer och regelbundet testa återställningsåtgärder för att kontrollera dataintegriteten och processer som krävs.

Förstå begränsningar av olika metoder för säkerhetskopiering av data och du måste du inaktivera dina data för ögonblicksbild. Säkerhetskopiering av data kan inte nödvändigtvis du återställa din Programmiljö av klusterdistribution. Mer information om dessa scenarier finns i [bästa praxis för företag affärskontinuitet och haveriberedskap i AKS][best-practices-multi-region].

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på lagring bästa praxis i AKS. Läs mer om grunderna i storage i Kubernetes [lagringsbegrepp för program i AKS][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[dysk]: https://github.com/Azure/kubernetes-volume-drivers/tree/master/flexvolume/dysk
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/linux/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/linux/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
