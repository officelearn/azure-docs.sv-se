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
ms.openlocfilehash: 3aa5310589101fa66fd70cc8d5449fbef80f02fa
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286151"
---
Geo-redundant lagring (GRS) är utformat för att tillhandahålla minst 99.99999999999999% (16 9) objekt hållbarhet för objekt under ett år genom att replikera dina data till en sekundär region som är hundratals mil bort från den primära regionen. Om ditt lagrings konto har GRS aktiverat, är dina data varaktiga även vid ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte går att återvinna.

Om du väljer GRS har du två relaterade alternativ att välja mellan:

* GRS replikerar dina data till ett annat data Center i en sekundär region, men dessa data är tillgängliga för läsning endast om Microsoft initierar en redundansväxling från den primära till den sekundära regionen. 
* Geo-redundant lagring med Läs behörighet (RA-GRS) baseras på GRS. RA-GRS replikerar dina data till ett annat data Center i en sekundär region och ger dig även möjlighet att läsa från den sekundära regionen. Med RA-GRS kan du läsa från den sekundära regionen oavsett om Microsoft initierar en redundansväxling från den primära till den sekundära regionen. 

För ett lagrings konto med GRS eller RA-GRS aktiverat replikeras alla data först med lokalt redundant lagring (LRS). En uppdatering allokeras först till den primära platsen och replikeras med hjälp av LRS. Uppdateringen replikeras sedan asynkront till den sekundära regionen med hjälp av GRS. När data skrivs till den sekundära platsen replikeras de också inom den platsen med hjälp av LRS. 

Både den primära och sekundära regionen hanterar repliker mellan olika fel domäner och uppgraderings domäner inom en lagrings skalnings enhet. Enhetens skalnings enhet är den grundläggande replikeringstjänsten i data centret. Replikering på den här nivån tillhandahålls av LRS; Mer information finns i [lokalt redundant lagring (LRS): Dataredundans med låg kostnad för Azure Storage](../articles/storage/common/storage-redundancy-lrs.md).

Tänk på följande när du bestämmer vilket replikeringsalternativ som ska användas:

* Zone-redundant lagring (ZRS) ger hög tillgänglighet med synkron replikering och kan vara ett bättre alternativ för vissa scenarier än GRS eller RA-GRS. Mer information om ZRS finns i [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Asynkron replikering innebär en fördröjning från den tid som data skrivs till den primära regionen till när den replikeras till den sekundära regionen. I händelse av en regional katastrof kan ändringar som ännu inte har repliker ATS till den sekundära regionen gå förlorade om data inte kan återställas från den primära regionen.
* Med GRS är repliken inte tillgänglig för Läs-eller skriv åtkomst om inte Microsoft initierar en redundansväxling till den sekundära regionen. Om det är en redundansväxling har du Läs-och skriv åtkomst till dessa data när redundansväxlingen har slutförts. Mer information finns i [rikt linjerna för haveri beredskap](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Om ditt program behöver läsa från den sekundära regionen aktiverar du RA-GRS.
