---
title: ta med fil
description: ta med fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117130"
---
| Resurs | Basic | Standard | Premium |
|---|---|---|---|
| Lagring<sup>1</sup> | 10 Gib (andra) | 100 Gib (100 GiB)| 500 GiB (gib) |
| Maximal bildlagerstorlek | 200 GiB (på andra) | 200 GiB (på andra) | 200 GiB (på andra) |
| ReadOps per minut<sup>2, 3</sup> | 1,000 | 3 000 | 10 000 |
| WriteOps per minut<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Ladda ner bandbredd MBps<sup>2</sup> | 30 | 60 | 100 |
| Ladda upp bandbredd MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Geo-replikering | Ej tillämpligt | Ej tillämpligt | [Stöds][geo-replication] |
| Förtroende för innehåll | Ej tillämpligt | Ej tillämpligt | [Stöds][content-trust] |
| Åtkomst till virtuellt nätverk | Ej tillämpligt | Ej tillämpligt | [Förhandsgranska][vnet] |
| Integrering av privata länkar | Ej tillämpligt | Ej tillämpligt | [Förhandsgranska][plink] |
| Kundhanterade nycklar | Ej tillämpligt | Ej tillämpligt | [Förhandsgranska][cmk] |
| Behörigheter för databasscope | Ej tillämpligt | Ej tillämpligt | [Förhandsgranska][token]|
| &bull;Token | Ej tillämpligt | Ej tillämpligt | 20 000 |
| &bull;Kartor över omfattning | Ej tillämpligt | Ej tillämpligt | 20 000 |
| &bull;Databaser per scope-karta | Ej tillämpligt | Ej tillämpligt | 500 |


<sup>1.</sup> De angivna lagringsgränserna är mängden *inkluderad* lagring för varje nivå. Du debiteras en extra dagskostnad per GiB för bildlagring över dessa gränser. Prisinformation finns i [prissättningen för Azure Container Registry][pricing].

<sup>2</sup>*ReadOps,* *WriteOps*och *Bandbredd* är minimiuppskattningar. Azure Container Registry strävar efter att förbättra prestanda som användning kräver.

<sup>3.</sup> En [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) översätter till flera läsåtgärder baserat på antalet lager i bilden, plus manifesthämtning.

<sup>4.</sup> En [docker-push](https://docs.docker.com/registry/spec/api/#pushing-an-image) översätts till flera skrivåtgärder, baserat på antalet lager som måste skjutas. A `docker push` innehåller *ReadOps* för att hämta ett manifest för en befintlig avbildning.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md