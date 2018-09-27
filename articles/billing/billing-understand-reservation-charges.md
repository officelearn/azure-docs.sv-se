---
title: Förstå Azure reservationer rabatt | Microsoft Docs
description: Lär dig hur en reservationsrabatten tillämpas på som kör SQL-databaser.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: cwatson
ms.openlocfilehash: ee73cb3164ce59136dd268853b8caa967a6f42e9
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393392"
---
# <a name="understand-how-an-azure-reservation-discount-is-applied-to-sql-databases"></a>Förstå hur ett Azure reservationsrabatten tillämpas på SQL-databaser

När du köper en Azure SQL Database reserverad kapacitet tillämpas i reservationsrabatten automatiskt till SQL-databaser som matchar de attribut och mängden reservationen. En reservation täcker beräkningskostnaderna för din SQL Database. Du debiteras för programvara, lagring och nätverk enligt de vanliga under. Du kan täcka licenskostnaden för SQL-databaser med [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/).

Reserverade instanser av virtuella datorer, se [förstå Azure Reserved VM Instances rabatt](billing-understand-vm-reservation-charges.md).

## <a name="reservation-discount-applied-to-sql-databases"></a>Reservationsrabatten tillämpas på SQL-databaser

 SQL Database rabatten reserverad kapacitet till att köra SQL-databaser per timme. Som du köper reservationen matchas till användningen av beräkning som genereras genom att köra SQL-databaser. För SQL-databaser som inte kör hela timmen, tillämpas reservationen automatiskt till andra SQL-databaser som matchar reservationen-attribut. Rabatten kan använda för SQL-databaser som körs samtidigt. Om du inte har SQL-databaser som körs under hela timmen som matchar reservationen attribut kan får du inte ut mesta möjliga av rabatten för den timmen.

I följande exempel visas hur SQL-databas reserverad kapacitet rabatten gäller baserat på antalet kärnor som du köpt och när de körs.

- Scenario 1: Du köpa en SQL-databas som reserverad kapacitet för en SQL-databas med 8 kärnor. Du kör en SQL-databas som matchar resten av reservationen attribut med 16 kärnor. Du debiteras användningsbaserade priset för 8 kärnor i SQL Database beräkning användning. Du får rabatten för en timmes 8 kärnor SQL Database beräkning användning.

I resten av de här exemplen förutsätter att du köper reserverade SQL-databas kapaciteten är för en SQL-databas med 16 kärnor och resten av reservation-attribut matchar de som kör SQL-databaser.

- Scenario 2: Du har kört två SQL-databaser med 8 kärnor i en timme. 16 kärnor reservationsrabatten tillämpas för att beräkna användning för både till 8 kärnor SQL-databaser.
- Scenario 3: Du kör ett 16-kärnig SQL-databas från 1 pm till 1:30 pm. Du kör en annan 16 kärnor SQL-databas från 1:30 till 14: 00. Båda omfattas av reservationsrabatten.
- Scenario 4: Du kör ett 16-kärnig SQL-databas från 1 pm till 1:45 pm. Du kör en annan 16 kärnor SQL-databas från 1:30 till 14: 00. Du debiteras användningsbaserad priset för den 15 minuters överlappar varandra. Rabatten gäller för den beräkning användningen av resten av tiden.

För att förstå och visa tillämpningen av dina Azure-reservationer i fakturering användningsrapporter, se [förstå Azure reservation användning](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för virtuella datorer med Azure reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Betala i förskott för beräkningsresurser för SQL-databas med Azure SQL Database reserverad kapacitet](../sql-database/sql-database-reserved-capacity.md)
- [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Förstå användningen av reserverade för CSP-prenumerationer](https://docs.microsoft.com/partner-center/azure-reservations)


## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
