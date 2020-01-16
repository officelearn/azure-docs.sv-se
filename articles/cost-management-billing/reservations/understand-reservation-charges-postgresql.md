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
ms.date: 10/17/2019
ms.openlocfilehash: 2f1843690861a463c634f523e82facffc72b6743
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995512"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>Så tillämpas en reservationsrabatt på Azure Database for PostgreSQL – enskild server

När du har köpt en reserverad kapacitet för Azure Database for PostgreSQL – enskild server tillämpas reservationsrabatten automatiskt på SQL-databaser som matchar reservationens attribut och kvantitet. En reservation omfattar bara beräkningskostnaderna för Azure Database for PostgreSQL – enskild server. Du debiteras för lagring och nätverk enligt de vanliga priserna. 

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatter ***går förlorade om de inte används***. Om du inte har några matchande resurser för en viss timme förlorar du därmed en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.</br>

När du avslutar en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna förlorade.

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Rabatt tillämpad på Azure Database for PostgreSQL – enskild server

Rabatten för reserverad kapacitet för Azure Database for PostgreSQL – enskild server tillämpas på körning av PostgreSQL – enskild server per timme. Den reservation som du köper matchas mot den beräkningsanvändning som genereras av reserverad kapacitet för Azure Database for PostgreSQL – enskild server som körs. För enskilda PostgreSQL-servrar som inte körs timmen ut tillämpas reservationen automatiskt på en annan Azure Database for PostgreSQL – enskild server som matchar reservationsattributen. Rabatten kan tillämpas på enskilda Azure Database for PostgreSQL-servrar som körs samtidigt. Om du inte har någon PostgreSQL – enskild server som körs timmen ut och som matchar reservationsattributen får du inte den fullständiga fördelen med reservationsrabatten för den timmen.

I följande exempel visas hur rabatten för reserverad kapacitet för Azure Database for PostgreSQL – enskild server tillämpas beroende på det antal kärnor som du har köpt och när de körs.

* **Exempel 1**: du köper en Azure Database for PostgreSQL reserverad Server kapacitet för en 8-vcore. Om du kör en Azure Database for PostgreSQL – enskild server med 16 virtuella kärnor som matchar resten av attributen för reservationen debiteras du Betala per användning-priset för 8 virtuella kärnor för din beräkningsanvändning för PostgreSQL – enskild server och du får en reservationsrabatt för PostgreSQL – enskild server med 8 virtuella kärnor.</br>

För resten av dessa exempel förutsätter vi att den reserverade kapaciteten för Azure Database for PostgreSQL – enskild server som du köper är avsedd för Azure Database for PostgreSQL – enskild server med 16 virtuella kärnor och att resten av reservationens attribut matchar de enskilda Azure Database for PostgreSQL-servrar som körs.

* **Exempel 2**: du kör två Azure Database for PostgreSQL enskilda servrar med 8 vCore per timme. Reservationsrabatten för 16 virtuella kärnor används för beräkningsanvändning för båda Azure Database for PostgreSQL – enskild server med 8 virtuella kärnor.

* **Exempel 3**: du kör 1 16 VCore Azure Database for PostgreSQL en server från 1 pm till 1:30 PM. Du kör en annan Azure Database for PostgreSQL – enskild server med 16 virtuella kärnor från kl. 13:30 till kl. 14:00. Båda omfattas av reservationsrabatten.

* **Exempel 4**: du kör 1 16 VCore Azure Database for PostgreSQL en server från 1 pm till 1:45 PM. Du kör en annan Azure Database for PostgreSQL – enskild server med 16 virtuella kärnor från kl. 13:30 till kl. 14:00. Du debiteras Betala per användning-priset för överlappningen på 15 minuter. Reservationsrabatten gäller för beräkningsanvändningen för resten av tiden.

Information om hur du förstår och visar tillämpningen av dina Azure-reservationer i rapporterna för faktureringsanvändning finns i avsnittet om [hur Azure-reservationsanvändning fungerar](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss
Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
