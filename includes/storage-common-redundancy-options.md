---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5df0fe23183b53f13d6c6545e248724324434cde
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029870"
---
Replikeringsalternativ för ett lagringskonto är bland annat:

* [Lokalt Redundant lagring (LRS)](../articles/storage/common/storage-redundancy-lrs.md): En enkel och låg kostnads strategi för replikering. Data replikeras synkront tre gånger inom den primära regionen.
* [Zon-redundant lagring (ZRS)](../articles/storage/common/storage-redundancy-zrs.md): Replikering för scenarier som kräver hög tillgänglighet. Data replikeras synkront över tre tillgänglighets zoner i Azure i den primära regionen.
* [Geo-redundant lagring (GRS)](../articles/storage/common/storage-redundancy-grs.md): Replikering mellan regioner för att skydda mot regionala avbrott. Data replikeras synkront tre gånger i den primära regionen, sedan replikeras asynkront till den sekundära regionen. För Läs åtkomst till data i den sekundära regionen aktiverar du Geo-redundant lagring med Läs behörighet (RA-GRS).
* [GZRS (geo-Zone-redundant lagring) (för hands version)](../articles/storage/common/storage-redundancy-gzrs.md): Replikering för scenarier som kräver både hög tillgänglighet och maximal hållbarhet. Data replikeras synkront över tre tillgänglighets zoner i Azure i den primära regionen, sedan replikeras asynkront till den sekundära regionen. För Läs åtkomst till data i den sekundära regionen aktiverar du Läs åtkomst geo-Zone-redundant lagring (RA-GZRS).
