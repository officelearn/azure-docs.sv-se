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
ms.openlocfilehash: ac3e87d7f921da2c1089eb6f2c7e61fc2c432f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463927"
---
Zone-redundant lagring (ZRS) replikerar dina data synkront över tre lagrings kluster i en enda region. Varje lagrings kluster separeras fysiskt från de andra och finns i en egen tillgänglighets zon (AZ). Varje tillgänglighets zon&mdash;och ZRS-klustret inom IT-&mdash;är autonomt och innehåller separata verktyg och nätverksfunktioner. En skrivbegäran till ett ZRS-lagrings konto returneras bara efter att data har skrivits till alla repliker i de tre klustren.

När du lagrar data i ett lagrings konto med ZRS-replikering kan du fortsätta att komma åt och hantera dina data om en tillgänglighets zon blir otillgänglig. ZRS ger utmärkta prestanda och låg latens. ZRS erbjuder samma skalbarhets mål som [Lokalt Redundant lagring (LRS)](../articles/storage/common/storage-redundancy-lrs.md). Mer information om skalbarhets mål för standard lagrings konton finns i [skalbarhets mål för standard lagrings konton](../articles/storage/common/scalability-targets-standard-account.md).

Överväg ZRS för scenarier som kräver konsekvens, hållbarhet och hög tillgänglighet. Även om ett avbrott eller en natur katastrof återger en tillgänglighets zon som inte är tillgänglig, ger ZRS tålighet för lagrings objekt på minst 99,9999999999% (12 9) under ett givet år.

Geo-Zone-redundant lagring (GZRS) (för hands version) replikerar dina data synkront över tre tillgänglighets zoner i Azure i den primära regionen och replikerar sedan data asynkront till den sekundära regionen. GZRS ger hög tillgänglighet tillsammans med maximal hållbarhet. GZRS har utformats för att ge minst 99.99999999999999% (16 9) objekts hållbarhet under ett angivet år. För Läs åtkomst till data i den sekundära regionen aktiverar du Läs åtkomst geo-Zone-redundant lagring (RA-GZRS). Mer information om GZRS finns i [geo-Zone-redundant lagring för hög tillgänglighet och maximal hållbarhet (för hands version)](../articles/storage/common/storage-redundancy-gzrs.md).

Mer information om tillgänglighets zoner finns i [Tillgänglighetszoner översikt](https://docs.microsoft.com/azure/availability-zones/az-overview).
