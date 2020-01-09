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
ms.openlocfilehash: 6707e844948ac76d4cec29faf69d80b3c9cb3c0f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392293"
---
| Resurs | Basic | Standard | Premium |
|---|---|---|---|
| Lagring<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Största bild skikt storlek | 200 GiB | 200 GiB | 200 GiB |
| ReadOps per minut<sup>2, 3</sup> | 1,000 | 3 000 | 10 000 |
| WriteOps per minut<sup>2, 4</sup> | 100 | 500 | 2,000 |
| Ladda ned bandbredd MBps<sup>2</sup> | 30 | 60 | 100 |
| Ladda upp bandbredd MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Geo-replikering | Gäller inte | Gäller inte | [Stöds][geo-replication] |
| Förtroende för innehåll | Gäller inte | Gäller inte | [Stöds][content-trust] |
| Åtkomst till virtuellt nätverk | Gäller inte | Gäller inte | [Förhandsversion][vnet] |
| Databas – begränsade behörigheter | Gäller inte | Gäller inte | [Förhandsversion][token]|
| &bull; tokens | Gäller inte | Gäller inte | 20,000 |
| &bull; omfångs kartor | Gäller inte | Gäller inte | 20,000 |
| Mappning av &bull; databaser per område | Gäller inte | Gäller inte | 500 |


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