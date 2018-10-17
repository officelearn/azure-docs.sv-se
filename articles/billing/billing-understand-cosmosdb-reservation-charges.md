---
title: Förstå hur reservationsrabatten tillämpas på Azure Cosmos DB | Microsoft Docs
description: Lär dig hur reservationsrabatten tillämpas på etablerat dataflöde (RU/s) i Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: cwatson
ms.reviewer: sngun
ms.openlocfilehash: 5b15b5f8188f2077b3e9cb17ab3794e881a4deb3
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353450"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Förstå hur reservationsrabatten tillämpas på Azure Cosmos DB

När du köper en Azure Cosmos DB reserverad kapacitet tillämpas reservationsrabatten automatiskt till Azure Cosmos DB-resurser som matchar de attribut och mängden reservationen. En reservation täcker dataflödet som tillhandahållits för Azure Cosmos DB-resurser. Den tas upp inte i programvara, nätverk, lagring, eller fördefinierade behållare avgifter.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Reservationsrabatten tillämpas på Azure Cosmos DB-konton

En reservationsrabatten tillämpas [etablerat dataflöde](../cosmos-db/request-units.md) när det gäller begäransenheter per sekund (RU/s) per timme för timme. detta. Reservationsrabatten tillämpas automatiskt till andra Cosmos DB-resurser som matchar reservationen attribut för Azure Cosmos DB-resurser som inte kör hela timmen. Rabatten kan använda för Azure Cosmos DB-resurserna som körs samtidigt. Om du inte har Cosmos DB-resurserna som körs under hela timmen och som matchar reservationen attribut kan får du inte ut mesta möjliga av rabatten för den timmen.

Rabatterna nivåindelas. Reservationer med högre begäransenheter ger större rabatter. 

