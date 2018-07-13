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
ms.openlocfilehash: 92a5d162e7a0b2c752a2f8e9c5941edf43e539e3
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991093"
---
| Resurs | Basic | Standard | Premium |
|---|---|---|---|---|
| Storage | 10 giB | 100 giB| 500 giB |
| Maximal bildstorlek lager | 20 giB | 20 giB | 50 giB |
| ReadOps per minut<sup>1, 2</sup> | 1,000 | 3 000 | 10 000 |
| WriteOps per minut<sup>1, 3</sup> | 100 | 500 | 2,000 |
| Ladda ned bandbredd Mbit/s<sup>1</sup> | 30 | 60 | 100 |
| Ladda upp bandbredd Mbit/s<sup>1</sup> | 10 | 20 | 50 |
| Webhook-konfigurationer | 2 | 10 | 100 |
| Geo-replikering | Gäller inte | Gäller inte | [Stöds](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps*, och *bandbredd* är minsta beräkningar. ACR strävar efter att förbättra prestanda som kräver användning.

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) översätts till flera läsåtgärder baserat på antalet lager i avbildningen, plus manifest hämtning.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) översätts till flera skrivåtgärder, baserat på antalet lager som måste skickas. En `docker push` innehåller *ReadOps* att hämta ett manifest för en befintlig avbildning.
