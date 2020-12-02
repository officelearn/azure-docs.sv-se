---
title: Så tillämpas reservationsrabatten på Azure Cache for Redis | Microsoft Docs
description: Lär dig hur reservationsrabatten används med Azure Cache for Redis-instanser.
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 5f9e0a18db0920acd35ebd7b133ed3fe5d0eaee9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352957"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>Så används reservationsrabatten med Azure Cache for Redis

När du har köpt en reserverad kapacitet för Azure Cache for Redis tillämpas reservationsrabatten automatiskt på cacheinstanser som matchar reservationsattributen och reservationskvantiteten. En reservation täcker endast beräkningskostnaderna för Azure Cache for Redis. Du debiteras för lagring och nätverk enligt de vanliga priserna. Reserverad kapacitet är endast tillgängligt för cacheinstanser på [premiumnivå](../../azure-cache-for-redis/quickstart-create-redis.md).

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatter ***går förlorade om de inte används** _. Om du inte har några matchande resurser för en viss timme förlorar du därmed en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du avslutar en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna förlorade.

## <a name="discount-applied-to-azure-cache-for-redis"></a>Rabatt som tillämpas på Azure Cache for Redis

Rabatten för reserverad Azure Cache for Redis-kapacitet tillämpas på cacheinstanser per timme. Den reservation som du köper matchas mot den beräkningsanvändning som genereras av de cacheinstanser som körs. Om dessa cacheinstanser inte körs hela timmen tillämpas reservationen automatiskt på andra cacheinstanser som matchar reservationsattributen. Rabatten kan tillämpas på cacheinstanser som körs samtidigt. Om du inte har några cacheinstanser som körs en hel timme och som matchar reservationsattributen får du inte den fullständiga fördelen med reservationsrabatten för den timmen.

Följande exempel illustrerar hur rabatten för en reserverad Azure Cache for Redis-kvantitet tillämpas beroende på hur många cacheinstanser du har köpt och när de körs.

_ **Exempel 1**: Du köper reserverad Azure Cache for Redis-kapacitet för en 6 GB-cache. Om du kör en 13 GB-cache som matchar resten av attributen för reservationen betalar du priset per användning för 7 GB av Azure Cache for Redis-beräkningsanvändningen, och får reservationsrabatten i en timme för beräkningsanvändning med en 6 GB-cache.

Låt säga i resten av exemplen att den reserverade Azure Cache for Redis-kapaciteten som du köper är för en 26 GB-cache och att resten av reservationsattributen matchar cacheinstansen som körs.

* **Exempel 2**: Du kör två 13 GB-cacheinstanser i en timme. Reservationsrabatten för 26 GB tillämpas på beräkningsanvändningen för båda cacheinstanserna.

* **Exempel 3**: Du kör en 26 GB-cache från 13:00 till 13:30. Du kör en annan 26 GB-cache från 13:30 till 14:00. Båda omfattas av reservationsrabatten.

* **Exempel 4**: Du kör en 26 GB-cache från 13:00 till 13:45. Du kör en annan 26 GB-cache från 13:30 till 14:00. Du debiteras Betala per användning-priset för överlappningen på 15 minuter. Reservationsrabatten gäller för beräkningsanvändningen för resten av tiden.

Information om hur du förstår och visar tillämpningen av dina Azure-reservationer i rapporterna för faktureringsanvändning finns i avsnittet om [hur Azure-reservationsanvändning fungerar](./understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss
Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).