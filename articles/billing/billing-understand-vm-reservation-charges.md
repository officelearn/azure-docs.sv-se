---
title: Förstå Azure Reserved VM Instances rabatt | Microsoft Docs
description: Läs om hur Azure reserverad VM instance-rabatt tillämpas på virtuella datorer som körs.
author: yashesvi
manager: yashar
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2019
ms.author: banders
ms.openlocfilehash: 191160035f516d818d5537c5c47f9604998c46f7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849997"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Hur Azure reservations rabatten tillämpas på virtuella datorer

När du har köpt en instans av en reserverad virtuell Azure-dator tillämpas reservations rabatten automatiskt på virtuella datorer som matchar attributen och mängden av reservationen. En reservation täcker beräknings kostnaderna för dina virtuella datorer.

En reservations rabatt gäller för de virtuella datorer som du köper från Azure Marketplace.

Mer SQL Database reserverad kapacitet finns i [förstå rabatt på reserverade instanser i Azure](billing-understand-reservation-charges.md).

I följande tabell visas kostnaderna för din virtuella dator efter att du har köpt en reserverad VM-instans. I samtliga fall debiteras du för lagring och nätverk enligt de normala priserna.

| Typ av virtuell dator  | Avgifter med reserverad VM-instans |
|-----------------------|--------------------------------------------|
|Virtuella Linux-datorer utan ytterligare program vara | Reservationen täcker dina kostnader för infrastruktur för virtuella datorer.|
|Virtuella Linux-datorer med program varu avgifter (till exempel Red Hat) | Reservationen täcker infrastruktur kostnaderna. Du debiteras för ytterligare program vara.|
|Virtuella Windows-datorer utan ytterligare program vara |Reservationen täcker infrastruktur kostnaderna. Du debiteras för Windows-programvara.|
|Virtuella Windows-datorer med ytterligare program vara (till exempel SQL Server) | Reservationen täcker infrastruktur kostnaderna. Du debiteras för Windows-programvara och för ytterligare program vara.|
|Virtuella Windows-datorer med [Azure Hybrid-förmån](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Reservationen täcker infrastruktur kostnaderna. Kostnaderna för Windows-programvaran omfattas av Azure Hybrid-förmån. Eventuell ytterligare program vara debiteras separat.|

## <a name="how-reservation-discount-is-applied"></a>Så här tillämpas reservations rabatten

En reservations rabatt är "*Använd-IT-eller-förlorad-IT*". Så om du inte har några matchande resurser i någon timme förlorar du en reservations kvantitet för den timmen. Det går inte att överföra oanvända reserverade timmar.

När du stänger av en resurs tillämpas reservations rabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna *förlorade*.

## <a name="reservation-discount-for-non-windows-vms"></a>Reservations rabatt för virtuella datorer som inte är Windows-datorer

 Reservations rabatten för Azure används för att köra virtuella dator instanser per timme. De reservationer som du har köpt matchar användningen av de virtuella datorer som körs för att tillämpa reservations rabatten. För virtuella datorer som inte kan köra hela timmen kommer reservationen att fyllas från andra virtuella datorer som inte använder en reservation, inklusive körning av virtuella datorer samtidigt. I slutet av timmen är reservations programmet för virtuella datorer i timmen låst. I händelse av att en virtuell dator inte körs för en timme eller samtidiga virtuella datorer inom timmen fyller du inte i timmen för reservationen, reservationen underutnyttjas för den timmen. Följande diagram illustrerar programmet för en reservation för fakturerbar VM-användning. Bilden baseras på ett reservations köp och två matchande VM-instanser.

![Skärm bild av en tillämpad reservation och två matchande VM-instanser](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. All användning som är ovanför reservations raden debiteras enligt de vanliga priserna för betala per användning. Du debiteras inte för all användning under reservations raden eftersom den redan har betalats som en del av reservations köpet.
2. I timmen 1 körs instans 1 i 0,75 timmar och instans 2 körs i 0,5 timmar. Total användning för timme 1 är 1,25 timmar. Du debiteras priserna för betala per användning för de återstående 0,25 timmarna.
3. För timme 2 och timme 3 kördes båda instanserna i 1 timme var och en. En instans omfattas av reservationen och den andra debiteras enligt priserna för betala per användning.
4. För timme 4 körs instans 1 i 0,5 timmar och instans 2 körs i 1 timme. Instans 1 omfattas fullt ut av reservationen och 0,5 timmar instans 2 omfattas. Du debiteras priset för betala per användning för de återstående 0,5 timmarna.

Information om hur du kan förstå och Visa programmet för Azure Reservations i fakturerings användnings rapporter finns i [förstå reservations användning](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Reservations rabatt för virtuella Windows-datorer

När du kör instanser av virtuella Windows-datorer används reservationen för att försäkra infrastruktur kostnaderna. Reservationen av den virtuella datorns infrastruktur kostnader för virtuella Windows-datorer är samma som för virtuella datorer som inte kommer från Windows. Du debiteras separat för Windows-programvara per vCPU. Se [kostnader för Windows-programvara med reservationer](billing-reserved-Instance-windows-software-costs.md). Du kan få dina Windows-licensierings kostnader med [Azure Hybrid-förmån för Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Rabatt kan gälla för olika storlekar

När du köper en reserverad VM-instans, om du väljer **optimerad för**: **storleks flexibilitet för instans storlek**, beror rabatten på den virtuella dator storleken som du väljer. Reservationen kan tillämpas på de virtuella datorernas (VM) storlekar i samma storleks serie grupp. Mer information finns i [storleks flexibilitet för virtuella datorer med reserverade VM-instanser](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>Rabatt gäller endast för matchande ServiceType

En reservations rabatt gäller enbart VM-användningen där `ServiceType` värdet i `AdditionalInfo` matchar reservationen som har köpts. Reservations rabatt programmet ignorerar den mätare som används för virtuella datorer och `ServiceType`utvärderas bara. Vet vilken tjänst typ som du har köpt den virtuella datorn för. Du kan byta ut reservation för en virtuell dator för en Premium Storage-reservation eller på motsatt sätt.

## <a name="services-that-get-vm-reservation-discounts"></a>Tjänster som hämtar rabatter för VM-reservationer

Dina VM-reservationer kan gälla VM-användning som genereras från flera tjänster – inte bara för dina VM-distributioner. Resurser som erhåller reservations rabatter varierar beroende på storleks inställningen flexibilitet för instans storlek.

### <a name="instance-size-flexibility-setting"></a>Inställning av flexibilitet för instans storlek

Inställningen för flexibilitet för instans storlek avgör vilka tjänster som hämtar de reserverade instans rabatterna.

Oavsett om inställningen är på eller av, gäller reservations rabatter automatiskt för matchande VM-användning när *ConsumedService* är `Microsoft.Compute`. Kontrol lera därför användnings data för *ConsumedService* -värdet. Några exempel är:

- Virtuella datorer
- Skalningsuppsättningar för virtuella datorer
- Container tjänst
- Azure Batch distributioner (i användar prenumerations läge)
- Azure Kubernetes Service (AKS)
- Service Fabric

När inställningen är på tillämpas reservations rabatter automatiskt på matchande VM-användning när *ConsumedService* är något av följande:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft. Kusto

Kontrol lera värdet *ConsumedService* i dina användnings data för att avgöra om användningen är berättigad till reservations rabatter.

Mer information om flexibilitet för instans storlek finns i [storleks flexibilitet för virtuella datorer med reserverade VM-instanser](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).


## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en support förfrågan](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Reservations finns i följande artiklar:

- [Vad är reservationer för Azure?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Hantera reservationer för Azure](billing-manage-reserved-vm-instance.md)
- [Förstå reservations användningen för din prenumeration enligt principen betala per användning](billing-understand-reserved-instance-usage.md)
- [Förstå reservations användningen för företags registreringen](billing-understand-reserved-instance-usage-ea.md)
- [Förstå reservations användningen för CSP-prenumerationer](/partner-center/azure-reservations)
- [Windows-programkostnader som inte ingår i reservationer](billing-reserved-instance-windows-software-costs.md)
