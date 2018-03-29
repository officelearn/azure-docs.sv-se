---
title: ta med fil
description: ta med fil
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 03/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 575483192954f4e05db50e701e223829e041cffc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
| Resurs | Basic | Standard | Premium |
|---|---|---|---|---|
| Lagring | 10 GiB | 100 GiB| 500 GiB |
| ReadOps per minut<sup>1, 2</sup> | 1000 | 3000 | 10000 |
| WriteOps per minut<sup>1, 3</sup> | 100 | 500 | 2000 |
| Hämta bandbredd Mbit/s<sup>1</sup> | 30 | 60 | 100 |
| Överför bandbredd Mbit/s<sup>1</sup> | 10 | 20 | 50 |
| Webhook-konfigurationer | 2 | 10 | 100 |
| Geo-replikering | Gäller inte | Gäller inte | [Stöd för *(förhandsgranskning)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps*, och *bandbredd* är minsta uppskattningar. ACR strävar efter att förbättra prestanda som kräver användning.

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) innebär att flera läsåtgärder baserat på antalet lager i bilden plus hämtning av manifestet.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) innebär att flera skrivåtgärder, baserat på antalet lager som måste skickas. En `docker push` innehåller *ReadOps* att hämta ett manifest för en befintlig avbildning.