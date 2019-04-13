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
ms.openlocfilehash: ee9b70827c19236287f81f66f4b9c6e1004a54fd
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59532465"
---
I följande tabell beskrivs standardgränser för Azure general-purpose v1, v2 och Blob storage-konton. Den *ingående* gränsen syftar på alla data från förfrågningar som skickas till ett lagringskonto. Den *utgående* gränsen syftar på alla data från svar som tas emot från ett lagringskonto.

| Resurs | Standardgräns |
| --- | --- |
| Antal lagringskonton per region per prenumeration, inklusive konton för standard- och premium | 250 |
| Maximal lagringskapacitet för konto | 2 PB för USA och Europa, 500 TB för alla andra regioner, vilket innefattar Storbritannien |
| Maximalt antal blob-behållare, blobar, filresurser, tabeller, köer, enheter eller meddelanden per lagringskonto | Obegränsad |
| Maximala hastigheten<sup>1</sup> per lagringskonto | 20 000 begäranden per sekund |
| Största ingående<sup>1</sup> per lagringskonto (USA-regioner) | 10 Gbit/s om RA-GRS/GRS är aktiverad, 20 Gbit/s för LRS/ZRS<sup>2</sup> |
| Största ingående<sup>1</sup> per lagringskonto (icke-amerikanska regioner) | 5 Gbit/s om RA-GRS/GRS är aktiverad, 10 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt utgående för generell användning v2 och Blob storage-konton (alla regioner) | 50 Gbit/s |
| Maximalt utgående general-purpose v1-lagringskonton (USA-regioner) | 20 Gbit/s om RA-GRS/GRS är aktiverad, 30 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt utgående general-purpose v1-lagringskonton (icke-amerikanska regioner) | 10 Gbit/s om RA-GRS/GRS är aktiverad, 15 Gbit/s för LRS/ZRS<sup>2</sup> |

<sup>1</sup>azure Standard Storage-konton har högre gränser för inkommande begäran. Om du vill begära en ökning av gränser för ingress, kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/).

<sup>2</sup> [azure Storage-replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy) alternativ inkluderar:
* **RA-GRS**: Läsåtkomst till geografiskt redundant lagring. Om RA-GRS aktiveras är utgående mål för den sekundära platsen samma som för den primära platsen.
* **GRS**: GEO-redundant lagring. 
* **ZRS**: Zonredundant lagring.
* **LRS**: Lokalt redundant lagring. 

> [!NOTE]
> Vi rekommenderar att du använder ett gpv2-lagringskonto för de flesta scenarier. Du kan enkelt uppgradera general-purpose v1- eller ett Azure Blob storage-konto till ett gpv2-konto utan avbrott och utan att behöva kopiera data.
>
> Mer information om Azure Storage-konton finns i [lagringskontoöversikten](../articles/storage/common/storage-account-overview.md). 

Om programmets behov överstiger det för skalbarhetsmål för ett enda lagringskonto, kan du skapa ditt program att använda flera lagringskonton. Du kan sedan partitionera dina dataobjekt över dessa lagringskonton. Information om Volympriser finns i [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/).

Alla lagringskonton körs på en flat nätverkstopologi och stöd för de mål för skalbarhet och prestanda som beskrivs i den här artikeln, oavsett när de har skapats. Läs mer på Azure Storage platt nätverk-arkitektur och skalbarhet, [Microsoft Azure Storage: En högtillgänglig Molnlagringstjänst med stark konsekvens](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

