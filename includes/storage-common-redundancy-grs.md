---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: 922f4d5dd8c71bc9365523b1d539d1b0754fff15
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580947"
---
GEO-redundant lagring (GRS) är utformad för att tillhandahålla minst 99,99999999999999% (16 9) objektshållbarhet under ett givet år genom att replikera dina data till en sekundär region som ligger hundratals mil bort från den primära regionen. Om ditt lagringskonto har GRS aktiverat, sedan dina data är beständiga även vid en fullständig regionalt strömavbrott eller en katastrof där den primära regionen inte kan återställas.

Om du väljer för GRS har två relaterade alternativ att välja mellan:

* GRS replikerar dina data till ett annat datacenter i en sekundär region utan att data kan vara skrivskyddad om Microsoft initierar en växling från primär till sekundär region. 
* Läsåtkomst till geografiskt redundant lagring (RA-GRS) är baserad på GRS. RA-GRS replikerar data till ett annat datacenter i en sekundär region och ger dig också möjlighet att läsa från den sekundära regionen. Du kan läsa från sekundärt oavsett om Microsoft initierar en växling från primär till sekundär med RA-GRS. 

Alla data för ett lagringskonto med GRS eller RA-GRS aktiveras replikeras först med lokalt redundant lagring (LRS). En uppdatering först är allokerad till den primära platsen som replikeras med LRS. Uppdateringen replikeras sedan asynkront till den sekundära regionen som med GRS. När data skrivs till den sekundära platsen, replikeras det också på den platsen med hjälp av LRS. 

Båda primära och sekundära regionerna hantera repliker i separata feldomäner och uppgraderingsdomäner i en lagringsskalningsenhet. Skala lagringsenheten är grundläggande kvarhållningsenheten inom datacentret. Replikering på den här nivån kommer från LRS; Mer information finns i [lokalt redundant lagring (LRS): låg kostnad dataredundans för Azure Storage](../articles/storage/common/storage-redundancy-lrs.md).

Ha de här punkterna i åtanke när du bestämmer vilket replikeringsalternativ du använder:

* Zonredundant lagring (ZRS) ger hög tillgänglighet med synkron replikering och kan vara ett bättre alternativ för vissa scenarier än GRS eller RA-GRS. Mer information om ZRS finns i [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Eftersom asynkron replikering innebär en fördröjning, i händelse av ett regionalt haveri är det möjligt att ändringar som ännu inte har replikerats till den sekundära regionen kommer att försvinna om data inte kan återställas från den primära regionen.
* Med GRS finns repliken inte om inte Microsoft initierar redundans till den sekundära regionen. Om Microsoft påbörja en växling till den sekundära regionen, du har läs- och skrivåtkomst till dessa data efter växlingen har slutförts. Mer information finns i [vägledning om Haveriberedskap](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Om programmet behöver för att läsa från den sekundära regionen, aktivera RA-GRS.