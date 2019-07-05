---
title: Översikt över Microsoft Azure FXT Edge Filer
description: Beskriver Azure FXT Edge Filer hybrid lagringscache, en aktiv arkivering och filen åtkomst accelerator lösning för databehandling med höga prestanda
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 58d4a2a52757b6ace1059fcccf379df3de5fd75c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542909"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Vad är Azure FXT Edge Filer hybrid storage cache?

Azure FXT Edge Filer är en hybridlagring cachelagring installation som ger snabb åtkomst och aktiva Arkiv för databehandling med höga prestanda (HPC) uppgifter.

Det fungerar med flera datakällor, oavsett om den lagras i ett lokalt Datacenter via fjärranslutning eller i molnet. Azure FXT Edge Filer kan ge en enhetlig namnrymd för data i olika lagringssystem.

Tre eller fler FXT Edge Filer maskinvaruenheter fungerar tillsammans som ett klustrade filsystem att tillhandahålla cachen. Kontakta din Microsoft-representant för mer information om hur du köper maskinvaran som krävs. 

Mer information finns produkten information och data bladet på [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Användningsfall

Edge-Filer Azure FXT förbättrar produktiviteten som är bäst för arbetsflöden som dessa:

* Läs omfattande filen åtkomst arbetsflöde 
* NFSv3 eller SMB2-protokoll
* Compute servergrupper på 1 000 till 100 000 CPU-kärnor

### <a name="nas-optimization-and-scaling"></a>Optimering av NAS och skalning

Du kan använda Azure FXT Edge Filer cache för att utjämna åtkomst till befintliga NetApp- och Dell EMC Isilon NAS-system. Du kan också lägga Azure Blob eller andra molnlagring för att tillhandahålla skalbarhet utan att behöva omarbeta data access-processer på klientsidan. 

### <a name="wan-caching"></a>WAN cachelagring

Azure FXT Edge-Filer kan användas som stöder snabb åtkomst från Privilegierade användare när de data de behöver lagras på annan plats. Ge åtkomst samtidigt som säkerhetskopiering och andra hanteringssystem för data i ett centraliserad datacenter. 

### <a name="active-archive-in-azure-blob"></a>Aktiva Arkiv i Azure Blob

Utöka ditt datacenter till molnet med Azure FXT Edge Filer anslutningspunkten. 

## <a name="features"></a>Funktioner 

Det finns två modeller för maskinvara. 

| Modell | DRAM | NVMe SSD | Nätverksportar | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25Gb / 10Gb + 2 x 1 Gb |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25Gb / 10Gb + 2 x 1 Gb |


## <a name="next-steps"></a>Nästa steg

* Lära dig mer om Azure FXT Edge Filer genom att läsa den [specifikationer](fxt-specs.md) eller [installation självstudien](fxt-install.md).
* Lär dig hur du köper Azure FXT Edge Filer på den [produktsidan för Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).