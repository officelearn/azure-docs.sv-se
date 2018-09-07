---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4a43966180850645584043690b1be9d6ae232f6e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027455"
---
Zonredundant lagring (ZRS) replikerar dina data synkront i tre lagringskluster i en enda region. Varje lagringskluster är fysiskt avgränsade från de andra och ligger i sin egen tillgänglighetszon (AZ). Varje tillgänglighetszon och ZRS-klustret i denna är autonoma med olika verktyg och nätverksfunktioner.

Lagra data i ett ZRS-konto säkerställer att du kan få åtkomst till och hantera dina data i händelse av att en zon blir otillgänglig. ZRS ger utmärkt prestanda och låg fördröjning. ZRS ger samma [skalbarhetsmål](../articles/storage/common/storage-scalability-targets.md) som [lokalt redundant lagring (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Överväg ZRS för scenarier som kräver stark konsekvens, stark hållbarhet och hög tillgänglighet, även om ett avbrott eller en naturkatastrof återger ett zonindelad Datacenter inte tillgänglig. ZRS ger hållbarhet för lagringsobjekt på minst 99,9999999999% (12 9) under ett givet år.

Läs mer om tillgänglighetszoner [Tillgänglighetszoner översikt](https://docs.microsoft.com/azure/availability-zones/az-overview).