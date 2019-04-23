---
title: Förstå reservationer rabatt för Azure SQL-databaser | Microsoft Docs
description: Lär dig hur en reservationsrabatten tillämpas på med Azure SQL-databaser.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 4b4c6b390e9b3a0cf764f998523fe3c1cdc66026
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997435"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>Hur en reservationsrabatten tillämpas på Azure SQL-databaser

När du köper en Azure SQL Database reserverad kapacitet tillämpas i reservationsrabatten automatiskt till SQL-databaser som matchar de attribut och mängden reservationen. En reservation täcker beräkningskostnaderna för din SQL Database. Du debiteras för programvara, lagring och nätverk enligt de vanliga under. Du kan täcka licenskostnaden för SQL-databaser med [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/).

Reserverade instanser av virtuella datorer, se [förstå Azure Reserved VM Instances rabatt](billing-understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Hur reservationsrabatten tillämpas

En reservationsrabatten är ”*användning – it-eller-förlora-it*”. Så om du inte har matchande resurser för en timme, förlorar sedan du en Reservationskvantitet för den timmen. Du kan inte utföra vidarebefordra oanvända reserverade timmar.

När du stänger en resurs kan tillämpas reservationsrabatten automatiskt till en annan matchande resurs i det specificerade omfånget. Om inga matchande resurser finns i det specificerade omfånget så är de reserverade timmarna *förlorad*.

## <a name="discount-applied-to-sql-databases"></a>Rabatt som tillämpats på SQL-databaser

 SQL Database rabatten reserverad kapacitet till att köra SQL-databaser per timme. Som du köper reservationen matchas till användningen av beräkning som genereras genom att köra SQL-databaser. För SQL-databaser som inte körs under en hel timme tillämpas automatiskt reservationen på andra SQL-databaser som matchar reservationsattributen. Rabatten kan använda för SQL-databaser som körs samtidigt. Om du inte har SQL-databaser som körs under hela timmen som matchar reservationen attribut kan får du inte ut mesta möjliga av rabatten för den timmen.

I följande exempel visas hur SQL-databas reserverad kapacitet rabatten gäller baserat på antalet kärnor som du köpt och när de körs.

- Scenario 1: Du köper en SQL-databas som reserverad kapacitet för en SQL-databas med 8 kärnor. Du kör en SQL-databas som matchar resten av reservationen attribut med 16 kärnor. Du debiteras användningsbaserade priset för 8 kärnor i SQL Database beräkning användning. Du får rabatten för en timmes 8 kärnor SQL Database beräkning användning.

I resten av de här exemplen förutsätter att du köper reserverade SQL-databas kapaciteten är för en SQL-databas med 16 kärnor och resten av reservation-attribut matchar de som kör SQL-databaser.

- Scenario 2: Du kan köra två SQL-databaser med 8 kärnor i en timme. 16 kärnor reservationsrabatten tillämpas för att beräkna användning för både till 8 kärnor SQL-databaser.
- Scenario 3: Du kör ett 16-kärnig SQL-databas från 1 pm till 1:30 pm. Du kör en annan 16 kärnor SQL-databas från 1:30 till 14: 00. Båda omfattas av reservationsrabatten.
- Scenario 4: Du kör ett 16-kärnig SQL-databas från 1 pm till 1:45 pm. Du kör en annan 16 kärnor SQL-databas från 1:30 till 14: 00. Du debiteras användningsbaserad priset för den 15 minuters överlappar varandra. Rabatten gäller för den beräkning användningen av resten av tiden.

För att förstå och visa tillämpningen av dina Azure-reservationer i fakturering användningsrapporter, se [förstå Azure reservation användning](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Förstå användningen av reserverade för CSP-prenumerationer](/partner-center/azure-reservations)
