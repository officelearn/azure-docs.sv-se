---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/03/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6381f8f0e68853183fc3e17e76b4ab93b152b48b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
| Resurs | Standardgräns |
| --- | --- |
| Antal lagringskonton per region | 200<sup>1</sup> |
| Maximal kapacitet för lagringskonton | 500 TiB<sup>2</sup> |
| Högsta antal blob-behållare, blobbar, filresurser, tabeller, köer, entiteter eller meddelanden per storage-konto | Obegränsat |
| Maximal förfrågningar per lagringskonto | 20 000 begäranden per sekund<sup>2</sup> |
| Max ingång<sup>3</sup> per lagringskonto (oss regioner) | 10 Gbit/s om GRS-RA-GRS aktiverad 20 Gbit/s för LRS/ZRS<sup>4</sup> |
| Maximalt antal utgående<sup>3</sup> per lagringskonto (oss regioner) | 20 Gbit/s om GRS-RA-GRS aktiverad 30 Gbit/s för LRS/ZRS<sup>4</sup> |
| Max ingång<sup>3</sup> per lagringskonto (icke-amerikansk regioner) | 5 Gbit/s om GRS-RA-GRS aktiverad, 10 Gbit/s för LRS/ZRS<sup>4</sup> |
| Maximalt antal utgående<sup>3</sup> per lagringskonto (icke-amerikansk regioner) | 10 Gbit/s om GRS-RA-GRS aktiverad 15 Gbit/s för LRS/ZRS<sup>4</sup> |

<sup>1</sup>innehåller både Standard- och Premium storage-konton. Om du behöver mer än 200 lagringskonton skickar du en begäran via [Azure-supporten](https://azure.microsoft.com/support/faq/). Azure Storage-teamet granskar ditt affärsfall och kan godkänna upp till 250 lagringskonton. 

<sup>2</sup> om du behöver utökade gränser för ditt lagringskonto kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/). Azure Storage-teamet kommer granska förfrågningen och godkänna högre gränser på en fall till fall. Både allmänna och Blob storage-konton stöder bättre kapacitet och ingång-/ utgång förfrågningar av begäran. Den nya maximala storleken för Blob storage-konton finns [meddelar större, högre skala lagringskonton](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>3</sup> begränsad endast av kontots ingång-/ utgång gränser. *Ingång* refererar till alla data (antal begäranden) som skickas till ett lagringskonto. *Utgående* syftar på alla data (svar) som tas emot från ett lagringskonto.  

<sup>4</sup>alternativ för azure Storage-redundans inkluderar:
* **RA-GRS**: geo-redundant lagring med läsbehörighet. Utgående mål för den sekundära platsen är samma som för den primära platsen om RA-GRS är aktiverad.
* **GRS**: Geo-redundant lagring. 
* **ZRS**: zonredundant lagring.
* **LRS**: lokalt redundant lagring. 
