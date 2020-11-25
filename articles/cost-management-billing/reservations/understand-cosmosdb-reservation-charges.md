---
title: Förstå reservationsrabatten i Azure Cosmos DB
description: Lär dig hur reservationsrabatten tillämpas för etablerade dataflöden (RU/s) i Azure Cosmos DB.
author: bandersmsft
ms.author: banders
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: ca4c1810912771f56661ca5b682b6991735f526e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023150"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Förstå hur reservationsrabatten tillämpas för Azure Cosmos DB

När du har köpt en reserverad Azure Cosmos DB-kapacitet tillämpas reservationsrabatten automatiskt på Azure Cosmos DB-resurser som matchar reservationens attribut och kvantitet. En reservation täcker det dataflöde som etableras för Azure Cosmos DB-resurser. Det täcker inte kostnader för programvara, nätverk, lagring eller fördefinierade containrar.

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatter *går förlorade om de inte används*. Om du inte har några matchande resurser för en viss timme förlorar du därmed en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du avslutar en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna *förlorade*.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Reservationsrabatt som tillämpas på Azure Cosmos DB-konton

En reservationsrabatt tillämpas på [etablerade dataflöden](../../cosmos-db/request-units.md) i termer av enheter för programbegäran per sekund (RU/s), timme för timme. För Azure Cosmos DB-resurser som inte körs hela timmen tillämpas reservationsrabatten automatiskt för andra Azure Cosmos DB-resurser som matchar reservationsattributen. Rabatten kan tillämpas på Azure Cosmos DB-resurser som körs samtidigt. Om du inte har några Cosmos DB-resurser som körs timmen ut och som matchar reservationsattributen får du inte valuta för reservationsrabatten under den timmen.

Rabatterna är nivåindelade. Reservationer med fler enheter för programbegäran ger större rabatt.

