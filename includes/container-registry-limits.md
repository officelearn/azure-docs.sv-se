---
title: ta med fil
description: ta med fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 11/05/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: bc4ac68cb415a43ac34d36afc2adc30307e6d37c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795246"
---
| Resurs | Basic | Standard | Premium |
|---|---|---|---|
| Lagring<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Största bild skikt storlek | 200 GiB | 200 GiB | 200 GiB |
| ReadOps per minut<sup>2, 3</sup> | 1,000 | 3 000 | 10 000 |
| WriteOps per minut<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Ladda ned bandbredd MBps<sup>2</sup> | 30 | 60 | 100 |
| Ladda upp bandbredd MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Geo-replikering | Saknas | Saknas | [Stöds][geo-replication] |
| Förtroende för innehåll | Saknas | Saknas | [Stöds][content-trust] |
| Åtkomst till virtuellt nätverk | Saknas | Saknas | [Förhandsversion][vnet] |
| Databas – begränsade behörigheter | Saknas | Saknas | [Förhandsversion][token]|
| &bull; tokens | Saknas | Saknas | 20 000 |
| &bull; omfångs kartor | Saknas | Saknas | 20 000 |
| Mappning av &bull; databaser per område | Saknas | Saknas | 500 |


<sup>1</sup> De angivna lagrings gränserna är mängden lagrings utrymme som *ingår* för varje nivå. Du debiteras ytterligare en daglig taxa per GiB för avbildnings lagring över dessa gränser. Mer information om priser finns i [Azure Container Registry prissättning][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*och *bandbredd* är minimala uppskattningar. Azure Container Registry strävar efter att förbättra prestandan när användningen kräver.

<sup>3</sup> En [Docker-pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) översätter sig till flera Läs åtgärder baserat på antalet lager i bilden, plus manifest hämtning.

<sup>4</sup> En [Docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) översätts till flera Skriv åtgärder baserat på antalet lager som måste flyttas. En `docker push` innehåller *ReadOps* för att hämta ett manifest för en befintlig avbildning.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md