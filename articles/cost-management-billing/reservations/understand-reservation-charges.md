---
title: Förstå reservationsrabatt för Azure SQL-databaser | Microsoft Docs
description: Lär dig hur en reservationsrabatt tillämpas på Azure SQL-databaser som körs.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: f0b093950b7f8fbbfbb0ca8ceb48cb0aa6341e10
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200290"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>Så tillämpas en reservationsrabatt på Azure SQL-databaser

När du har köpt en reserverad Azure SQL Database-kapacitet tillämpas reservationsrabatten automatiskt på SQL-databaser som matchar reservationens attribut och kvantitet. En reservation omfattar beräkningskostnaderna för din SQL Database. Du debiteras för programvara, lagring och nätverk enligt de vanliga priserna. Du kan täcka licenskostnaderna för SQL-databaser med [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/).

Information om reserverade VM-instanser finns i artikeln om [hur rabatt för Azure Reserved VM Instances fungerar](../manage/understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatter *går förlorade om de inte används*. Om du inte har några matchande resurser för en viss timme förlorar du därmed en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du avslutar en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna *förlorade*.

## <a name="discount-applied-to-sql-databases"></a>Rabatt som tillämpas på SQL-databaser

 Rabatten för reserverad SQL Database-kapacitet tillämpas timvis på SQL-databaser som körs. Den reservation som du köper matchas mot den beräkningsanvändning som genereras av de SQL-databaser som körs. För SQL-databaser som inte körs timmen ut tillämpas reservationen automatiskt på andra SQL-databaser som matchar reservationsattributen. Rabatten kan gälla för SQL-databaser som körs samtidigt. Om du inte har några SQL-databaser som körs timmen ut och som matchar reservationsattributen får du inte den fullständiga fördelen med reservationsrabatten för den timmen.

I följande exempel visas hur rabatten för reserverad SQL Database-kapacitet tillämpas beroende på det antal kärnor som du har köpt och när de körs.

- Scenario 1: Du köper en reserverad SQL Database-kapacitet för en 8-kärnig SQL Database. Du kör en 16-kärnig SQL Database som matchar resten av reservationens attribut. Du debiteras Betala per användning-pris för 8 kärnor med SQL Database-beräkningsanvändning. Du får reservationsrabatten för en timme med 8-kärnig SQL Database-beräkningsanvändning.

För resten av dessa exempel förutsätter vi att den reserverade SQL Database-kapacitet som du köper är avsedd för en 16-kärnig SQL Database och att resten av reservationens attribut matchar de SQL-databaser som körs.

- Scenario 2: Du kör två 8-kärniga SQL-databaser i en timme. Reservationsrabatten för 16 kärnor används för beräkningsanvändning för båda de 8-kärniga SQL-databaserna.
- Scenario 3: Du kör en 16-kärnig SQL Database från kl. 13:00 till 13:30. Du kör en till 16-kärnig SQL Database från kl. 13:30 till 14:00. Båda omfattas av reservationsrabatten.
- Scenario 4: Du kör en 16-kärnig SQL Database från kl. 13:00 till 13:45. Du kör en till 16-kärnig SQL Database från kl. 13:30 till 14:00. Du debiteras Betala per användning-priset för överlappningen på 15 minuter. Reservationsrabatten gäller för beräkningsanvändningen för resten av tiden.

Information om hur du förstår och visar tillämpningen av dina Azure-reservationer i rapporterna för faktureringsanvändning finns i avsnittet om [hur Azure-reservationsanvändning fungerar](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

- [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [Hantera Azure Reservations](manage-reserved-vm-instance.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
- [Förstå reservationsanvändning för CSP-prenumerationer](/partner-center/azure-reservations)
