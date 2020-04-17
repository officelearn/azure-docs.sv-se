---
title: Översikt över Azure HPC-cache
description: Beskriver Azure HPC Cache, en filåtkomstacceleratorlösning för högpresterande datorsystem
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 084c33874b474fc1789df93e088d3cec4263eac9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536649"
---
# <a name="what-is-azure-hpc-cache"></a>Vad är Azure HPC Cache?

Azure HPC Cache snabbar åtkomst till dina data för högpresterande datorsystem (HPC) uppgifter. Genom att cachelagra filer i Azure ger Azure HPC-cache skalbarheten för molnbaserad databehandling till ditt befintliga arbetsflöde. Den här tjänsten kan användas även för arbetsflöden där dina data lagras över WAN-länkar, till exempel i din lokala datacenternätverksanknad lagringsmiljö (NAS).

Azure HPC Cache är enkelt att starta och övervaka från Azure-portalen. Befintlig NFS-lagring eller nya Blob-behållare kan bli en del av dess aggregerade namnområde, vilket gör klientåtkomst enkel även om du ändrar backend-lagringsmålet.

## <a name="use-cases"></a>Användningsfall

Azure HPC Cache förbättrar produktiviteten bäst för arbetsflöden som dessa:

* Arbetsflöde för lästunga filåtkomst
* Data som lagras i NFS-tillgänglig lagring, Azure Blob eller båda
* Beräkna gårdar på upp till 75 000 PROCESSORKÄRNor

Azure HPC-cache kan läggas till i en mängd olika arbetsflöden i många branscher. Alla system där ett stort antal datorer behöver komma åt en uppsättning filer i stor skala och med låg latens kommer att dra nytta av den här tjänsten. Avsnitten nedan ger specifika exempel.

### <a name="visual-effects-vfx-rendering"></a>Visuella effekter (VFX) rendering

I media och underhållning kan Azure HPC Cache påskynda dataåtkomst för tidskritiska renderingsprojekt. VFX-renderingsarbetsflöden kräver ofta bearbetning i sista minuten av ett stort antal beräkningsnoder. Data för dessa arbetsflöden finns vanligtvis i en lokal NAS-miljö. Azure HPC Cache kan cachelagra fildata i molnet för att minska svarstiden och öka flexibiliteten för rendering på begäran.

### <a name="life-sciences"></a>Biovetenskap

Många life sciences arbetsflöden kan dra nytta av skala ut fil caching.

Ett forskningsinstitut som vill portera sina genomiska analysarbetsflöden till Azure kan enkelt flytta dem med hjälp av Azure HPC-cache. Eftersom cachen ger POSIX-filåtkomst behövs inga ändringar på klientsidan för att köra deras befintliga klientarbetsflöde i molnet.

Azure HPC Cache kan också utnyttjas för att förbättra effektiviteten i uppgifter som sekundär analys, farmakologisk simulering eller AI-driven bildanalys.

### <a name="financial-services-analytics"></a>Analyser för ekonomiska tjänster

En Azure HPC-cachedistribution kan hjälpa till att påskynda kvantitativa analysberäkningar, riskanalysarbetsbelastningar och Monte Carlo-simuleringar för att ge företag för finansiella tjänster bättre insikt för att fatta strategiska beslut.

## <a name="region-availability"></a>Regional tillgänglighet

Azure HPC-cache är tillgänglig i dessa Azure-regioner:

| Nordamerika      | Europa         | Asien            | Australien      |
|--------------------|----------------|-----------------|----------------|
| USA, östra            | Europa, norra   | Sydkorea, centrala   | Australien, östra |
| USA, östra 2          | Europa, västra    | Sydostasien  |               |
| USA, södra centrala | | | |
| USA, västra 2        | | | |

[Funktionen kundhanterade nycklar](customer-keys.md) stöds endast i dessa regioner:

* USA, östra
* USA, södra centrala
* USA, västra 2

Kontrollera [produktsidan för Azure HPC-cache](https://azure.microsoft.com/services/hpc-cache) för den senaste tillgänglighetsinformationen.

## <a name="next-steps"></a>Nästa steg

* Läs [produktsidan](https://azure.microsoft.com/services/hpc-cache) för Azure HPC Cache om du vill veta mer om dess funktioner
* Läs mer om [produktförkunskaper](hpc-cache-prereqs.md)
* [Skapa en Azure HPC-cache](hpc-cache-create.md) från Azure-portalen
