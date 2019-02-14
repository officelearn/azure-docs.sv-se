---
title: ta med fil
description: ta med fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/30/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: fe1227d91c0d039a94e5b9a3046b879f4f17355a
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56247170"
---
| Resurs | Basic | Standard | Premium |
|---|---|---|---|---|
| Storage<sup>1</sup> | 10 GiB | 100 giB| 500 giB |
| Maximal bildstorlek lager | 20 GiB | 20 GiB | 50 giB |
| ReadOps per minut<sup>2, 3</sup> | 1,000 | 3 000 | 10 000 |
| WriteOps per minut<sup>2, 4</sup> | 100 | 500 | 2,000 |
| Ladda ned bandbredd Mbit/s<sup>2</sup> | 30 | 60 | 100 |
| Ladda upp bandbredd Mbit/s<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Geo-replikering | Gäller inte | Gäller inte | [Stöds][geo-replication] |
| Innehållsförtroende (förhandsversion) | Gäller inte | Gäller inte | [Stöds][content-trust] |

<sup>1</sup> angivna lagringsgränserna är mängden *ingår* lagring för varje nivå. Du debiteras en ytterligare daglig avgift per GiB för lagring av avbildningen över gränserna. Priset information finns i [prissättning för Container Registry][pricing].

<sup>2</sup> *ReadOps*, *WriteOps*, och *bandbredd* är minsta beräkningar. ACR strävar efter att förbättra prestanda som kräver användning.

<sup>3</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) översätts till flera läsåtgärder baserat på antalet lager i avbildningen, plus manifest hämtning.

<sup>4</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) översätts till flera skrivåtgärder, baserat på antalet lager som måste skickas. En `docker push` innehåller *ReadOps* att hämta ett manifest för en befintlig avbildning.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
