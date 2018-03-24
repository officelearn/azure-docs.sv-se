---
title: Bildlagring i registret för Azure-behållare
description: Information om hur dina Docker behållare avbildningar lagras i registret för Azure-behållare, inklusive säkerhet, redundans och kapacitet.
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: marsma
ms.openlocfilehash: df46712889a3eba54f1a2288ba93c82b21b92deb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="container-image-storage-in-azure-container-registry"></a>Behållaren bildlagring i registret för Azure-behållare

Varje [Basic, Standard och Premium](container-registry-skus.md) Azure-behållaren registret fördelar från Azure storage för avancerade funktioner som kryptering i vila för avbildningen datasäkerhet och geo-redundans för dataskydd för avbildningen. I följande avsnitt beskrivs både funktioner och begränsningar för bildlagring i Azure Container registret (ACR).

## <a name="encryption-at-rest"></a>Kryptering i vila

Alla behållare bilder i registret är krypterat i vila. Azure automatiskt krypterar en avbildning innan de lagras och dekrypterar dem på direkt när du eller ditt program och tjänster pull-avbildningen.

## <a name="geo-redundant-storage"></a>Geografiskt redundant lagring.

Azure använder ett schema för geo-redundant lagring som skyddar mot förlust av bilderna behållare. Azure Container registret replikerar automatiskt bilderna behållare till flera geografiskt avlägsna Datacenter hindrar sina går förlorade vid fel regionala lagring.

## <a name="geo-replication"></a>Geo-replikering

För scenarier som kräver ytterligare säkerhet för hög tillgänglighet kan du använda den [georeplikering](container-registry-geo-replication.md) funktion i Premium register. GEO-replikering hjälper till att förhindra att förlora åtkomsten till registret i händelse av en *totala* regionala fel, inte bara ett storage-fel. GEO-replikering har andra fördelar, som nätverket Stäng bild lagring för snabbare skickar och tar emot distribuerade scenarier för utveckling eller distribution.

## <a name="image-limits"></a>Bild gränser

I följande tabell beskrivs behållaren avbildningen och lagringsgränser för Azure-behållaren register.

| Resurs | Gräns |
| -------- | :---- |
| Centrallager | Obegränsat |
| Avbildningar | Obegränsat |
| Lager | Obegränsat |
| Taggar | Obegränsat|
| Lagring | 5 TB |

Mycket stort antal databaser och taggar kan påverka prestanda för registret. Regelbundet ta bort oanvända databaser, taggar och bilder med hjälp av den [Azure CLI](/cli/azure/acr), ACR [REST API](/rest/api/containerregistry/), eller [Azure-portalen] [ portal] som en del av din rutin för underhåll av registret. Ta bort registernyckeln resurser, t.ex. databaser, avbildningar och taggar *kan* återställas efter borttagningen.

## <a name="storage-cost"></a>Kostnad för datalagring

Fullständig information om priser finns i [priser för Azure-behållare registret][pricing].

## <a name="next-steps"></a>Nästa steg

Läs mer om de olika Azure-behållare registret SKU: er (Basic, Standard, Premium) [Azure Container registret SKU: er](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: http://aka.ms/acr/pricing

<!-- LINKS - Internal -->
