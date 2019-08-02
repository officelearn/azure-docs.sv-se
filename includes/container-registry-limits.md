---
title: ta med fil
description: ta med fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/14/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ee8ff3529524a63ca2e54a64327570197f363538
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "67187707"
---
| Resource | Basic | Standard | Premium |
|---|---|---|---|
| Lagring<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Största bild skikt storlek | 200 GiB | 200 GiB | 200 GiB |
| ReadOps per minut<sup>2, 3</sup> | 1,000 | 3,000 | 10 000 |
| WriteOps per minut<sup>2, 4</sup> | 100 | 500 | 2,000 |
| Ladda ned bandbredd MBps<sup>2</sup> | 30 | 60 | 100 |
| Ladda upp bandbredd MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhook-konfigurationer | 2 | 10 | 100 |
| Geo-replikering | Gäller inte | Gäller inte | [Stöds][geo-replication] |
| Förtroende för innehåll | Gäller inte | Gäller inte | [Stöds][content-trust] |

<sup>1</sup> De angivna lagrings gränserna är mängden lagrings utrymme som *ingår* för varje nivå. Du debiteras ytterligare en daglig taxa per GiB för avbildnings lagring över dessa gränser. Mer information om priser finns i [Azure Container Registry prissättning][pricing].

<sup>2</sup> *ReadOps*, *WriteOps*och *bandbredd* är minimala uppskattningar. Azure Container Registry strävar efter att förbättra prestandan när användningen kräver.

<sup>3</sup> En [Docker-pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) översätter sig till flera Läs åtgärder baserat på antalet lager i bilden, plus manifest hämtning.

<sup>4</sup> En [Docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) översätts till flera Skriv åtgärder baserat på antalet lager som måste flyttas. En `docker push` innehåller *ReadOps* för att hämta ett manifest för en befintlig avbildning.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
