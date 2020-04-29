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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77157234"
---
Alternativ för redundans för ett lagrings konto är:

* Lokalt redundant lagring (LRS): en enkel, låg kostnads redundans strategi. Data kopieras synkront tre gånger inom den primära regionen.
* Zone-redundant lagring (ZRS): redundans för scenarier som kräver hög tillgänglighet. Data kopieras synkront över tre tillgänglighets zoner i Azure i den primära regionen.
* Geo-redundant lagring (GRS): Cross-regional redundans för att skydda mot regionala avbrott. Data kopieras synkront tre gånger i den primära regionen och kopieras sedan asynkront till den sekundära regionen. För Läs åtkomst till data i den sekundära regionen aktiverar du Geo-redundant lagring med Läs behörighet (RA-GRS).
* Geo-Zone-redundant lagring (GZRS) (för hands version): redundans för scenarier som kräver både hög tillgänglighet och maximal hållbarhet. Data kopieras synkront över tre tillgänglighets zoner i Azure i den primära regionen och kopieras sedan asynkront till den sekundära regionen. För Läs åtkomst till data i den sekundära regionen aktiverar du Läs åtkomst geo-Zone-redundant lagring (RA-GZRS).

Mer information om alternativ för redundans i Azure Storage finns [Azure Storage redundans](../articles/storage/common/storage-redundancy.md).
