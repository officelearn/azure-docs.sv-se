---
title: Översikt över Microsoft Azure FXT Edge Filer
description: Beskriver Azure FXT Edge Filer hybridlagringscache, en aktiv fil- och filåtkomstacceleratorlösning för högpresterande datorsystem
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: acf35015ff7851f7ea2a2527852be2573512e35d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "72254837"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Vad är Azure FXT Edge Filer hybridlagringscache?

Azure FXT Edge Filer är en hybridlagringscachelagringsinstallation som ger snabb filåtkomst och aktivt arkiv för HPC-uppgifter (High-Performance Computing).

Den fungerar med flera datakällor, oavsett om de lagras i ett lokalt datacenter, på distans eller i molnet. Azure FXT Edge Filer kan tillhandahålla ett enhetligt namnområde för data i olika lagringssystem.

Tre eller flera FXT Edge Filer-maskinvaruenheter fungerar tillsammans som ett klustrade filsystem för att tillhandahålla cachen. Kontakta din Microsoft-representant om du vill ha mer information om hur du köper den maskinvara som krävs. 

Mer information finns i produktinformationen och databladet på [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Användningsfall

Azure FXT Edge Filer förbättrar produktiviteten bäst för arbetsflöden som dessa:

* Arbetsflöde för lästunga filåtkomst 
* NFSv3- eller SMB2-protokoll
* Beräkna gårdar på 1000 till 100 000 CPU-kärnor

### <a name="nas-optimization-and-scaling"></a>NAS-optimering och skalning

Du kan använda Azure FXT Edge Filer-cacheminnet för att underlätta åtkomsten till befintliga NetApp- och Dell EMC Isilon NAS-system. Du kan också lägga till Azure Blob eller annan molnlagring för att ge skalbarhet utan att behöva omarbeta dataåtkomstprocesser på klientsidan. 

### <a name="wan-caching"></a>WAN-cachelagring

Azure FXT Edge Filer kan användas för att stödja snabb filåtkomst från avancerade användare när de data de behöver lagras någon annanstans. Ge åtkomst samtidigt som säkerhetskopiering och andra datahanteringssystem i ett centraliserat datacenter bevaras. 

### <a name="active-archive-in-azure-blob"></a>Aktivt arkiv i Azure Blob

Expandera ditt datacenter till molnlagring med Azure FXT Edge Filer som åtkomstpunkt. 

## <a name="features"></a>Funktioner 

Det finns två maskinvarumodeller. 

| Modell | Dram | NVMe SSD | Nätverksportar | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |


## <a name="next-steps"></a>Nästa steg

* Fortsätt att lära dig mer om Azure FXT Edge Filer genom att läsa [specifikationerna](fxt-specs.md) eller [installationshandledningen](fxt-install.md).
* Lär dig hur du köper Azure FXT Edge Filer på [produktsidan för Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).