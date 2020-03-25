---
title: Förstå hur reservationsrabatten tillämpas för Azure Storage | Microsoft Docs
description: Lär dig mer om hur rabatten för reserverad kapacitet i Azure Storage tillämpas på blockblob- och Azure Data Lake Storage Gen2-resurser.
author: tamram
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: tamram
ms.openlocfilehash: b6360619b7666ab7836a751491e05e379df32d93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199202"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>Förstå hur reservationsrabatten tillämpas för Azure Storage

När du har köpt reserverad kapacitet i Azure Storage tillämpas reservationsrabatten automatiskt på blockblob- och Azure Data Lake Storage Gen2-resurser som matchar reservationens villkor. Reservationsrabatten gäller endast för lagringskapacitet. Bandbredd och förfrågningsfrekvens debiteras till Betala per användning-priser.

Mer information om reserverad kapacitet i Azure Storage finns i [Optimera kostnader för bloblagring med reserverad kapacitet](../../storage/blobs/storage-blob-reserved-capacity.md).

Information om Azure Storage-reservationspriser finns i [Prissättning för blockblobar](https://azure.microsoft.com/pricing/details/storage/blobs/) och [Azure Data Lake Storage Gen 2-prissättning](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="how-the-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Rabatten för reserverad kapacitet i Azure Storage tillämpas på blockblob- och Azure Data Lake Storage Gen2-resurser per timme.

Rabatten för reserverad kapacitet i Azure Storage går förlorad om du inte använder den. Om du inte har några blockblob- eller Azure Data Lake Storage Gen2-resurser som uppfyller villkoren för reservationen för en viss timme förlorar du reservationskvantiteten för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du tar bort en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna förlorade.

## <a name="discount-examples"></a>Exempel på rabatter

I följande exempel visas hur rabatten för reserverad kapacitet i Azure Storage tillämpas beroende på distributionen.

Anta att du har köpt 100 TB reserverad kapacitet i regionen USA, västra 2 för en period på 1 år. Reservationen är för lokalt redundant lagring (LRS) i frekvent åtkomstnivå.

Anta att kostnaden i den här exempelreservationen är 18 540 USD. Du kan välja att betala hela beloppet direkt eller betala i fasta månatliga avbetalningar på 1 545 USD per månad under de kommande tolv månaderna.

Anta för dessa exempel att du har registrerat dig för en månatlig reservationsbetalningsplan. Följande scenarier beskriver vad som händer om du underutnyttjar eller överutnyttjar din reserverade kapacitet.

### <a name="underusing-your-capacity"></a>Underutnyttja kapaciteten

Anta att du under en viss timme inom reservationsperioden bara har använt 80 TB av din reserverade kapacitet på 100 TB. Rabatten gäller inte för de 20 TB som är kvar den timmen och överförs inte till nästa period.

### <a name="overusing-your-capacity"></a>Överutnyttja kapaciteten

Anta att du under en viss timme inom reservationsperioden har använt 101 TB lagringskapacitet. Reservationsrabatten tillämpas på 100 TB av dina data och de återstående 1 TB debiteras till Betala per användning-priser för den timmen. Om din användning nästa timme ändras till 100 TB omfattas all användning av reservationen.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Optimera kostnader för blobblagring med reserverad kapacitet](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
