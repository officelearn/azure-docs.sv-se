---
title: Lagring av container avbildning
description: Information om hur dina Docker-behållar avbildningar lagras i Azure Container Registry, inklusive säkerhet, redundans och kapacitet.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: f66c3dd95edfe5035c46857cb6f9aa59d8a6a0e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74456201"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Lagrings avbildnings lagring i Azure Container Registry

Alla fördelar med Azure Container Registry för [Basic, standard och Premium](container-registry-skus.md) från avancerade Azure Storage-funktioner som kryptering i vila för avbildnings data säkerhet och GEO-redundans för avbildnings data skydd. I följande avsnitt beskrivs både funktionerna och gränserna för avbildnings lagring i Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Kryptering vid vila

Alla behållar avbildningar i registret är krypterade i vila. Azure krypterar automatiskt en avbildning innan den lagras och dekrypterar den direkt när du eller dina program och tjänster hämtar avbildningen.

## <a name="geo-redundant-storage"></a>Geografiskt redundant lagring

Azure använder ett Geo-redundant lagrings schema för att skydda mot förlust av behållar avbildningar. Azure Container Registry replikerar automatiskt dina behållar avbildningar till flera geografiskt avlägsnade Data Center, vilket förhindrar förlust i händelse av ett regionalt lagrings haveri.

## <a name="geo-replication"></a>Geo-replikering

För scenarier som kräver ännu mer hög tillgänglighets säkerhet kan du överväga att använda funktionen för [geo-replikering](container-registry-geo-replication.md) i Premium register. Geo-replikering hjälper till att förlora åtkomsten till registret i händelse av ett *totalt* regionalt haveri, inte bara ett lagrings haveri. Geo-replikering ger även andra fördelar, t. ex. nätverks nära avbildnings lagring för snabbare push-meddelanden och hämtningar i distribuerade utvecklings-eller distributions scenarier.

## <a name="image-limits"></a>Bild gränser

I följande tabell beskrivs behållar avbildningen och lagrings gränserna för Azure Container register.

| Resurs | Gräns |
| -------- | :---- |
| Centrallager | Obegränsad |
| Avbildningar | Obegränsad |
| Skikt | Obegränsad |
| Taggar | Obegränsad|
| Storage | 5 TB |

Ett stort antal databaser och taggar kan påverka prestanda för registret. Ta regelbundet bort oanvända databaser, taggar och avbildningar som en del av din rutin för register underhåll. Borttagna register resurser som Arkiv, bilder och taggar *kan inte* återställas efter borttagning. Mer information om hur du tar bort register resurser finns [i ta bort behållar avbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Lagringskostnad

Fullständig information om priser finns i [Azure Container Registry prissättning][pricing].

## <a name="next-steps"></a>Nästa steg

Mer information om de olika Azure Container Registry SKU: er (Basic, standard, Premium) finns i [Azure Container Registry SKU: er](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
