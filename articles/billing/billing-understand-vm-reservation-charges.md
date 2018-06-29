---
title: Förstå Azure reserverade instanser rabatt | Microsoft Docs
description: Lär dig hur Azure reserverade VM-instans rabatt tillämpas på virtuella datorer som körs.
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
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: d1229a49a5bb3bf3198c91a748ed37b7a626c506
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064044"
---
# <a name="understand-how-the-reserved-instance-discount-is-applied"></a>Förstå hur de reserverade rabatterna används
När du köper en Azure-reserverade VM-instans tillämpas reserverade rabatten automatiskt på virtuella datorer som matchar de attribut och antalet reserverade instansen. En reserverad instans omfattar infrastrukturkostnader för de virtuella datorerna. I följande tabell visas kostnaderna för den virtuella datorn när du har köpt en reserverad instans. I samtliga fall måste du debiteras för lagring och nätverk i de normala takt.

| Typ av virtuell dator  | Tillägg med reserverade instans |    
|-----------------------|--------------------------------------------|
|Virtuella Linux-datorer utan ytterligare programvara | Den reserverade instansen omfattar VM-infrastrukturkostnader.|
|Linux virtuella datorer med programvara avgifter (till exempel Red Hat) | Den reserverade instansen omfattar kostnader med infrastruktur. Du debiteras för ytterligare programvara.|
|Virtuella Windows-datorer utan ytterligare programvara |Den reserverade instansen omfattar kostnader med infrastruktur. Du debiteras för Windows-program.|
|Virtuella Windows-datorer med ytterligare programvara (till exempel SQLServer) | Den reserverade instansen omfattar kostnader med infrastruktur. Du debiteras för Windows-program och för ytterligare programvara.|
|Virtuella Windows-datorer med [Hybridrapportering i Azure-förmån](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Den reserverade instansen omfattar kostnader med infrastruktur. Windows-programvarukostnader omfattas av Hybrid-förmån för Azure. Ytterligare programvara debiteras separat.| 

## <a name="application-of-reserved-instance-discount-to-non-windows-vms"></a>Tillämpning av reserverade rabatt till icke - Windows-datorer
 Azure reserverade instans rabatt tillämpas på VM-instanser som körs på timbasis. De reserverade instanser som du har köpt matchas Usage sänds av de virtuella datorerna som körs att använda reserverade rabatt. För virtuella datorer som inte kanske kan köras hela tiden, fylls den reserverade instansen från andra virtuella datorer som inte använder en reserverad instans, inklusive virtuella datorer som körs samtidigt. Reserverade programmet för virtuella datorer i en timme är låst i slutet av en timme. En virtuell dator körs inte i en timma eller samtidiga virtuella datorer inom en timme inte fyller timmes reserverade instansen underutnyttjade reserverade instansen för den timmen. Följande diagram illustrerar tillämpningen av en instans som är reserverade för fakturerbar användning av VM. Bilden är baserad på en reserverad instans inköp och två matchande VM-instanser.

![Skärmbild av en instans av tillämpade reserverade och två matchande VM-instanser](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Alla användning är över reserverade raden hämtar debiteras enligt regelbundna betalning per användning priser. Du är inte för användning under raden reserverade instanser eftersom det redan har betalats som en del av reserverade inköp.
2.  Instansen 1 körs 0,75 timmar timmen 1 och 2-instans som körs i 0,5 timmar. Total användning för timme 1 är 1,25 timmar. Du debiteras betalning per användning priser för återstående 0.25 timmar.
3.  Båda instanser kördes för 2 respektive timmarsformat 3, 1 timme. En instans som omfattas av den reserverade instansen och den andra debiteras med betalning per användning priser.
4.  För timme 4, 1-instans som körs i 0,5 timmar och 2-instans som körs i en timme. Instansen 1 omfattas fullständigt av reserverade instansen och 0,5 instans 2 timmar som omfattas. Du är debiteras hastigheten med betalning per användning för de återstående 0,5 timmarna.

För att förstå och visa tillämpningen av dina Azure reserverade instanser i fakturering användningsrapporter, se [förstå reserverade instans användning](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reserved-instance-discount-to-windows-vms"></a>Användning av reserverade rabatt på virtuella Windows-datorer
När du kör Windows VM-instanser tillämpas reserverade instansen så att den täcker kostnader med infrastruktur. Tillämpning av reserverade instansen för VM-infrastrukturkostnader för virtuella Windows-datorer är samma som för icke - Windows-datorer. Du debiteras separat för Windows-program på grundval av per vCPU. Se [Windows programvarukostnader med reserverade instanser](https://go.microsoft.com/fwlink/?linkid=862756). Du kan omfatta din Windows licensieringskostnader med [Azure Hybrid-förmån för Windows Server] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>Nästa steg
Mer information om reserverade instanser finns i följande artiklar:

- [Vad är Azure reserverade VM-instanser?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala virtuella datorer med Azure reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera reserverade instanser i Azure](billing-manage-reserved-vm-instance.md)
- [Förstå hur de reserverade rabatterna används](billing-understand-vm-reservation-charges.md)
- [Förstå reserverade användning för din prenumeration med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå reserverade användning för Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Förstå reserverade användning för CSP-prenumerationer](https://docs.microsoft.com/partner-center/azure-reservations)
- [Windows programvarukostnader ingår inte i reserverade instanser](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
