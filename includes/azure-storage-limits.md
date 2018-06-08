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
ms.openlocfilehash: 6572adb0d8d629910492603a17988b89acce2f17
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852066"
---
| Resurs | Standardgräns |
| --- | --- |
| Antal lagringskonton per region per prenumeration | 200<sup>1</sup> |
| Maximal kapacitet för lagringskonton | 500 TiB<sup>2</sup> |
| Högsta antal blob-behållare, blobbar, filresurser, tabeller, köer, entiteter eller meddelanden per storage-konto | Obegränsat |
| Maximal förfrågningar per lagringskonto | 20 000 begäranden per sekund<sup>2</sup> |
| Max ingång<sup>3</sup> per lagringskonto (oss regioner) | 10 Gbit/s om GRS-RA-GRS aktiverad 20 Gbit/s för LRS/ZRS<sup>4</sup> |
| Maximalt antal utgående<sup>3</sup> per lagringskonto (oss regioner) | 20 Gbit/s om GRS-RA-GRS aktiverad 30 Gbit/s för LRS/ZRS<sup>4</sup> |
| Max ingång<sup>3</sup> per lagringskonto (icke-amerikansk regioner) | 5 Gbit/s om GRS-RA-GRS aktiverad, 10 Gbit/s för LRS/ZRS<sup>4</sup> |
| Maximalt antal utgående<sup>3</sup> per lagringskonto (icke-amerikansk regioner) | 10 Gbit/s om GRS-RA-GRS aktiverad 15 Gbit/s för LRS/ZRS<sup>4</sup> |

<sup>1</sup>innehåller både Standard- och Premium storage-konton. Om du behöver mer än 200 storage-konton i en viss region, gör en begäran via [Azure-supporten](https://azure.microsoft.com/support/faq/). Azure Storage-teamet kommer granska ditt företag fall och godkänna upp till 250 lagringskonton för en viss region. 

<sup>2</sup> om du behöver utökade gränser för ditt lagringskonto kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/). Azure Storage-teamet kommer granska förfrågningen och godkänna högre gränser på en fall till fall. Både allmänna och Blob storage-konton stöder bättre kapacitet och ingång-/ utgång förfrågningar av begäran. Den nya maximala storleken för Blob storage-konton finns [meddelar större, högre skala lagringskonton](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>3</sup> begränsad endast av kontots ingång-/ utgång gränser. *Ingång* refererar till alla data (antal begäranden) som skickas till ett lagringskonto. *Utgående* syftar på alla data (svar) som tas emot från ett lagringskonto.  

<sup>4</sup>alternativ för azure Storage-redundans inkluderar:
* **RA-GRS**: geo-redundant lagring med läsbehörighet. Utgående mål för den sekundära platsen är samma som för den primära platsen om RA-GRS är aktiverad.
* **GRS**: Geo-redundant lagring. 
* **ZRS**: zonredundant lagring.
* **LRS**: lokalt redundant lagring. 
