---
title: Metodtips för lagring och säkerhetskopiering
titleSuffix: Azure Kubernetes Service
description: Lär dig metodtips för klusteroperatören för lagring, datakryptering och säkerhetskopiering i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 843b775f7761af7cd40140c9bf34768d63eb5a50
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877906"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Metodtips för lagring och säkerhetskopiering i Azure Kubernetes Service (AKS)

När du skapar och hanterar kluster i Azure Kubernetes Service (AKS) behöver dina program ofta lagringsutrymme. Det är viktigt att förstå prestandabehov och åtkomstmetoder för poddar så att du kan tillhandahålla lämplig lagring till program. AKS-nodstorleken kan påverka dessa lagringsalternativ. Du bör också planera för olika sätt att säkerhetskopiera och testa återställningsprocessen för ansluten lagring.

Den här artikeln med metodtips fokuserar på lagringsöverväganden för klusteroperatorer. I den här artikeln lär du dig:

> [!div class="checklist"]
> * Vilka typer av lagringsutrymme är tillgängliga
> * Så här storlekar du AKS-noder på rätt sätt för lagringsprestanda
> * Skillnader mellan dynamisk och statisk etablering av volymer
> * Olika sätt att säkerhetskopiera och skydda dina datavolymer

## <a name="choose-the-appropriate-storage-type"></a>Välj lämplig lagringstyp

**Vägledning för bästa praxis** – Förstå behoven hos ditt program för att välja rätt lagring. Använd hög prestanda, SSD-stödd lagring för produktionsarbetsbelastningar. Planera för nätverksbaserad lagring när det finns behov av flera samtidiga anslutningar.

Program kräver ofta olika typer och hastigheter för lagring. Behöver dina program lagring som ansluter till enskilda poddar eller delas över flera poddar? Är lagringen för skrivskyddad åtkomst till data eller för att skriva stora mängder strukturerade data? Dessa lagringsbehov avgör vilken typ av lagring som ska användas.

I följande tabell beskrivs tillgängliga lagringstyper och deras funktioner:

| Användningsfall | Volym plugin | Läs/skriv en gång | Skrivskyddade många | Läs/skriv många | Stöd för Windows Server-behållare |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Delad konfiguration       | Azure Files   | Ja | Ja | Ja | Ja |
| Strukturerade appdata        | Azure-diskar   | Ja | Inga  | Inga  | Ja |
| Ostrukturerade data, filsystemåtgärder | [BlobFuse (BlobFuse)][blobfuse] | Ja | Ja | Ja | Inga |

De två primära typerna av lagring som tillhandahålls för volymer i AKS backas upp av Azure Disks eller Azure Files. För att förbättra säkerheten använder båda typerna av lagring Azure Storage Service Encryption (SSE) som standard som krypterar data i vila. Diskar kan för närvarande inte krypteras med Azure Disk Encryption på AKS-nodnivå.

Både Azure-filer och Azure-diskar är tillgängliga på standard- och premiumprestandanivåer:

- *Premiumdiskar* backas upp av högpresterande SSD-diskar (Solid State). Premiumdiskar rekommenderas för alla produktionsarbetsbelastningar.
- *Standarddiskar* backas upp av vanliga snurrande diskar (HÅRDDISKAR) och är bra för arkivering eller sällan åtkomna data.

