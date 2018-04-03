---
title: Azure-stacken behandling av princip | Microsoft Docs
description: Läs mer om Azure-stacken behandling av princip för och hur du hålla ett integrerat system i ett läge som stöds.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mabrigg
ms.openlocfilehash: 2c10dcf185c62f3672be80ad2e3d049eae82fe6b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-servicing-policy"></a>Azure-stacken behandling av princip
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
Om du vill fortsätta få support, måste du behålla distributionen av Azure-stacken aktuella. Principen för avstängning av uppdateringar är att för Azure-stacken hålls kvar i stöd, måste kör den uppdaterade versionen som nyligen utgivna eller köra något av två föregående större Uppdateringsversioner.  Snabbkorrigeringar inte anses vara viktiga Uppdateringsversioner.  Om ditt Azure Stack-moln som skyddas av *fler än två uppdateringar*, den anses vara inkompatibel och måste uppdatera till minst den lägsta versionen som stöds för att få support. 

Till exempel om den senaste tillgängliga uppdateringsversionen är 1805 och de föregående två uppdateringspaket har version 1804 och 1803, både 1803 1804 finnas kvar i stöd. 1802 är dock inte längre stöds. Principen gäller när det finns inga versionen för en månad eller två. Till exempel om den aktuella versionen är 1805 och det fanns inga 1804 versionen, skulle de föregående två uppdateringspaket 1803 och 1802 kvar i stöd.

Microsoft programuppdateringspaket är kumulativ och kräver tidigare uppdateringspaketet som ett krav. Om du vill skjuta upp en eller flera uppdateringar du övergripande runtime om du vill hämta till den senaste versionen. 

I följande tabell visas exempel uppdatera paketet versioner och deras nödvändiga den lägsta version som stöds som datorn måste vara på för att stöda. Tabellen är baserad på den första versionen av Azure-stacken integrerat system (build 1708), med den första uppdatering paketet versionen (1709) i September 2017. 

| Senaste uppdateringspaketet (*exempel*) | Krav | Lägsta Version som stöds |
| -- | -- | -- |
| 1710 | 1709 | Gäller inte |
| 1711 | 1710 | 1709 |
| 1712 | 1711 | 1710 |
| 1802 | 1712 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1802 |
| 1805 | 1804 | 1803 |
| | | 
Det finns inga versionen 1801 i tabellen ovan.

## <a name="next-steps"></a>Nästa steg

- [Hantera uppdateringar i Azure-stacken](azure-stack-updates.md)


