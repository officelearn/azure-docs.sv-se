---
title: inkludera fil
description: inkludera fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 643cf8fd400adf06bf61f070947bd78ba7be50eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85242227"
---
| Resurs | Basic | Standard | Premium |
|---|---|---|---|
| Ingår lagring<sup>1</sup> (GIB) | 10 | 100 | 500 |
| Lagrings gräns (TiB) | 20| 20 | 20 |
| Maximal bild skikt storlek (GiB) | 200 | 200 | 200 |
| ReadOps per minut<sup>2, 3</sup> | 1,000 | 3 000 | 10 000 |
| WriteOps per minut<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Ladda ned bandbredd MBps<sup>2</sup> | 30 | 60 | 100 |
| Ladda upp bandbredd MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Geo-replikering | E.t. | E.t. | [Stöds][geo-replication] |
| Förtroende för innehåll | E.t. | E.t. | [Stöds][content-trust] |
| Privat länk med privata slut punkter | E.t. | E.t. | [Stöds][plink] |
| Åtkomst till tjänst slut punktens VNet | E.t. | E.t. | [Förhandsgranskning][vnet] |
| Kundhanterade nycklar | E.t. | E.t. | [Stöds][cmk] |
| Databas – begränsade behörigheter | E.t. | E.t. | [Förhandsgranskning][token]|
| &bull;Token | E.t. | E.t. | 20 000 |
| &bull;Omfångs kartor | E.t. | E.t. | 20 000 |
| &bull;Mappning av databaser per område | E.t. | E.t. | 500 |


<sup>1</sup> lagring ingår i den dagliga taxan för varje nivå. För ytterligare lagring debiteras du ytterligare en daglig taxa per GiB, upp till lagrings gränsen. Mer information om priser finns i [Azure Container Registry prissättning][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*och *bandbredd* är minimala uppskattningar. Azure Container Registry strävar efter att förbättra prestandan när användningen kräver.

<sup>3</sup> En [Docker-pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) översätter sig till flera Läs åtgärder baserat på antalet lager i bilden, plus manifest hämtning.

<sup>4</sup> En [Docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) översätts till flera Skriv åtgärder baserat på antalet lager som måste flyttas. En `docker push` innehåller *ReadOps* för att hämta ett manifest för en befintlig avbildning.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md