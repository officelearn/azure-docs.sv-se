---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d25efa8c666fa91c7bd652a7cf931ea6ca379aa2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133023"
---
Zonredundant lagring (ZRS) replikerar dina data synkront i tre lagringskluster i en enda region. Varje lagringskluster är fysiskt avgränsade från de andra och finns i sin egen tillgänglighetszon (AZ). Varje tillgänglighetszon&mdash;och ZRS-klustret i den&mdash;är autonoma och inkluderar olika verktyg och funktioner. En skrivbegäran till ett ZRS-lagringskonto returnerar har bara när data skrivs till alla repliker över de tre kluster.

När du lagrar dina data i ett lagringskonto med ZRS-replikering kan fortsätta du att komma åt och hantera dina data om en tillgänglighetszon blir otillgänglig. ZRS ger utmärkt prestanda och låg fördröjning. ZRS ger samma [skalbarhetsmål](../articles/storage/common/storage-scalability-targets.md) som [lokalt redundant lagring (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Överväg ZRS för scenarier som kräver konsekvens, hållbarhet och hög tillgänglighet. Även om ett avbrott eller en naturkatastrof visas med en tillgänglighetszon otillgänglig, ZRS ger hållbarhet för lagringsobjekt på minst 99,9999999999% (12 9) under ett givet år.

Läs mer om tillgänglighetszoner [Tillgänglighetszoner översikt](https://docs.microsoft.com/azure/availability-zones/az-overview).