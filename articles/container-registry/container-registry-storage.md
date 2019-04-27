---
title: Bildlagring i Azure Container Registry
description: Information om hur din Docker-behållaravbildningar lagras i Azure Container Registry, inklusive säkerhet, redundans och kapacitet.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: danlep
ms.openlocfilehash: 55c84907ab41f6da9d7a0989c68a1c1f90c5e424
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827280"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Bildlagring för behållare i Azure Container Registry

Varje [Basic, Standard och Premium](container-registry-skus.md) Azure container registry fördelar från avancerade Azure-lagringsfunktionerna som kryptering i vila för avbildning datasäkerhet och georedundans för dataskydd för avbildningen. I följande avsnitt beskrivs både funktionerna och begränsningarna för bildlagring i Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Kryptering i vila

Alla avbildningar i registret är krypterade i vila. Azure automatiskt krypterar en avbildning innan de lagras och dekrypterar dem på direkt när du eller dina program och tjänster kan du hämta avbildningen.

## <a name="geo-redundant-storage"></a>Geografiskt redundant lagring

Azure använder ett schema för geo-redundant lagring för att skydda mot förlust av dina behållaravbildningar. Azure Container Registry replikerar automatiskt dina behållaravbildningar till flera geografiskt avlägsna Datacenter förhindrar sina går förlorade i händelse av ett regionalt storage-fel.

## <a name="geo-replication"></a>Geo-replikering

För scenarier som kräver ytterligare trygghet för hög tillgänglighet kan du överväga att använda den [geo-replikering](container-registry-geo-replication.md) funktion i Premium-register. GEO-replikering hjälper till att förhindra att förlora åtkomsten till ditt register i händelse av en *totala* regionala fel, inte bara ett storage-fel. GEO-replikering har andra fördelar, som bild nätverksnära lagring för snabbare push-meddelanden och hämtar i distribuerade scenarier för utveckling eller distribution.

## <a name="image-limits"></a>Bild gränser

I följande tabell beskrivs behållare avbildningen och lagringsgränser för Azure-behållarregister.

| Resurs | Gräns |
| -------- | :---- |
| Centrallager | Obegränsad |
| Avbildningar | Obegränsad |
| Lager | Obegränsad |
| Taggar | Obegränsad|
| Storage | 5 TB |

Mycket stort antal databaser och taggar kan påverka prestanda för ditt register. Regelbundet ta bort oanvända databaser, taggar och bilder som en del av din rutinen för underhåll av registret. Ta bort registret resurser som databaser, avbildningar och taggar *kan* återställas efter borttagningen. Läs mer om att ta bort Registerresurser [ta bort avbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Lagringskostnad

Fullständig information om priser finns [priser för Azure Container Registry][pricing].

## <a name="next-steps"></a>Nästa steg

Läs mer om de olika Azure Container Registry SKU: er (Basic, Standard, Premium), [Azure Container Registry SKU: er](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
