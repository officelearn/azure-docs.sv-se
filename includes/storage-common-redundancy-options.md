---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77157234"
---
Redundansalternativ för ett lagringskonto inkluderar:

* Lokalt redundant lagring (LRS): En enkel, billig redundansstrategi. Data kopieras synkront tre gånger inom den primära regionen.
* Zonupptraktad lagring (ZRS): Redundans för scenarier som kräver hög tillgänglighet. Data kopieras synkront över tre Azure-tillgänglighetszoner i den primära regionen.
* Geouppsagbar lagring (GRS): Gränsöverskridande redundans för att skydda mot regionala avbrott. Data kopieras synkront tre gånger i den primära regionen och kopieras sedan asynkront till den sekundära regionen. För läsåtkomst till data i den sekundära regionen aktiverar du geo redundant lagring med läsåtkomst (RA-GRS).
* Geo-zon-redundant lagring (GZRS) (förhandsgranskning): Redundans för scenarier som kräver både hög tillgänglighet och maximal hållbarhet. Data kopieras synkront över tre Azure-tillgänglighetszoner i den primära regionen och kopieras sedan asynkront till den sekundära regionen. För läsåtkomst till data i den sekundära regionen aktiverar du geo-zon redundant lagring (läsåtkomst geografisk zon redundant lagring).For read access to data in the secondary region, enable read-access geo-zone-redundant storage (RA-GZRS).

Mer information om redundansalternativ i Azure Storage finns i [Azure Storage redundans](../articles/storage/common/storage-redundancy.md).
