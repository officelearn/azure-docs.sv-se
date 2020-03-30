---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 87457bb103f49be4ca3e7bf9f463c5bf63f3a119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597845"
---
För att skydda data i dina Azure-filresurser mot dataförlust eller skada, lagrar alla Azure-filresurser flera kopior av varje fil när de skrivs. Beroende på kraven för din arbetsbelastning kan du välja ytterligare grad av redundans. Azure Files stöder för närvarande följande alternativ för dataredundans:

- **Lokalt redundant**: Lokalt redundant lagring, ofta kallad LRS, innebär att varje fil lagras tre gånger i ett Azure-lagringskluster. Detta skyddar mot förlust av data på grund av maskinvarufel, till exempel en felaktig hårddisk.
- **Zon redundant:** Zon redundant lagring, ofta kallad ZRS, innebär att varje fil lagras tre gånger över tre olika Azure-lagringskluster. De tre olika Azure-lagringskluster varje lagra filen tre gånger, precis som med lokalt redundant lagring, och är fysiskt isolerade i olika *Azure-tillgänglighetszoner*. Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. En skriv till lagring accepteras inte förrän den skrivs till lagringskluster i alla tre tillgänglighetszoner. 
- **Geoupptundit:** Geo redundant lagring, ofta kallad GRS, är som lokalt redundant lagring, eftersom en fil lagras tre gånger i ett Azure-lagringskluster i den primära regionen. Alla skrivningar replikeras sedan asynkront till en sekundär microsoft-definierad region. Geouppsagd lagring ger 6 kopior av dina data som sprids mellan två Azure-regioner. I händelse av en större katastrof, till exempel permanent förlust av en Azure-region på grund av en naturkatastrof eller annan liknande händelse, kommer Microsoft att utföra en redundans så att den sekundära i praktiken blir den primära, som betjänar alla åtgärder. Eftersom replikeringen mellan de primära och sekundära regionerna är asynkrona går data som ännu inte replikerats till den sekundära regionen förlorade i händelse av en större katastrof. Du kan också utföra en manuell redundans för ett geouppsagt lagringskonto.
- **Geo-zon redundant**: Geo-zon redundant lagring, ofta kallad GZRS, är som zon redundant lagring, eftersom en fil lagras nio gånger över 3 olika lagringskluster i den primära regionen. Alla skrivningar replikeras sedan asynkront till en sekundär microsoft-definierad region. Redundansprocessen för geo-zon redundant lagring fungerar på samma sätt som för geo-redundant lagring.

Standard Azure-filresurser stöder alla fyra redundanstyper, medan premium Azure-filresurser endast stöder lokalt redundant och zon redundant lagring.

Lagringskonton för allmän användning version 2 (GPv2) innehåller ytterligare två redundansalternativ som inte stöds av Azure-filer: läs tillgänglig geoupptundit lagring, ofta kallad RA-GRS, och läs tillgänglig geo-zonundant lagring, ofta kallas RA-GZRS. Du kan etablera Azure-filresurser i lagringskonton med dessa alternativuppsättning, men Azure Files stöder inte läsning från den sekundära regionen. Azure-filresurser som distribueras till läs-tillgängliga geo- eller geozons redundanta lagringskonton faktureras som geo-redundant eller geo-zon-redundant lagring, respektive.