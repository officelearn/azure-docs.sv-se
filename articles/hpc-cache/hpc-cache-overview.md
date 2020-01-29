---
title: Översikt över Azure HPC cache
description: Beskriver Azure HPC cache, en lösning för fil åtkomst Accelerator för data behandling med höga prestanda
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 2a008d22de5df8d091e868153205697b4bb343ee
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582134"
---
# <a name="what-is-azure-hpc-cache"></a>Vad är Azure HPC Cache?

Azure HPC cache ger åtkomst till dina data för HPC-uppgifter (data behandling med höga prestanda). Genom att cachelagra filer i Azure ger Azure HPC-cache skalbarhet för molnbaserad data behandling till ditt befintliga arbets flöde. Den här tjänsten kan användas även för arbets flöden där dina data lagras i WAN-länkar, t. ex. i din lokala NAS-miljö (Data Center Network-Attached Storage).

Azure HPC-cache är enkelt att starta och övervaka från Azure Portal. Befintlig NFS-lagring eller nya BLOB-behållare kan bli en del av dess sammanlagda namnrymd, vilket gör klient åtkomsten enkel även om du ändrar Server dels lagrings målet.

## <a name="use-cases"></a>Användningsfall

Azure HPC cache förbättrar produktiviteten bäst för arbets flöden som dessa:

* Läs – tungt arbets flöde för fil åtkomst
* Data som lagras i NFS-tillgängligt lagrings utrymme, Azure-Blob eller båda
* Beräknings grupper med upp till 75 000 CPU-kärnor

Azure HPC-cache kan läggas till i en mängd olika arbets flöden i flera branscher. Alla system där ett stort antal datorer behöver få åtkomst till en uppsättning filer i skala och med låg latens drar nytta av den här tjänsten. I avsnitten nedan ges vissa exempel.

### <a name="visual-effects-vfx-rendering"></a>Rendering av visuella effekter (VFX)

I media och underhållning kan Azure HPC-cache påskynda data åtkomsten för tids kritiska åter givnings projekt. VFX rendering-arbetsflöden kräver ofta sista minuters bearbetning med ett stort antal datornoder. Data för de här arbets flödena finns vanligt vis i en lokal NAS-miljö. Azure HPC cache kan cachelagra fildata i molnet för att minska svars tiden och förbättra flexibiliteten för rendering på begäran.

### <a name="life-sciences"></a>Biovetenskap

Många arbets flöden i Life Science kan dra nytta av skalbar cachelagring av filer.

Ett forsknings institut som vill hamna i sina genomiks analys arbets flöden i Azure kan enkelt byta till dem med hjälp av Azure HPC-cache. Eftersom cachen tillhandahåller POSIX-filåtkomst behövs inga ändringar på klient sidan för att köra befintliga klient arbets flöden i molnet.

Azure HPC cache kan också användas för att förbättra effektiviteten i uppgifter som sekundär analys, farmakologisk simulering eller AI-driven bild analys.

### <a name="financial-services-analytics"></a>Analyser för ekonomiska tjänster

En Azure HPC cache-distribution kan hjälpa dig att påskynda kvantitativa analys beräkningar, riskhanterings arbets belastningar och Monte Carlo-simuleringar för att ge finansiella tjänste företag bättre insikt i att fatta strategiska beslut.

## <a name="region-availability"></a>Regional tillgänglighet

Azure HPC-cache är tillgängligt i följande Azure-regioner:

* USA, östra
* USA, östra 2
* Europa, norra
* Europa, västra
* Sydostasien
* Sydney
* USA, västra 2
* Sydkorea, centrala

På [produkt sidan för Azure HPC cache](https://azure.microsoft.com/services/hpc-cache) finns den senaste tillgängliga informationen.

## <a name="service-availability"></a>Tjänsttillgänglighet

Du måste begära åtkomst för varje prenumeration som du kommer att använda med Azure HPC-cache. Den här begränsningen säkerställer service kvaliteten under de första månaderna av allmän tillgänglighet.

Begär åtkomst genom att fylla i [det här formuläret](https://aka.ms/onboard-hpc-cache). När du har lagt till din prenumeration i åtkomst listan kan du skapa cacheminnen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om funktionerna i [produkt sidan för Azure HPC cache](https://azure.microsoft.com/services/hpc-cache)
* Läs om produkt [krav](hpc-cache-prereqs.md)
* [Skapa en Azure HPC-cache](hpc-cache-create.md) från Azure Portal
