---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a777151216cb70b696da088b8a0d34779ebc39
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396143"
---
I följande tabell beskrivs standardgränser för Azure Storage. Den *ingående* gränsen syftar på alla data (förfrågningar) som skickas till ett lagringskonto. Den *utgående* gränsen syftar på alla data (svar) som tas emot från ett lagringskonto.

| Resurs | Standardgräns |
| --- | --- |
| Antal lagringskonton per region per prenumeration, inklusive konton för standard- och premium | 200 |
| Maximal kapacitet för lagringskonton<sup>1</sup> | 500 TiB |
| Maxantal blob-behållare, blobar, filresurser, tabeller, köer, enheter eller meddelanden per lagringskonto | Obegränsat |
| Maximala hastigheten<sup>1</sup> per lagringskonto | 20 000 begäranden per sekund |
| Maximalt antal inkommande<sup>1</sup> per lagringskonto (oss regioner) | 10 Gbit/s om RA-GRS/GRS aktiverad 20 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt antal utgående<sup>1</sup> per lagringskonto (oss regioner) | 20 Gbit/s om RA-GRS/GRS aktiverad, 30 Gbit/s för LRS/ZRS |
| Maximalt antal inkommande<sup>1</sup> per lagringskonto (icke-amerikanska regioner) | 5 Gbit/s om RA-GRS/GRS aktiverad, 10 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt antal utgående<sup>1</sup> per lagringskonto (icke-amerikanska regioner) | 10 Gbit/s om RA-GRS/GRS aktiverad, 15 Gbit/s för LRS/ZRS |

<sup>1</sup> azure storage-konton har högre gränser för kapacitet, förfrågningar, inkommande och utgående begäran. Mer information om de ökade gränserna finns i [meddelande om större, högre skala lagringskonton](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/). Om du vill begära en ökning av begränsningar, kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/).

<sup>2</sup>[azure Storage-replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy) alternativ inkluderar:
* **RA-GRS**: Read-access geo-redundant lagring. Om RA-GRS aktiveras är utgående mål för den sekundära platsen samma som för den primära platsen.
* **GRS**: Geo-redundant lagring. 
* **ZRS**: zonredundant lagring.
* **LRS**: lokalt redundant lagring. 

