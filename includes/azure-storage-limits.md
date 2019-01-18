---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e4fa42b6c32c3eb383eea4489ea109c0d496bdb9
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392662"
---
I följande tabell beskrivs standardgränser för Azure Storage. Den *ingående* gränsen syftar på alla data (förfrågningar) som skickas till ett lagringskonto. Den *utgående* gränsen syftar på alla data (svar) som tas emot från ett lagringskonto.

| Resurs | Standardgräns |
| --- | --- |
| Antal lagringskonton per region per prenumeration, inklusive konton för standard- och premium | 250 |
| Maximal kapacitet för lagringskonton | 2 PB för USA och Europa, 500 TB för alla andra regioner, inklusive Storbritannien |
| Maxantal blob-behållare, blobar, filresurser, tabeller, köer, enheter eller meddelanden per lagringskonto | Obegränsat |
| Maximala hastigheten<sup>1</sup> per lagringskonto | 20 000 begäranden per sekund |
| Maximalt antal inkommande<sup>1</sup> per lagringskonto (oss regioner) | 10 Gbit/s om RA-GRS/GRS aktiverad 20 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt antal inkommande<sup>1</sup> per lagringskonto (icke-amerikanska regioner) | 5 Gbit/s om RA-GRS/GRS aktiverad, 10 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt antal utgående för generell användning v2 och Blob storage-konton (alla regioner) | 50 Gbit/s |
| Maximalt antal utgående general-purpose v1-lagringskonton (USA-regioner) | 20 Gbit/s om RA-GRS/GRS aktiverad, 30 Gbit/s för LRS/ZRS <sup>2</sup> |
| Maximalt antal utgående general-purpose v1-lagringskonton (icke-amerikanska regioner) | 10 Gbit/s om RA-GRS/GRS aktiverad, 15 Gbit/s för LRS/ZRS <sup>2</sup> |

<sup>1</sup> azure standard storage-konton har högre gränser för inkommande begäran. Om du vill begära en ökning av gränser för ingress, kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/).

<sup>2</sup> [azure Storage-replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy) alternativ inkluderar:
* **RA-GRS**: Läsåtkomst till geografiskt redundant lagring. Om RA-GRS aktiveras är utgående mål för den sekundära platsen samma som för den primära platsen.
* **GRS**: GEO-redundant lagring. 
* **ZRS**: Zonredundant lagring.
* **LRS**: Lokalt redundant lagring. 

> [!NOTE]
> Microsoft rekommenderar att du använder ett gpv2-lagringskonto för de flesta scenarier. Du kan enkelt uppgradera allmänna v1- eller Blob storage-konto till ett gpv2-konto utan avbrott och utan att behöva kopiera data.
>
> Mer information om Azure Storage-konton finns i [Lagringskontoöversikten](../articles/storage/common/storage-account-overview.md). 

Om programmets behov överstiger det för skalbarhetsmål för ett enda lagringskonto, kan du skapa ditt program att använda flera lagringskonton. Du kan sedan partitionera dina dataobjekt över dessa lagringskonton. Se [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) för information om Volympriser.

Alla lagringskonton körs på en flat nätverkstopologi och stöd för de mål för skalbarhet och prestanda som beskrivs i den här artikeln, oavsett när de har skapats. Läs mer på Azure Storage platt nätverk-arkitektur och skalbarhet, [Microsoft Azure Storage: En högtillgänglig Molnlagringstjänst med stark konsekvens](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

