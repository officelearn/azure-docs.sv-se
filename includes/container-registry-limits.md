---
title: ta med fil
description: ta med fil
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 05/29/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 942b9bdf0201acaefe3333bcf928772899b9bdc2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34665098"
---
| Resurs | Basic | Standard | Premium |
|---|---|---|---|---|
| Storage | 10 giB | 100 giB| 500 giB |
| Maximal bildstorlek lager | 20 giB | 20 giB | 50 giB |
| ReadOps per minut<sup>1, 2</sup> | 1000 | 3000 | 10000 |
| WriteOps per minut<sup>1, 3</sup> | 100 | 500 | 2000 |
| Hämta bandbredd Mbit/s<sup>1</sup> | 30 | 60 | 100 |
| Överför bandbredd Mbit/s<sup>1</sup> | 10 | 20 | 50 |
| Webhook-konfigurationer | 2 | 10 | 100 |
| Geo-replikering | Gäller inte | Gäller inte | [Stöds](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps*, och *bandbredd* är minsta uppskattningar. ACR strävar efter att förbättra prestanda som kräver användning.

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) innebär att flera läsåtgärder baserat på antalet lager i bilden plus hämtning av manifestet.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) innebär att flera skrivåtgärder, baserat på antalet lager som måste skickas. En `docker push` innehåller *ReadOps* att hämta ett manifest för en befintlig avbildning.