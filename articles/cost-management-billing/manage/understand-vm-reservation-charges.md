---
title: Förstå Azure Reserved VM Instances-rabatter
description: Lär dig hur rabatten för Azure Reserved VM Instances tillämpas på virtuella datorer som körs.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: bda93712dd9a7501fbfddf0e75b8ae3c0088ed55
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341248"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Så tillämpas rabatten för Azure-reservation på virtuella datorer

När du har köpt en reserverad instans för virtuella Azure-datorer tillämpas reservationsrabatten automatiskt på virtuella datorer som matchar reservationens attribut och kvantitet. En reservation omfattar beräkningskostnaderna för dina virtuella datorer.

En reservationsrabatt tillämpas på de virtuella datorer som du köper från Azure Marketplace.

Information som gäller reserverad SQL Database-kapacitet finns i avsnittet om att [förstå rabatten för reserverade Azure-instanser](../reservations/understand-reservation-charges.md).

I följande tabell visas kostnaderna för din virtuella dator efter att du har köpt en reserverad VM-instans. I alla dessa fall debiteras du för lagring och nätverk enligt de vanliga priserna.

| Typ av virtuell dator  | Avgifter med reserverad VM-instans |
|-----------------------|--------------------------------------------|
|Virtuella Linux-datorer utan ytterligare programvara | Reservationen omfattar dina kostnader för VM-infrastruktur.|
|Virtuella Linux-datorer med programvaruavgifter (till exempel Red Hat) | Reservationen omfattar kostnaderna för infrastruktur. Du debiteras för ytterligare programvara.|
|Virtuella Windows-datorer utan ytterligare programvara |Reservationen omfattar kostnaderna för infrastruktur. Du debiteras för Windows-programvara.|
|Virtuella Windows-datorer med ytterligare programvara (till exempel SQL Server) | Reservationen omfattar kostnaderna för infrastruktur. Du debiteras för Windows-programvara samt för ytterligare programvara.|
|Virtuella Windows-datorer med [Azure Hybrid-förmån](../../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Reservationen omfattar kostnaderna för infrastruktur. Kostnaderna för Windows-programvara omfattas av Azure Hybrid-förmånen. Eventuell ytterligare programvara debiteras separat.|

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatter *går förlorade om de inte används*. Om du inte har några matchande resurser för en viss timme förlorar du därmed en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du avslutar en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna *förlorade*.

## <a name="reservation-discount-for-non-windows-vms"></a>Reservationsrabatt för virtuella datorer som inte baseras på Windows

 Rabatten för Azure-reservation tillämpas per timme på VM-instanser som körs. De reservationer som du har köpt matchas mot den användning som skickas av de virtuella datorer som körs för att tillämpa reservationsrabatten. För virtuella datorer som kanske inte körs timmen ut fylls reservationen från andra virtuella datorer som inte använder en reservation, däribland virtuella datorer som körs samtidigt. I slutet av timmen låses reservationstillämpningen för virtuella datorer inom timmen. Om en virtuell dator inte körs i en hel timme, eller om samtidiga virtuella datorer inom timmen inte fyller timmen i reservationen, underutnyttjas reservationen för den timmen. Följande diagram illustrerar tillämpningen av en reservation för fakturerbar VM-användning. Bilden baseras på ett reservationsköp och två matchande VM-instanser.

![Skärmbild av en tillämpad reservation och två matchande VM-instanser](./media/understand-vm-reservation-charges/billing-reserved-vm-instance-application.png)

1. All användning som är ovanför reservationslinjen debiteras enligt de vanliga Betala per användning-priserna. Du debiteras inte för användning under reservationslinjen eftersom den redan har betalats som en del av reservationsköpet.
2. Under timme 1 körs instans 1 i 0,75 timmar och instans 2 körs i 0,5 timmar. Total användning för timme 1 är 1,25 timmar. Du debiteras Betala per användning-priser för de återstående 0,25 timmarna.
3. För timme 2 och timme 3 kördes båda instanserna 1 timme vardera. Den ena instansen omfattas av reservationen och den andra debiteras enligt Betala per användning-priserna.
4. För timme 4 körs instans 1 i 0,5 timmar och instans 2 körs i 1 timme. Instans 1 omfattas helt av reservationen och 0,5 timmar av instans 2 omfattas. Du debiteras Betala per användning-priser för de återstående 0,5 timmarna.

Information om hur du förstår och visar tillämpningen av dina Azure-reservationer i användningsrapporter för fakturering finns i [Förstå reservationsanvändning](../reservations/understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Reservationsrabatt för virtuella Windows-datorer

När du kör instanser av virtuella Windows-datorer tillämpas reservationen för att omfatta kostnaderna för infrastruktur. Tillämpningen av reservationen på kostnaderna för VM-infrastruktur för virtuella Windows-datorer är samma som för virtuella datorer utan Windows. Du debiteras separat för Windows-programvara per vCPU. Se [Kostnader för Windows-programvara med reservationer](../reservations/reserved-instance-windows-software-costs.md). Du kan täcka dina Windows-licensieringskostnader med [Azure Hybrid-förmån för Windows Server](../../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Rabatten kan tillämpas på olika storlekar

När du köper en reserverad VM-instans och väljer **Optimerad för flexibel instansstorlek** beror rabattens täckning på den VM-storlek du väljer. Den kan också gälla för andra VM-storlekar som är i samma serie av flexibel instansstorlek. Mer information finns i artikeln om [flexibel storlek för virtuella datorer med reserverade VM-instanser](../../virtual-machines/reserved-vm-instance-size-flexibility.md).

## <a name="premium-storage-vms-dont-get-non-premium-discounts"></a>Virtuella datorer i Premium Storage får inte rabatter som inte är premium

Här är ett exempel. Anta att du har köpt en reservation för fem virtuella Standard_D1-datorer. Då gäller reservationsrabatten endast för virtuella Standard_D1-datorer eller andra virtuella datorer i samma instansfamilj. Rabatten gäller inte för virtuella Standard_DS1-datorer eller andra storlekar i gruppen DS1 för flexibel instansstorlek.

Tillämpningen av reservationsrabatt ignorerar den mätare som används för virtuella datorer och utvärdera endast tjänsttyp. Titta på värdet `ServiceType` i `AdditionalInfo` för att fastställa informationen för instansens flexibilitetsgrupp/-serie för dina virtuella datorer. Värdena finns i din användningsfil (CSV).

Du kan inte ändra instansens flexibilitetsgrupp/-serie för reservationen direkt efter köpet. Men om du vill kan du *byta ut* en VM-reservation från en flexibilitetsinstansgrupp/-serie mot en annan.

## <a name="services-that-get-vm-reservation-discounts"></a>Tjänster som får rabatter för VM-reservation

Dina VM-reservationer kan gälla för VM-användning som skickas från flera tjänster – inte bara för dina VM-distributioner. Resurser som får reservationsrabatter varierar beroende på inställningen för flexibel instansstorlek.

### <a name="instance-size-flexibility-setting"></a>Inställning för flexibel instansstorlek

Inställningen för flexibel instansstorlek avgör vilka tjänster som får rabatterna för reserverad instans.

Oavsett om inställningen är på eller av tillämpas reservationsrabatter automatiskt på matchande VM-användning när *ConsumedService* är `Microsoft.Compute`. Kontrollera därför dina användningsdata med avseende på värdet för *ConsumedService*. Några exempel är:

- Virtuella datorer
- Skalningsuppsättningar för virtuella datorer
- Containertjänst
- Azure Batch-distributioner (i användarprenumerationsläge)
- Azure Kubernetes Service (AKS)
- Service Fabric

När inställningen är på tillämpas reservationsrabatter automatiskt på matchande VM-användning när *ConsumedService* är något av följande:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Kontrollera värdet för *ConsumedService* i dina användningsdata för att fastställa huruvida användningen är berättigad till reservationsrabatter.

Mer information om flexibel instansstorlek finns i artikeln om [flexibel storlek för virtuella datorer med reserverade VM-instanser](../../virtual-machines/reserved-vm-instance-size-flexibility.md).


## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

- [Vad är Azure-reservationer?](../reservations/save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Hantera reservationer för Azure](../reservations/manage-reserved-vm-instance.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](../reservations/understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](../reservations/understand-reserved-instance-usage-ea.md)
- [Förstå reservationsanvändning för CSP-prenumerationer](/partner-center/azure-reservations)
- [Kostnader för Windows-programvara ingår inte i reservationer](../reservations/reserved-instance-windows-software-costs.md)