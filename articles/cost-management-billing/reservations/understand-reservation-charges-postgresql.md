---
title: Så tillämpas en reservationsrabatt på Azure Database for PostgreSQL – enskild server
description: Så tillämpas en reservationsrabatt på Azure Database for PostgreSQL – enskild server
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/13/2020
ms.openlocfilehash: e0dc7780ee570ce2d731bedf9ef56be606088a1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199304"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>Så tillämpas en reservationsrabatt på Azure Database for PostgreSQL – enskild server

När du har köpt en reserverad kapacitet för Azure Database for PostgreSQL – enskild server tillämpas reservationsrabatten automatiskt på SQL-databaser som matchar reservationens attribut och kvantitet. En reservation omfattar bara beräkningskostnaderna för Azure Database for PostgreSQL – enskild server. Du debiteras för lagring och nätverk enligt de vanliga priserna.

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatter ***går förlorade om de inte används***. Om du inte har några matchande resurser för en viss timme förlorar du därmed en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.</br>

När du avslutar en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna förlorade.

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Rabatt tillämpad på Azure Database for PostgreSQL – enskild server

Rabatten för reserverad kapacitet för Azure Database for PostgreSQL – enskild server tillämpas på körning av PostgreSQL – enskild server per timme. Den reservation som du köper matchas mot den beräkningsanvändning som genereras av reserverad kapacitet för Azure Database for PostgreSQL – enskild server som körs. För enskilda PostgreSQL-servrar som inte körs timmen ut tillämpas reservationen automatiskt på en annan Azure Database for PostgreSQL – enskild server som matchar reservationsattributen. Rabatten kan tillämpas på enskilda Azure Database for PostgreSQL-servrar som körs samtidigt. Om du inte har någon PostgreSQL – enskild server som körs timmen ut och som matchar reservationsattributen får du inte den fullständiga fördelen med reservationsrabatten för den timmen.

I följande exempel visas hur rabatten för reserverad kapacitet för Azure Database for PostgreSQL – enskild server tillämpas beroende på det antal kärnor som du har köpt och när de körs.

* **Exempel 1**: Du köper en reserverad kapacitet för Azure Database for PostgreSQL – enskild server för en med 8 virtuella kärnor. Om du kör en Azure Database for PostgreSQL – enskild server med 16 virtuella kärnor som matchar resten av attributen för reservationen debiteras du Betala per användning-priset för 8 virtuella kärnor för din beräkningsanvändning för PostgreSQL – enskild server och du får en reservationsrabatt för PostgreSQL – enskild server med 8 virtuella kärnor.</br>

För resten av dessa exempel förutsätter vi att den reserverade kapaciteten för Azure Database for PostgreSQL – enskild server som du köper är avsedd för Azure Database for PostgreSQL – enskild server med 16 virtuella kärnor och att resten av reservationens attribut matchar de enskilda Azure Database for PostgreSQL-servrar som körs.

* **Exempel 2**: Du kör två enskilda Azure Database for PostgreSQL-servrar med 8 virtuella kärnor i en timme. Reservationsrabatten för 16 virtuella kärnor används för beräkningsanvändning för båda Azure Database for PostgreSQL – enskild server med 8 virtuella kärnor.

* **Exempel 3**: Du kör en Azure Database for PostgreSQL – enskild server med 16 virtuella kärnor från kl. 13:00 till kl. 13:30. Du kör en annan Azure Database for PostgreSQL – enskild server med 16 virtuella kärnor från kl. 13:30 till kl. 14:00. Båda omfattas av reservationsrabatten.

* **Exempel 4**: Du kör en Azure Database for PostgreSQL – enskild server med 16 virtuella kärnor från kl. 13:00 till kl. 13:45. Du kör en annan Azure Database for PostgreSQL – enskild server med 16 virtuella kärnor från kl. 13:30 till kl. 14:00. Du debiteras Betala per användning-priset för överlappningen på 15 minuter. Reservationsrabatten gäller för beräkningsanvändningen för resten av tiden.

Information om hur du förstår och visar tillämpningen av dina Azure-reservationer i rapporterna för faktureringsanvändning finns i avsnittet om [hur Azure-reservationsanvändning fungerar](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss
Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
