---
title: Förstå Azure Reserved VM Instances rabatt | Microsoft Docs
description: Lär dig hur Azure reserverade VM-instanser rabatt tillämpas på virtuella datorer som körs.
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
ms.openlocfilehash: b112dd881d4b2e87e617111d00bc82c6151d7750
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370088"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Hur Azure reservationsrabatten tillämpas på virtuella datorer

När du köper en reserverad VM-instans för Azure kan tillämpas automatiskt rabatten på virtuella datorer som matchar de attribut och mängden reservationen. En reservation täcker beräkningskostnaderna för dina virtuella datorer.

En reservationsrabatten gäller för de virtuella basdatorerna som du köper från Azure Marketplace.

SQL-databas som reserverad kapacitet finns [förstå reserverade instanser rabatt](billing-understand-reservation-charges.md).

I följande tabell visas kostnaderna för den virtuella datorn när du har köpt en reserverad VM-instans. I samtliga fall måste du debiteras för lagring och nätverk enligt de vanliga under.

| Typ av virtuell dator  | Avgifter med reserverade VM-instans |
|-----------------------|--------------------------------------------|
|Linux-datorer utan ytterligare programvara | Reservationen beskriver dina VM-infrastrukturkostnader.|
|Virtuella Linux-datorer med debitering för programvara (till exempel Red Hat) | Reservationen täcker infrastrukturkostnaderna. Du debiteras för ytterligare programvara.|
|Windows-datorer utan ytterligare programvara |Reservationen täcker infrastrukturkostnaderna. Du debiteras för Windows-programvara.|
|Windows-datorer med ytterligare programvara (till exempel SQLServer) | Reservationen täcker infrastrukturkostnaderna. Du debiteras för Windows-program och för ytterligare programvara.|
|Windows-datorer med [Azure Hybrid-förmånen](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Reservationen täcker infrastrukturkostnaderna. Kostnader för Windows-programvara omfattas av Azure Hybrid-förmånen. Ytterligare programvara debiteras separat.|

## <a name="how-reservation-discount-is-applied"></a>Hur reservationsrabatten tillämpas

En reservationsrabatten är ”*användning – it-eller-förlora-it*”. Så om du inte har matchande resurser för en timme, förlorar sedan du en Reservationskvantitet för den timmen. Du kan inte utföra vidarebefordra oanvända reserverade timmar.

När du stänger en resurs kan tillämpas reservationsrabatten automatiskt till en annan matchande resurs i det specificerade omfånget. Om inga matchande resurser finns i det specificerade omfånget så är de reserverade timmarna *förlorad*.

## <a name="reservation-discount-for-non-windows-vms"></a>Reservationsrabatten för icke - Windows-datorer

 Azure rabatten används till att köra VM-instanser per timme. Reservationer som du har köpt matchas mot den användning som anges av virtuella datorer som körs ska tillämpas reservationsrabatten. För virtuella datorer som inte kan köras hela timmen, fylls reservationen från andra virtuella datorer som inte använder en reservation, inklusive virtuella datorer som körs samtidigt. I slutet av timmen låses reservation-programmet för virtuella datorer under timmen. En virtuell dator kan inte köras i en timme eller samtidiga virtuella datorer inom en timme Fyll inte timmes reservationen underutnyttjade reservationen för den timmen. I följande diagram illustrerar hur en reservation fakturerbara VM-användningen. Bilden är baserad på en reservationsköp och två matchande VM-instanser.

![Skärmbild av en tillämpad reservation och två matchande VM-instanser](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. All användning som är ovanför reservationsraden hämtar debiteras enligt de användningsbaserad betalning. Du inte är kostnad för all användning under raden reservationer eftersom det redan har betalats som en del av reservationsköp.
2. I timme 1, 1-instans som körs i 0,75 timmar och instans 2 som körs i 0,5 timmar. Total användning för timme 1 är 1,25 timmar. Priserna för användningsbaserad betalning debiteras du för de återstående 0,25 timmarna.
3. Båda instanserna kördes för 1 timme för timme 2 och timme 3. En instans täcks av reservationen och den andra debiteras enligt priserna för användningsbaserad betalning.
4. För timme 4, 1-instans som körs i 0,5 timmar och 2-instans som körs i 1 timme. Instansen 1 omfattas fullständigt av reservationen och 0,5 timmars instans 2 behandlas. Användningsbaserad betalning debiteras du för de återstående 0,5 timmarna.

För att förstå och visa tillämpningen av dina Azure-reservationer i fakturering användningsrapporter, se [förstå användningen av reserverade](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Reservationsrabatten för Windows-datorer

När du kör Windows VM-instanser, ska reservationen gälla för att täcka infrastrukturkostnaderna. Tillämpning av VM-infrastrukturkostnader reservationen för virtuella Windows-datorer är samma som för icke - Windows-datorer. Du debiteras separat för Windows-programvara på basis av per vCPU. Se [Windows kostnader för programvara med reservationer](billing-reserved-Instance-windows-software-costs.md). Du kan omfatta din Windows licenskostnaden med [Azure Hybrid-förmånen för Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Rabatten kan gälla olika storlekar

När du köper en reserverad VM-instans, om du väljer **optimerade för**: **instans storlek flexibilitet**, rabatt täckning beror på virtuella datorstorlek som du väljer. Reservationen kan använda för storlekar för virtuella datorer (VM) i gruppen för serien av samma storlek. Mer information finns i [VM-storlek flexibilitet med reserverade VM-instanser](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>Rabatten gäller för matchande ServiceType endast

En reservationsrabatten gäller enbart för VM-användning där den `ServiceType` värde i `AdditionalInfo` matchar reservationen som köps. Tillämpningen av rabatter för reserverade ignorerar den mätning som används för virtuella datorer och endast utvärderar `ServiceType`. Vet vilken tjänst som anger att du har köpt den virtuella datorn för. Du kan byta en icke-premium storage VM-reservation för en reservation för premium-lagring, eller på motsatt sätt.

## <a name="classic-vms-and-cloud-services"></a>Klassiska virtuella datorer och molntjänster

Reserverade VM-instanser gäller för både klassiska virtuella datorer och molntjänster när instansen storlek flexibilitet aktiveras automatiskt. Rabatten gäller endast för beräkningskostnaden för cloud services. När reservationsrabatten tillämpas på molntjänster, avgifter för användning är uppdelade i beräkningsavgifter (Linux mätaren) och cloud services-avgifter (cloud services management mätare). Mer information finns i [hur rabatten gäller för Cloud Services](billing-reserved-instance-windows-software-costs.md#cloud-services-software-meters-not-included-in-reservation-cost).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är reservationer för Azure?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Hantera reservationer för Azure](billing-manage-reserved-vm-instance.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Förstå användningen av reserverade för CSP-prenumerationer](/partner-center/azure-reservations)
- [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)
