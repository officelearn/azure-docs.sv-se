---
title: Lagring av container avbildning
description: Information om hur dina Docker-behållar avbildningar lagras i Azure Container Registry, inklusive säkerhet, redundans och kapacitet.
ms.topic: article
ms.date: 06/18/2020
ms.openlocfilehash: d51014e9e0769091aba42682cce3a6a01cfa19de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214068"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Lagrings avbildnings lagring i Azure Container Registry

Alla fördelar med Azure Container Registry för [Basic, standard och Premium](container-registry-skus.md) från avancerade Azure Storage-funktioner som kryptering i vila för avbildnings data säkerhet och GEO-redundans för avbildnings data skydd. I följande avsnitt beskrivs både funktionerna och gränserna för avbildnings lagring i Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Kryptering vid vila

Alla behållar avbildningar i registret är krypterade i vila. Azure krypterar automatiskt en avbildning innan den lagras och dekrypterar den direkt när du eller dina program och tjänster hämtar avbildningen. Du kan också använda ytterligare ett krypterings lager med en [kundhanterad nyckel](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Geografiskt redundant lagring

Azure använder ett Geo-redundant lagrings schema för att skydda mot förlust av behållar avbildningar. Azure Container Registry replikerar automatiskt dina behållar avbildningar till flera geografiskt avlägsnade Data Center, vilket förhindrar förlust i händelse av ett regionalt lagrings haveri.

## <a name="geo-replication"></a>Geo-replikering

För scenarier som kräver ännu mer hög tillgänglighets säkerhet kan du överväga att använda funktionen för [geo-replikering](container-registry-geo-replication.md) i Premium register. Geo-replikering hjälper till att förlora åtkomsten till registret i händelse av ett *totalt* regionalt haveri, inte bara ett lagrings haveri. Geo-replikering ger även andra fördelar, t. ex. nätverks nära avbildnings lagring för snabbare push-meddelanden och hämtningar i distribuerade utvecklings-eller distributions scenarier.

## <a name="scalable-storage"></a>Skalbar lagring

Med Azure Container Registry kan du skapa så många databaser, bilder, lager eller taggar som du behöver, upp till [registrets lagrings gräns](container-registry-skus.md#service-tier-features-and-limits). 

Ett stort antal databaser och taggar kan påverka prestanda för registret. Ta regelbundet bort oanvända databaser, taggar och avbildningar som en del av din rutin för register underhåll och om du vill kan du ange en [bevarande princip](container-registry-retention-policy.md) för otaggade manifest. Borttagna register resurser som lagrings platser, bilder och taggar *kan inte* återställas efter borttagning. Mer information om hur du tar bort register resurser finns [i ta bort behållar avbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Lagringskostnad

Fullständig information om priser finns i [Azure Container Registry prissättning][pricing].

## <a name="next-steps"></a>Nästa steg

Mer information om register för standard-, standard-och Premium-behållare finns i [Azure Container Registry tjänst nivåer](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
