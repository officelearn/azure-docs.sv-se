---
title: Lagring av behållaravbildning
description: Information om hur docker-behållaravbildningar lagras i Azure Container Registry, inklusive säkerhet, redundans och kapacitet.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: f66c3dd95edfe5035c46857cb6f9aa59d8a6a0e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456201"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Lagring av behållaravbildning i Azure Container-registret

Alla [basic-, standard- och Premium](container-registry-skus.md) Azure-behållarregister drar nytta av avancerade Azure-lagringsfunktioner som kryptering i vila för avbildningsdatasäkerhet och georedundans för avbildningsdataskydd. I följande avsnitt beskrivs både funktioner och begränsningar för avbildningslagring i Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Kryptering-i vila

Alla behållaravbildningar i registret krypteras i vila. Azure krypterar automatiskt en avbildning innan du lagrar den och dekrypterar den i farten när du eller dina program och tjänster hämtar avbildningen.

## <a name="geo-redundant-storage"></a>Geografiskt redundant lagring

Azure använder ett geouppsagtiskt lagringsschema för att skydda mot förlust av dina behållaravbildningar. Azure Container Registry replikerar automatiskt dina behållaravbildningar till flera geografiskt avlägsna datacenter, vilket förhindrar att de går förlorade i händelse av ett regionalt lagringsfel.

## <a name="geo-replication"></a>Geo-replikering

Överväg att använda [georeplikeringsfunktionen i Premium-register](container-registry-geo-replication.md) för scenarier som kräver ännu mer hög tillgänglighet. Geo-replikering hjälper till att skydda mot att förlora åtkomst till registret i händelse av ett *totalt* regionalt fel, inte bara ett lagringsfel. Geo-replikering ger andra fördelar också, som nätverksnära avbildningslagring för snabbare pushs och pulls i distribuerade utvecklings- eller distributionsscenarier.

## <a name="image-limits"></a>Bildgränser

I följande tabell beskrivs behållaravbildningen och lagringsgränserna för Azure-behållarregister.

| Resurs | Gräns |
| -------- | :---- |
| Centrallager | Obegränsad |
| Avbildningar | Obegränsad |
| Skikt | Obegränsad |
| Taggar | Obegränsad|
| Lagring | 5 TB |

Mycket många databaser och taggar kan påverka registrets prestanda. Ta regelbundet bort oanvända databaser, taggar och avbildningar som en del av registrets underhållsrutin. Borttagna registerresurser som databaser, avbildningar och taggar *kan inte* återställas efter borttagning. Mer information om hur du tar bort registerresurser finns [i Ta bort behållaravbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Lagringskostnad

Fullständig information om priser finns i [prissättningen för Azure Container Registry][pricing].

## <a name="next-steps"></a>Nästa steg

Mer information om de olika SKU:erna för Azure Container Registry (Basic, Standard, Premium) finns i [Azure Container Registry SKU: er](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