Vid reservationsköpet tillämpas rabatter för alla regioner i förhållande till regionens Betala per användning-priser. Du kan se förhållandet för reservationsrabatten i respektive region i avsnittet [Reservationsrabatter per region](#reservation-discount-per-region) i den här artikeln.

## <a name="reservation-discount-per-region"></a>Reservationsrabatt per region
Reservationsrabatten tillämpas på kostnader för Azure Cosmos DB-dataflöden timme för timme. Den tillämpas antingen för en enskild prenumeration eller i omfånget registrering/konto. Reservationsrabatten gäller för mätaranvändning enligt följande förhållanden i olika regioner:

|Mätarbeskrivning  |Region |Förhållande  |
|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/timme – Asien och Stillahavsområdet, sydöstra  |  Asien och Stillahavsområdet, sydöstra    |   1      |
|Azure Cosmos DB – 100 RU/s/timme – Asien och Stillahavsområdet, östra |   Asien och Stillahavsområdet, östra   |    1     |
|Azure Cosmos DB – 100 RU/s/timme – Europa, norra|  Europa, norra       |    1     |
|Azure Cosmos DB – 100 RU/s/timme – Sydkorea, södra|    Sydkorea, södra     |     1    |
|Azure Cosmos DB – 100 RU/s/timme – Europa, västra|    Europa, västra     |      1   |
|Azure Cosmos DB – 100 RU/s/timme – Sydkorea, centrala|   Sydkorea, centrala    |       1  |
|Azure Cosmos DB – 100 RU/s/timme – Storbritannien, södra|   Storbritannien, södra      |     1    |
|Azure Cosmos DB – 100 RU/s/timme – Storbritannien, västra|   Storbritannien, västra      |    1     |
|Azure Cosmos DB – 100 RU/s/timme – Storbritannien, norra |   Storbritannien, norra    |     1    |
|Azure Cosmos DB – 100 RU/s/timme – Storbritannien, södra 2|   Storbritannien, södra 2      |     1    |
|Azure Cosmos DB – 100 RU/s/timme – USA, östra 2|  USA, östra 2     |     1    |
|Azure Cosmos DB – 100 RU/s/timme – USA, norra centrala|   USA, norra centrala      |     1    |
|Azure Cosmos DB – 100 RU/s/timme – USA, västra|   USA, Västra      |     1    |
|Azure Cosmos DB – 100 RU/s/timme – USA, centrala| USA, centrala        |     1    |
|Azure Cosmos DB – 100 RU/s/timme – USA, västra 2|   USA, västra 2      |      1   |
|Azure Cosmos DB – 100 RU/s/timme – USA, västra centrala|   USA, västra centrala      |       1  |
|Azure Cosmos DB – 100 RU/s/timme – USA, östra|   USA, Östra      |  1       |
|Azure Cosmos DB – 100 RU/s/timme – SA, norra|     SA, norra    |   1      |
|Azure Cosmos DB – 100 RU/s/timme – SA, västra |    SA, västra      |    1     |
|Azure Cosmos DB – 100 RU/s/timme – Indien, södra|    Indien, södra     |    1.0375    |
|Azure Cosmos DB – 100 RU/s/timme – Kanada, östra|   Kanada, östra      |    1.1      |
|Azure Cosmos DB – 100 RU/s/timme – Japan, östra|   Japan, östra      |    1.125     |
|Azure Cosmos DB – 100 RU/s/timme – Japan, västra|     Japan, västra    |   1.125       |
|Azure Cosmos DB – 100 RU/s/timme – Indien, västra|     Indien, västra    |    1.1375     |
|Azure Cosmos DB – 100 RU/s/timme – Indien, centrala|    Indien, centrala     |  1.1375       |
|Azure Cosmos DB – 100 RU/s/timme – Australien, östra|     Australien, östra    |   1.15       |
|Azure Cosmos DB – 100 RU/s/timme – Kanada, centrala|  Kanada, centrala       |   1.2       |
|Azure Cosmos DB – 100 RU/s/timme – Frankrike, centrala|   Frankrike, centrala      |    1.25      |
|Azure Cosmos DB – 100 RU/s/timme – Brasilien, södra|  Brasilien, södra       |   1.5      |
|Azure Cosmos DB – 100 RU/s/timme – Australien, centrala|   Australien, centrala      |   1.5      |
|Azure Cosmos DB – 100 RU/s/timme – Australien, centrala 2| Australien, centrala 2        |    1.5     |
|Azure Cosmos DB – 100 RU/s/timme – Frankrike, södra|    Frankrike, södra     |    1.625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Scenarier som visar hur reservationsrabatten tillämpas

Tänk dig att du har följande krav för en reservation:

* Nödvändigt dataflöde: 50 000 RU/s  
* Regioner som används: 2

I det här fallet gäller användningen på begäran en kvantitet på 500 st. 100 RU/s-mätare i dessa två regioner. Den totala förbrukningen av RU/s är 100 000 varje timme.

**Scenario 1**

Anta till exempel att du behöver Azure Cosmos DB-distributioner i regionerna USA, norra centrala och USA, västra. Varje region har en dataflödesförbrukning på 50 000 RU/s. Ett reservationsköp på 100 000 RU/s skulle helt täcka dina utgifter för användningen på begäran.

Rabatten som reservationen täcker beräknas så här: dataflödesförbrukning * reservationsrabattens_förhållande_i_regionen. För regionerna USA, norra centrala och USA, västra är det här förhållandet 1. Den totala mängden rabatterade RU/s är alltså 100 000. Värdet beräknas så här: 50 000 * 1 + 50 000 * 1 = 100 000 RU/s. Du behöver inte betala några ytterligare avgifter enligt de vanliga Betala per användning-priserna.

|Mätarbeskrivning | Region |Dataflödesförbrukning (RU/s) |Reservationsrabatt tillämpad på RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/timme – USA, norra centrala  |   USA, norra centrala  | 50 000  | 50 000  |
|Azure Cosmos DB – 100 RU/s/timme – USA, västra  |  USA, Västra   |  50 000  |  50 000 |

**Scenario 2**

Anta till exempel att du behöver Azure Cosmos DB-distributioner i regionerna Australien, centrala 2 och Frankrike, södra. Varje region har en dataflödesförbrukning på 50 000 RU/s. Ett reservationsköp på 100 000 RU/s skulle tillämpas så här (förutsatt att användningen i Australien, centrala 2 prioriteras):

|Mätarbeskrivning | Region |Dataflödesförbrukning (RU/s) |Reservationsrabatt tillämpad på RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/timme – Australien, centrala 2  |  Australien, centrala 2   |  50 000  |  50 000   |
|Azure Cosmos DB – 100 RU/s/timme – Frankrike, södra  |  Frankrike, södra   |  50 000 |  15 384  |

En användning på 50 000 enheter i regionen Australien, centrala 2 motsvarar en fakturerbar användning (eller normaliserad användning) på 75 000 RU/s. Värdet beräknas så här: dataflödesförbrukning * reservationsrabattens_förhållande_i_regionen. Beräkningen motsvarar 75 000 RU/s fakturerbar eller normaliserad användning. Värdet beräknas så här: 50 000 * 1.5 = 75 000 RU/s.

Reservationsköpet på 100 000 RU/s skulle täcka användningen på 75 000 RU/s i Australien, centrala 2. Det lämnar 25 000 RU/s till regionen Frankrike, södra. De återstående 25 000 RU/s ger en reservationsrabatt på 15 384 RU/s i regionen Frankrike, södra. Rabattvärdet beräknas så här: 25 000/1.625 = 15 384 RU/s. Återstående 34 616 RU/s i regionen Frankrike, södra debiteras enligt de vanliga Betala per användning-priserna.

Azure-faktureringssystemet tilldelar reservationsrabatten till den första instansen som bearbetas och som matchar reservationskonfigurationen. I det här fallet är det Australien, centrala 2.

Information om hur du förstår och visar tillämpningen av dina Azure-reservationer i användningsrapporter på fakturor finns i [Förstå användning av Azure-reservationer](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

* [Vad är reservationer för Azure](save-compute-costs-reservations.md)  
* [Betala i förväg för Azure Cosmos DB-resurser med reserverad Azure Cosmos DB-kapacitet](../../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)  
* [Hantera reservationer för Azure](manage-reserved-vm-instance.md)  
* [Förstå reservationsanvändning för din Betala per användning-prenumeration](understand-reserved-instance-usage.md)  
* [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
* [Förstå reservationsanvändning för CSP-prenumerationer](/partner-center/azure-reservations)