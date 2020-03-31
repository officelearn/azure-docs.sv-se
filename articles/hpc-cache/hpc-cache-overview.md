---
title: Översikt över Azure HPC-cache
description: Beskriver Azure HPC Cache, en filåtkomstacceleratorlösning för högpresterande datorsystem
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 2a008d22de5df8d091e868153205697b4bb343ee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241194"
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

* USA, östra
* USA, östra 2
* Europa, norra
* Europa, västra
* Sydostasien
* Sydney
* USA, västra 2
* Sydkorea, centrala

Kontrollera [produktsidan för Azure HPC-cache](https://azure.microsoft.com/services/hpc-cache) för den senaste tillgänglighetsinformationen.

## <a name="service-availability"></a>Tjänstens tillgänglighet

Du måste begära åtkomst för varje prenumeration som du ska använda med Azure HPC-cache. Den här begränsningen säkerställer tjänsternas kvalitet under de första månaderna av allmän tillgänglighet.

Begär åtkomst genom att fylla [i det här formuläret](https://aka.ms/onboard-hpc-cache). När prenumerationen har lagts till i åtkomstlistan kan du skapa cacheminnen.

## <a name="next-steps"></a>Nästa steg

* Läs [produktsidan](https://azure.microsoft.com/services/hpc-cache) för Azure HPC Cache om du vill veta mer om dess funktioner
* Läs mer om [produktförkunskaper](hpc-cache-prereqs.md)
* [Skapa en Azure HPC-cache](hpc-cache-create.md) från Azure-portalen