Förstå programmets prestandabehov och åtkomstmönster för att välja lämplig lagringsnivå. Mer information om storlekar och prestandanivåer för hanterade diskar finns i [översikt över hanterade hårddiskar i Azure][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Skapa och använda lagringsklasser för att definiera programbehov

Vilken typ av lagring du använder definieras med kubernetes *lagringsklasser*. Lagringsklassen refereras sedan i pod- eller distributionsspecifikationen. Dessa definitioner fungerar tillsammans för att skapa lämplig lagring och ansluta den till poddar. Mer information finns [i Lagringsklasser i AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Ändra storlek på noderna för lagringsbehov

**Vägledning för bästa praxis** - Varje nodstorlek stöder ett maximalt antal diskar. Olika nodstorlekar ger också olika mängder lokal lagring och nätverksbandbredd. Planera för dina programkrav för att distribuera lämplig storlek på noder.

AKS-noder körs som virtuella Azure-datorer. Olika typer och storlekar av virtuell dator är tillgängliga. Varje vm-storlek ger olika mängder kärnresurser som CPU och minne. Dessa VM-storlekar har ett maximalt antal diskar som kan anslutas. Lagringsprestanda varierar också mellan VM-storlekar för den maximala lokala och anslutna disken IOPS (input/utdataåtgärder per sekund).

Om dina program kräver Azure Diskar som lagringslösning, planera för och välj en lämplig nod VM-storlek. Mängden CPU och minne är inte den enda faktorn när du väljer en vm-storlek. Lagringsfunktionerna är också viktiga. Till exempel innehåller både *Standard_B2ms* och *Standard_DS2_v2* VM-storlekar en liknande mängd CPU- och minnesresurser. Deras potentiella lagringsprestanda är annorlunda, vilket visas i följande tabell:

| Nodtyp och storlek | Virtuell processor | Minne (GiB) | Maximalt antal datadiskar | Max oansluten disk IOPS | Max okopplat dataflöde (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

Här tillåter *Standard_DS2_v2* dubbelt så många anslutna diskar och ger tre till fyra gånger så mycket IOPS- och diskgenomströmning. Om du bara tittade på de grundläggande beräkningsresurserna och jämförde kostnader kan du välja *Standard_B2ms* vm-storlek och har dålig lagringsprestanda och begränsningar. Arbeta med ditt programutvecklingsteam för att förstå deras lagringskapacitet och prestandabehov. Välj lämplig VM-storlek för AKS-noder för att uppfylla eller överträffa deras prestandabehov. Baslinjeprogram för att justera vm-storlek efter behov.

Mer information om tillgängliga vm-storlekar finns i [Storlekar för virtuella Linux-datorer i Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Volymer för dynamisk etablering

**Vägledning om bästa praxis** – För att minska hanteringskostnader och låta dig skala, skapa och tilldela beständiga volymer statiskt. Använd dynamisk etablering. I lagringsklasserna definierar du lämplig återkravsprincip för att minimera onödiga lagringskostnader när poddar har tagits bort.

När du behöver koppla lagring till poddar använder du beständiga volymer. Dessa beständiga volymer kan skapas manuellt eller dynamiskt. Manuellt skapande av beständiga volymer lägger till hanteringskostnader och begränsar din förmåga att skala. Använd dynamisk beständig volymetablering för att förenkla lagringshanteringen och låta dina program växa och skala efter behov.

![Beständiga volymanspråk i ett AKS-kluster (Azure Kubernetes Services)](media/concepts-storage/persistent-volume-claims.png)

Med ett beständigt volymanspråk (PVC) kan du dynamiskt skapa lagring efter behov. De underliggande Azure-diskarna skapas som poddar begär dem. I pod-definitionen begär du att en volym ska skapas och kopplas till en angiven monteringsbana.

Begreppen om hur du dynamiskt skapar och använder volymer finns i [Beständiga volymer Fordringar][aks-concepts-storage-pvcs].

Om du vill se dessa volymer i praktiken kan du se hur du dynamiskt skapar och använder en beständig volym med [Azure Diskar][dynamic-disks] eller [Azure Files][dynamic-files].

Som en del av dina lagringsklassdefinitioner anger du lämplig *reclaimPolicy*. Den här reclaimPolicy styr beteendet för den underliggande Azure-lagringsresursen när podden tas bort och den beständiga volymen kanske inte längre krävs. Den underliggande lagringsresursen kan tas bort eller behållas för användning med en framtida pod. ReclaimPolicy kan ange att *behålla* eller *ta bort*. Förstå dina programbehov och implementera regelbundna kontroller för lagring som behålls för att minimera mängden oanvänd lagring som används och faktureras.

Mer information om lagringsklassalternativ finns i [principer för lagringsåtervinning][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Skydda och säkerhetskopiera dina data

**Vägledning för bästa praxis** – Säkerhetskopiera dina data med ett lämpligt verktyg för din lagringstyp, till exempel Velero eller Azure Site Recovery. Verifiera integriteten och säkerheten för dessa säkerhetskopior.

När dina program lagrar och använder data som sparats på diskar eller i filer måste du ta regelbundna säkerhetskopior eller ögonblicksbilder av dessa data. Azure Disks kan använda inbyggda ögonblicksbildtekniker. Du kan behöva leta efter dina program för att rensa skrivningar till disk innan du utför ögonblicksbilden. [Velero][velero] kan säkerhetskopiera beständiga volymer tillsammans med ytterligare klusterresurser och konfigurationer. Om du inte kan [ta bort tillstånd från dina program][remove-state]säkerhetskopierar du data från beständiga volymer och testar regelbundet återställningsåtgärderna för att verifiera dataintegritet och de processer som krävs.

Förstå begränsningarna i de olika metoderna för säkerhetskopiering av data och om du behöver för att få dina data före ögonblicksbild. Säkerhetskopiering av data behöver inte nödvändigtvis du återställa programmiljön för klusterdistribution. Mer information om dessa scenarier finns i [Metodtips för affärskontinuitet och haveriberedskap i AKS][best-practices-multi-region].

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserade på bästa metoder för lagring i AKS. Mer information om grunderna för lagring i Kubernetes finns i [Lagringsbegrepp för program i AKS][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
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
