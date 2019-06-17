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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66148965"
---
| Resource | Basic | Standard | Premium |
|---|---|---|---|
| Storage<sup>1</sup> | 10 GiB | 100 giB| 500 giB |
| Maximal bildstorlek för lager | 200 GiB | 200 GiB | 200 GiB |
| ReadOps per minut<sup>2, 3</sup> | 1,000 | 3,000 | 10 000 |
| WriteOps per minut<sup>2, 4</sup> | 100 | 500 | 2,000 |
| Ladda ned bandbredd Mbit/s<sup>2</sup> | 30 | 60 | 100 |
| Ladda upp bandbredd Mbit/s<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Geo-replikering | Gäller inte | Gäller inte | [Stöds][geo-replication] |
| Förtroende för innehåll | Gäller inte | Gäller inte | [Stöds][content-trust] |

<sup>1</sup>angivna lagringsgränserna är mängden *ingår* lagring för varje nivå. Du debiteras en ytterligare daglig avgift per GiB för lagring av avbildningen över gränserna. Priset information finns i [priser för Azure Container Registry][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*, och *bandbredd* är minsta beräkningar. Azure Container Registry strävar efter att förbättra prestanda som kräver användning.

<sup>3</sup>A [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) översätts till flera läsåtgärder baserat på antalet lager i avbildningen, plus manifest hämtning.

<sup>4</sup>A [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) översätts till flera skrivåtgärder, baserat på antalet lager som måste skickas. En `docker push` innehåller *ReadOps* att hämta ett manifest för en befintlig avbildning.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
