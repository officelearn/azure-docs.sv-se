---
title: Storage kapacitetsplanering för Azure Stack | Microsoft Docs
description: Läs mer om storage kapacitetsplanering för Azure Stack-distributioner.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.openlocfilehash: 9ea46860817d60c2ffbde68c0fc5ae6f6ca14877
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368883"
---
# <a name="azure-stack-storage-capacity-planning"></a>Azure Stack storage kapacitetsplanering
Följande avsnitt innehåller Azure Stack lagringskapacitet planeringsinformation för att hjälpa att planera för de lösningar lagringsbehov.

## <a name="uses-and-organization-of-storage-capacity"></a>Användning och organisation lagringskapacitet
Hyperkonvergerade konfigurationen av Azure Stack möjliggör delning av fysiska lagringsenheter. Tre flera avsnitt i tillgängligt lagringsutrymme är mellan infrastrukturen, tillfällig lagring av de virtuella klientdatorerna och lagringen säkerhetskopiering blobbar, tabeller och köer Azure konsekvent Storage (ACS)-tjänster.

## <a name="spaces-direct-cache-and-capacity-tiers"></a>Funktionen Lagringsdirigering Cache och Kapacitetsnivåer
Det finns lagringskapacitet som används för operativsystemet, lokal loggning, skärmbilder och andra infrastruktur för tillfällig lagring behov. Den här lokala lagringskapacitet är separata (enheter och kapacitet) från lagringsenheter börja hanteras av Storage Spaces Direct-konfigurationen. Resten av lagringsenheter placeras i en enda pool med lagringskapacitet, oavsett antalet servrar i Skalningsenheten. Dessa enheter finns två typer av: Cache och kapacitet.  Cache-enheter är bara – Cache. Lagringsutrymmen direkt förbrukar dessa enheter för återskrivning och läsa cachelagring. Kapaciteter för dessa Cache-enheter, gäller medan används, inte för formaterade, ”synliga” kapacitet formaterade virtuella-diskar. Kapacitetsenheter används för detta ändamål och ange ”home platsen” för de data som hanteras av lagringsutrymmen.

Alla lagringskapacitet är allokerade och hanteras direkt av Azure Stack-infrastruktur. Operatören behöver fatta beslut om konfigurationen, allokering, eller hantera alternativ när det gäller kapacitetsökning. Dessa designbeslut har gjorts för att anpassas till lösningskraven och utförs automatiskt under antingen inledande installation/distribution eller under kapacitetsökning. Information om återhämtning, reserverad kapacitet för behöver och annan information har bedömts vara som en del av designen. 

Operatörer kan välja mellan en alla flash eller en hybrid-konfiguration för lagring:

![Kapacitetsplanering för Azure storage](media/azure-stack-capacity-planning/storage.png)

I alla flash konfigurationen är cacheminnet NVMe ett urval av SATA SSD eller NVMe för kapacitet. Hybrid-konfiguration finns cacheminnet i ett val mellan NVMe och SATA SSD och kapaciteten är HDD.

En kort sammanfattning av Lagringsdirigering och lagringskonfiguration för Azure Stack är följande:
- Ett blanksteg lagringspoolen per Skalningsenhet (alla lagringsenheter är konfigurerade i en enda pool)
- Virtuella diskar skapas som en tre kopia spegling för bästa prestanda och återhämtning
- Varje virtuell disk som är formaterade som en ReFS-filsystemet
- Virtuella diskkapacitet beräknas och allokeras på ett sätt att lämna en kapacitet enhets mängden datakapacitet som tilldelats i poolen. Det här är detsamma som en enhet med kapacitet per server.
- Varje ReFS-filsystemet har BitLocker är aktiverat för kryptering av data i vila. 

De virtuella--diskarna som skapas automatiskt och deras kapacitet är följande:




|Namn|Beräkning av kapacitet|Beskrivning|
|-----|-----|-----|
|Lokal/startenheten|Minst 340 GB<sup>1</sup>|Enskild serverlagring för avbildningar av operativsystem och ”local” infrastrukturens virtuella datorer|
|Infrastruktur|3,5 TB|All användning för Azure Stack-infrastruktur|
|VmTemp|Se nedan<sup>2</sup>|Virtuella datorer har en tillfällig disk som är ansluten och att data lagras i dessa virtuella diskar|
|ACS|Se nedan <sup>3</sup>|Azure konsekvent lagringskapacitet för att underhålla blobbar, tabeller och köer|

<sup>1</sup> minsta lagringskapacitet som krävs för den lösning Azure Stack-partnern.

<sup>2</sup> storleken för virtuell disk som används för virtuell klientdator temporära diskar beräknas som ett förhållande på det fysiska minnet på servern. Enligt vad som anges i tabellerna nedan för Azure IaaS VM-storlekar är den temporära disken ett förhållande på den fysiska minne som tilldelats den virtuella datorn. Allokeringen klar för ”temporär disk” lagring i Azure Stack görs på ett sätt att samla in de flesta användningsfall, men kanske inte uppfyller alla lagringsbehov för temporär disk. Förhållandet mellan valt är en kompromiss mellan gör tillfällig lagring tillgängligt när du inte använder en majoritet av lagringskapacitet lösningen för temporär disk-kapacitet. En tillfällig lagringsdisk skapas per server i Skalningsenheten. Kommer inte att utöka kapaciteten för tillfällig lagring mer än 10 procent av den totala tillgängliga lagringskapaciteten i lagringspoolen för Skalningsenheten. Beräkningen är något som liknar följande exempel:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> de virtuella--diskarna som skapats för användning av ACS är en enkel avdelning av återstående kapacitet. Enligt vad som anges, alla virtuella-diskar är en trevägsspegling och en kapacitet enhet leverantör av kapaciteten för varje server är lediga. De olika virtuella-diskarna räknats upp ovan allokeras först och återstående kapacitet används sedan för ACS virtuella-diskar.

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om Azure Stack-kalkylbladet för kapacitetsplanering](capacity-planning-spreadsheet.md)