Då reservationen köptes gäller rabatter för alla regioner i förhållandet motsvarar den på begäran prissättning för olika regioner. Reservation rabatt förhållanden i varje region, finns det [reservationsrabatten per region](#reservation-discount-per-region) i den här artikeln.

## <a name="reservation-discount-per-region"></a>Reservationsrabatten per region
Reservationsrabatten tillämpas på Azure Cosmos DB dataflöde kostnader per timme för timme. detta. Används på enskild prenumeration eller registrerats/konto omfånget. Rabatten gäller att mäta användningen i olika regioner i följande förhållanden:

|Mätaren beskrivning  |Region |Förhållandet mellan  |
|---------|---------|---------|
|Azure Cosmos DB - 100 RU/s/timme – Asien och Stillahavsområdet i Asien  |  Asien och stillahavsområdet, Sydost    |   1      |
|Azure Cosmos DB - 100 RU/s/timme – Asien och Stillahavsområdet östra |   Asien och stillahavsområdet, Öst   |    1     |
|Azure Cosmos DB - 100 RU/s/timme – Europa, Nord|  Europa, Nord       |    1     |
|Azure Cosmos DB - 100 RU/s/timme – Korea, södra|    Sydkorea, södra     |     1    |
|Azure Cosmos DB - 100 RU/s/timme – Europa, Väst|    Europa, Väst     |      1   |
|Azure Cosmos DB - 100 RU/s/timme – Korea, centrala|   Sydkorea, centrala    |       1  |
|Azure Cosmos DB - 100 RU/s/timme – Storbritannien, södra|   Storbritannien, södra      |     1    |
|Azure Cosmos DB - 100 RU/s/timme – Storbritannien, västra|   Storbritannien, västra      |    1     |
|Azure Cosmos DB - 100 RU/s/timme – Storbritannien, Norra |   Storbritannien, norra    |     1    |
|Azure Cosmos DB - 100 RU/s/timme – Storbritannien, södra 2|   Storbritannien, södra 2      |     1    |
|Azure Cosmos DB - 100 RU/s/timme – USA, Öst 2|  USA, östra 2     |     1    |
|Azure Cosmos DB - 100 RU/s/timme – USA, Central Nord|   USA, norra centrala      |     1    |
|Azure Cosmos DB – 100 RU/s/timme – USA, Väst|   USA, Västra      |     1    |
|Azure Cosmos DB - 100 RU/s/timme – USA, Central| USA, centrala        |     1    |
|Azure Cosmos DB - 100 RU/s/timme – USA, Väst 2|   USA, västra 2      |      1   |
|Azure Cosmos DB - 100 RU/s/timme – USA, västra centrala|   USA, västra centrala      |       1  |
|Azure Cosmos DB – 100 RU/s/timme – USA, Öst|   USA, Östra      |  1       |
|Azure Cosmos DB - 100 RU/s/timme - Sydafrika, Norra|     Sydafrika, Norra    |   1      |
|Azure Cosmos DB - 100 RU/s/timme - Sydafrika, västra |    Sydafrika, västra      |    1     |
|Azure Cosmos DB - 100 RU/s/timme - i Indien, södra|    Indien, södra     |    1.0375    |
|Azure Cosmos DB - 100 RU/s/timme – Kanada, östra|   Kanada, östra      |    1.1      |
|Azure Cosmos DB - 100 RU/s/timme – Japan, Öst|   Japan, östra      |    1,125     |
|Azure Cosmos DB - 100 RU/s/timme – Japan, Väst|     Japan, västra    |   1,125       |
|Azure Cosmos DB - 100 RU/s/timme - i Indien, västra|     Indien, västra    |    1.1375     |
|Azure Cosmos DB - 100 RU/s/timme - i Indien, centrala|    Indien, centrala     |  1.1375       |
|Azure Cosmos DB - 100 RU/s/timme – Australien, Öst|     Australien, Öst    |   1,15       |
|Azure Cosmos DB - 100 RU/s/timme – Kanada, centrala|  Kanada, centrala       |   1.2       |
|Azure Cosmos DB - 100 RU/s/timme - Frankrike, centrala|   Frankrike, centrala      |    1.25      |
|Azure Cosmos DB - 100 RU/s/timme – Brasilien, Syd|  Brasilien, Syd       |   1.5      |
|Azure Cosmos DB - 100 RU/s/timme – Australien, centrala|   Australien, centrala      |   1.5      |
|Azure Cosmos DB - 100 RU/s/timme – Australien, centrala 2| Australien, centrala 2        |    1.5     |
|Azure Cosmos DB - 100 RU/s/timme - Frankrike, Syd|    Frankrike, södra     |    1.625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Scenarier som visar hur reservationsrabatten tillämpas

Beakta följande krav för en reservation:

* Nödvändiga genomströmning: 50 000 RU/s  
* Regioner som används: 2 

I det här fallet är dina totala kostnader för på begäran för 500 kvantitet av 100 RU/s i dessa två regioner. Totalt antal RU/s förbrukningen är 100 000 varje timme. 

**Scenario 1**

Anta exempelvis att du behöver Azure Cosmos DB-distributioner i norra centrala USA och västra USA. Varje region har ett dataflöde förbrukning av 50 000 RU/s. En reservationsköp på 100 000 RU/s skulle helt balansera dina kostnader för på begäran.

Rabatt som omfattar en reservation beräknas som: dataflöde konsumtion * reservation_discount_ratio_for_that_region. För Norra centrala USA och västra USA-regioner är reservation rabatt kvoten 1. Totalt antal rabatterade RU/s är 100 000. Det här värdet beräknas som: 50 000 * 1 + 50 000 * 1 = 100 000 RU/s. Du behöver att betala ytterligare avgifter vid de användningsbaserad betalning. 

|Mätaren beskrivning | Region |Användning av dataflöde (RU/s) |Reservationsrabatten tillämpas på RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/s/timme – USA, Central Nord  |   USA, norra centrala  | 50,000  | 50,000  |
|Azure Cosmos DB – 100 RU/s/timme – USA, Väst  |  USA, Västra   |  50,000  |  50,000 |

**Scenario 2**

Anta exempelvis att du behöver Azure Cosmos DB-distributioner i regionerna Australien, centrala 2 och Frankrike, Syd. Varje region har ett dataflöde förbrukning av 50 000 RU/s. En reservationsköp på 100 000 RU/s är tillämpliga på följande sätt (förutsatt att Australien centrala 2 användning har rabatterade först):

|Mätaren beskrivning | Region |Användning av dataflöde (RU/s) |Reservationsrabatten tillämpas på RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/s/timme – Australien, centrala 2  |  Australien, centrala 2   |  50,000  |  50,000   |
|Azure Cosmos DB - 100 RU/s/timme - Frankrike, Syd  |  Frankrike, södra   |  50,000 |  15,384  |

Användning av en 50 000 enheter i regionen Australien, centrala 2 motsvarar 75 000 RU/s för fakturerbar användning (eller normaliserade användning). Det här värdet beräknas som: dataflöde konsumtion * reservation_discount_ratio_for_that_region. Beräkningen är lika med 75 000 RU/s för fakturerbar eller normaliserad användning. Det här värdet beräknas som: 50 000 * 1.5 = 75 000 RU/s.

100 000 RU/s för reservationsköp skulle förskjuta 75 000 RU/s i Australien centrala 2. Den lämnar 25 000 RU/s till Frankrike, Syd-region. Från den återstående 25 000 RU/s tillämpas en reservation rabatt på 15,384 RU/s till Frankrike, Syd-region. Rabattvärde beräknas som: 25 000 / 1.625 = 15,384 RU/s. Den återstående 34,616 RU/s i regionen Frankrike, Syd debiteras enligt normal priserna för användningsbaserad betalning. 

Azure faktureringssystem tilldelar reservationen fakturering fördelen för den första instansen som ska bearbetas och som matchar reservationen konfigurationen. Det är exempelvis Australien centrala 2 i det här fallet.

För att förstå och visa tillämpningen av dina Azure reservationer i fakturering användningsrapporter, se [förstå Azure reservation användning](../billing/billing-understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

* [Vad är Azure reservationer?](../billing/billing-save-compute-costs-reservations.md)  
* [Betala i förskott för Azure Cosmos DB-resurser med Azure Cosmos DB reserverad kapacitet](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)  
* [Hantera Azure-reservationer](../billing/billing-manage-reserved-vm-instance.md)  
* [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](../billing/billing-understand-reserved-instance-usage.md)  
* [Förstå användningen av reserverade för din Enterprise-registrering](../billing/billing-understand-reserved-instance-usage-ea.md)  
* [Förstå användningen av reserverade för CSP-prenumerationer](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

