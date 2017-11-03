---
title: Azure-stacken behandling av princip | Microsoft Docs
description: "Läs mer om Azure-stacken behandling av princip för och hur du hålla ett integrerat system i ett läge som stöds."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: f2c99f19b30d2cdfdf65dea6dd3909b88ffc15dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-servicing-policy"></a>Azure-stacken behandling av princip

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskriver Underhåll princip för Azure-stacken integrerat system och vad du måste göra för att hålla datorn i ett läge som stöds. 

## <a name="update-package-types"></a>Uppdatera pakettyper

Det finns två typer av uppdateringspaket för integrerad system. Microsoft-programuppdateringar och uppdateringar som är specifika för leverantören OEM-tillverkaren (OEM), till exempel drivrutiner och inbyggd programvara. Dessa uppdateringar levereras som separata Azure Stack-uppdateringspaket och hanteras oberoende av varandra.

- **Microsoft-programuppdateringar**. Microsoft ansvarar för slutpunkt till slutpunkt Underhåll livscykeln för uppdateringspaket för Microsoft-program. Dessa paket kan innehålla senaste säkerhetsuppdateringarna från Windows Server, icke-säkerhetsuppdateringar och funktionsuppdateringar för Azure-stacken. Du kan hämta de här uppdateringspaket direkt från Microsoft.
- **OEM-tillverkarens maskinvaruuppdateringar**. Azure Stack maskinvarupartners ansvarar för slutpunkt till slutpunkt Underhåll livscykeln (inklusive vägledning) för maskinvarurelaterade inbyggd programvara och uppdatera drivrutinspaket. Dessutom Azure Stack maskinvarupartners äger och underhåll för alla program- och maskinvara maskinvara livscykel värden. Maskinvaruleverantören OEM-värd för dessa uppdateringspaket på sina egna hämtningsplatsen.

## <a name="update-package-release-cadence"></a>Uppdatera paketet versionen takt

Microsoft räknar med att släppa programuppdateringspaket i en månatlig takt. Det är dock möjligt att ha flera och update-versioner i en månad. OEM-maskinvaruleverantörer släpp uppdateringarna som behövs.

Ett Microsoft update-paket har följande namngivningskonvention för att enkelt identifiera lanseringsdatumet:

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

Till exempel skulle en Microsoft-programuppdatering som gavs ut den 15 juni 2017 ha version ”1.0.170615.1”.

## <a name="keep-your-system-under-support"></a>Hålla systemet under stöd

För att få support för ditt system, måste du behålla din Azure-stacken uppdateras inom ett visst tidsintervall. Vår policy för avstängning av Microsoft-programuppdateringar är tre månader. Om datorn är mer än tre månader inaktuell, är du betraktas som inkompatibel. Du måste uppdatera systemet till åtminstone den minimal stödd version få support. 

Microsoft programuppdateringspaket är kumulativ och kräver tidigare uppdateringspaketet som ett krav. Om du vill skjuta upp en eller flera uppdateringar du övergripande runtime om du vill hämta till den senaste versionen.

I följande tabell visas exempel uppdatera paketet versioner och deras nödvändiga den lägsta version som stöds som datorn måste vara på för att stöda. Tabellen är baserad på den första versionen av Azure-stacken integrerat system (build 1708), med den första uppdatering paketet versionen (1709) i September 2017. 

| Senaste uppdateringspaketet (*exempel*) | Krav | Lägsta Version som stöds |
| -- | -- | -- |
| 1709 | Skapa 1708 | Saknas |
| 1710 | 1709 | Saknas |
| 1711 | 1710 | Saknas |
| 1712 | 1711 | 1709 |
| 1801 | 1712 | 1710 |
| 1802 | 1801 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1801 |
| | | 

## <a name="next-steps"></a>Nästa steg

- [Hantera uppdateringar i Azure-stacken](azure-stack-updates.md)


