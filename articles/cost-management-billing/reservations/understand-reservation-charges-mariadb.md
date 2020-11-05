---
title: Förstå reservationsrabatt – Azure Database for MariaDB
description: Läs mer om hur en reservationsrabatt tillämpas på Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2197f3ec9907aa9f1afde6272dd2e8a807465476
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240400"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mariadb"></a>Så tillämpas en reservationsrabatt på Azure Database for MariaDB

När du har köpt en reserverad Azure Database for MariaDB-kapacitet tillämpas reservationsrabatten automatiskt på MariaDB-servrar som matchar reservationens attribut och kvantitet. En reservation omfattar bara beräkningskostnaderna för din Azure Database for MariaDB. Du debiteras för lagring och nätverk enligt de vanliga priserna.

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatter * **går förlorade om de inte används** _. Om du inte har några matchande resurser för en viss timme förlorar du därmed en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du avslutar en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna förlorade.

## <a name="discount-applied-to-azure-database-for-mariadb"></a>Rabatt tillämpad på Azure Database for MariaDB

Rabatten för reserverad Azure Database for MariaDB-kapacitet tillämpas timvis på MariaDB-servrar som körs. Den reservation som du köper matchas mot den beräkningsanvändning som genereras av de Azure Database for MariaDB-servrar som körs. För MariaDB-servrar som inte körs timmen ut tillämpas reservationen automatiskt på andra Azure Database for MariaDB som matchar reservationsattributen. Rabatten kan tillämpas på Azure Database for MariaDB-servrar som körs samtidigt. Om du inte har någon MariaDB-server som körs timmen ut och som matchar reservationsattributen får du inte den fullständiga fördelen med reservationsrabatten för den timmen.

I följande exempel visas hur rabatten för reserverad Azure Database for MariaDB-kapacitet tillämpas beroende på det antal kärnor som du har köpt och när de körs.

_ **Exempel 1** : Du köper en reserverad Azure Database for MariaDB-kapacitet för en med 8 virtuella kärnor. Om du kör en Azure Database for MariaDB-server med 16 virtuella kärnor som matchar resten av attributen för reservationen debiteras du Betala per användning-priset för 8 virtuella kärnor för din beräkningsanvändning för MariaDB-servern och du får en reservationsrabatt för MariaDB-server med 8 virtuella kärnor.

För resten av dessa exempel förutsätter vi att den reserverade Azure Database for MariaDB-kapacitet som du köper är avsedd för en Azure Database for MariaDB med 16 virtuella kärnor och att resten av reservationens attribut matchar de Azure Database for MariaDB-servrar som körs.

* **Exempel 2** : Du kör två Azure Database for MariaDB-servrar med 8 virtuella kärnor i en timme. Reservationsrabatten för 16 virtuella kärnor används för beräkningsanvändning för båda Azure Database for MariaDB-servrarna med 8 virtuella kärnor.

* **Exempel 3** : Du kör en Azure Database for MariaDB-server med 16 virtuella kärnor från kl. 13:00 till kl. 13:30. Du kör en annan Azure Database for MariaDB-server med 16 virtuella kärnor från kl. 13:30 till kl. 14:00. Båda omfattas av reservationsrabatten.

* **Exempel 4** : Du kör en Azure Database for MariaDB-server med 16 virtuella kärnor från kl. 13:00 till kl. 13:45. Du kör en annan Azure Database for MariaDB-server med 16 virtuella kärnor från kl. 13:30 till kl. 14:00. Du debiteras Betala per användning-priset för överlappningen på 15 minuter. Reservationsrabatten gäller för beräkningsanvändningen för resten av tiden.

Information om hur du förstår och visar tillämpningen av dina Azure-reservationer i rapporterna för faktureringsanvändning finns i avsnittet om [hur Azure-reservationsanvändning fungerar](./understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Nästa steg

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).