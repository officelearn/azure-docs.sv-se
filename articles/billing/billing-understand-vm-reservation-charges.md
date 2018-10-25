---
title: Förstå Azure Reserved VM Instances rabatt | Microsoft Docs
description: Lär dig hur Azure reserverade VM-instanser rabatt tillämpas på virtuella datorer som körs.
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
ms.openlocfilehash: e43188459bf495748a054b0f24936c9141bd218c
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025730"
---
# <a name="understand-how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Förstå hur Azure reservationsrabatten tillämpas på virtuella datorer

När du köper en reserverad VM-instans för Azure kan tillämpas automatiskt rabatten på virtuella datorer som matchar de attribut och mängden reservationen. En reservation täcker beräkningskostnaderna för dina virtuella datorer.

SQL-databas som reserverad kapacitet finns [förstå reserverade instanser rabatt](billing-understand-reservation-charges.md).

I följande tabell visas kostnaderna för den virtuella datorn när du har köpt en reserverad VM-instans. I samtliga fall måste du debiteras för lagring och nätverk enligt de vanliga under.

| Typ av virtuell dator  | Avgifter med reserverade VM-instans |
|-----------------------|--------------------------------------------|
|Linux-datorer utan ytterligare programvara | Reservationen beskriver dina VM-infrastrukturkostnader.|
|Virtuella Linux-datorer med debitering för programvara (till exempel Red Hat) | Reservationen täcker infrastrukturkostnaderna. Du debiteras för ytterligare programvara.|
|Windows-datorer utan ytterligare programvara |Reservationen täcker infrastrukturkostnaderna. Du debiteras för Windows-programvara.|
|Windows-datorer med ytterligare programvara (till exempel SQLServer) | Reservationen täcker infrastrukturkostnaderna. Du debiteras för Windows-program och för ytterligare programvara.|
|Windows-datorer med [Azure Hybrid-förmånen](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Reservationen täcker infrastrukturkostnaderna. Kostnader för Windows-programvara omfattas av Azure Hybrid-förmånen. Ytterligare programvara debiteras separat.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Tillämpning av reservationsrabatten till icke - Windows-datorer

 Azure rabatten används till att köra VM-instanser per timme. Reservationer som du har köpt matchas mot den användning som anges av virtuella datorer som körs ska tillämpas reservationsrabatten. För virtuella datorer som inte kan köras hela timmen, fylls reservationen från andra virtuella datorer som inte använder en reservation, inklusive virtuella datorer som körs samtidigt. I slutet av timmen låses reservation-programmet för virtuella datorer under timmen. En virtuell dator kan inte köras i en timme eller samtidiga virtuella datorer inom en timme Fyll inte timmes reservationen underutnyttjade reservationen för den timmen. I följande diagram illustrerar hur en reservation fakturerbara VM-användningen. Bilden är baserad på en reservationsköp och två matchande VM-instanser.

![Skärmbild av en tillämpad reservation och två matchande VM-instanser](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. All användning som är ovanför reservationsraden hämtar debiteras enligt de användningsbaserad betalning. Du inte är kostnad för all användning under raden reservationer eftersom det redan har betalats som en del av reservationsköp.
2. I timme 1, 1-instans som körs i 0,75 timmar och instans 2 som körs i 0,5 timmar. Total användning för timme 1 är 1,25 timmar. Priserna för användningsbaserad betalning debiteras du för de återstående 0,25 timmarna.
3. Båda instanserna kördes för 1 timme för timme 2 och timme 3. En instans täcks av reservationen och den andra debiteras enligt priserna för användningsbaserad betalning.
4. För timme 4, 1-instans som körs i 0,5 timmar och 2-instans som körs i 1 timme. Instansen 1 omfattas fullständigt av reservationen och 0,5 timmars instans 2 behandlas. Användningsbaserad betalning debiteras du för de återstående 0,5 timmarna.

För att förstå och visa tillämpningen av dina Azure-reservationer i fakturering användningsrapporter, se [förstå användningen av reserverade](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>Tillämpning av reservationsrabatten på Windows virtuella datorer

När du kör Windows VM-instanser, ska reservationen gälla för att täcka infrastrukturkostnaderna. Tillämpning av VM-infrastrukturkostnader reservationen för virtuella Windows-datorer är samma som för icke - Windows-datorer. Du debiteras separat för Windows-programvara på basis av per vCPU. Se [Windows kostnader för programvara med reservationer](https://go.microsoft.com/fwlink/?linkid=862756). Du kan omfatta din Windows licenskostnaden med [Azure Hybrid-förmånen för Windows Server](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Förstå användningen av reserverade för CSP-prenumerationer](https://docs.microsoft.com/partner-center/azure-reservations)
- [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
