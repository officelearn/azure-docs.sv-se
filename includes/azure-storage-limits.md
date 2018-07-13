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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38756035"
---
| Resurs | Standardgräns |
| --- | --- |
| Antal lagringskonton per region per prenumeration | 200<sup>1</sup> |
| Maximal kapacitet för lagringskonton | 500 TiB<sup>2</sup> |
| Maxantal blob-behållare, blobar, filresurser, tabeller, köer, enheter eller meddelanden per lagringskonto | Obegränsat |
| Maximala hastigheten per lagringskonto | 20 000 begäranden per sekund<sup>2</sup> |
| Maximalt antal inkommande<sup>3</sup> per lagringskonto (oss regioner) | 10 Gbit/s om RA-GRS/GRS aktiverad 20 Gbit/s för LRS/ZRS<sup>4</sup> |
| Maximalt antal utgående<sup>3</sup> per lagringskonto (oss regioner) | 20 Gbit/s om RA-GRS/GRS aktiverad, 30 Gbit/s för LRS/ZRS<sup>4</sup> |
| Maximalt antal inkommande<sup>3</sup> per lagringskonto (icke-amerikanska regioner) | 5 Gbit/s om RA-GRS/GRS aktiverad, 10 Gbit/s för LRS/ZRS<sup>4</sup> |
| Maximalt antal utgående<sup>3</sup> per lagringskonto (icke-amerikanska regioner) | 10 Gbit/s om RA-GRS/GRS aktiverad, 15 Gbit/s för LRS/ZRS<sup>4</sup> |

<sup>1</sup>innehåller både Standard och Premium storage-konton. Om du behöver fler än 200 lagringskonton i en viss region kan skicka en förfrågan via [Azure-supporten](https://azure.microsoft.com/support/faq/). Azure Storage-teamet granskar ditt affärsfall och kan godkänna upp till 250 lagringskonton för en viss region. 

<sup>2</sup> om du behöver utökade gränser för ditt storage-konto kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/). Azure Storage-teamet granskar begäran och kan godkänna högre gränser på grundval av fall. Både allmänna och Blob storage-konton stöder ökad kapacitet, ingång/utgång och förfrågningar av begäran. De nya maximala storlekar för Blob storage-konton finns i [meddelande om större, högre skala lagringskonton](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>3</sup> versaler bara av kontots ingående/utgående trafik gränser. *Ingående* syftar på alla data (förfrågningar) som skickas till ett lagringskonto. *Utgående* syftar på alla data (svar) som tas emot från ett lagringskonto.  

<sup>4</sup>redundansalternativ för azure Storage inkluderar:
* **RA-GRS**: Read-access geo-redundant lagring. Om RA-GRS aktiveras är utgående mål för den sekundära platsen samma som för den primära platsen.
* **GRS**: Geo-redundant lagring. 
* **ZRS**: zonredundant lagring.
* **LRS**: lokalt redundant lagring. 
