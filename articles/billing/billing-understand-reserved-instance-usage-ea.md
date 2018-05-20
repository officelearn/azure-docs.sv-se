---
title: Förstå Azure reserverade instans användning för Enterprise - Azure Billing | Microsoft Docs
description: Lär dig hur du läsa hur du använder för att förstå hur Azure reserverade VM-instansen för Enterprise-registrering används.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: a92fce33b194c5cb7b763930e7fd11135f9fbd4f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>Förstå Azure reserverade instans användning för Enterprise-registrering
Förstå användning av reserverade instans med hjälp av den **ReservationId** från [reservationer sidan](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) och användning av filen från den [EA portal](https://ea.azure.com). Du kan också se reserverade användning i sammanfattningen för användning i [EA portal](https://ea.azure.com).

>[!NOTE]
>Om du har köpt den reserverade instans i en betalning per användning fakturering kontext finns [förstå reserverade instans användning för din prenumeration med användningsbaserad betalning.](billing-understand-reserved-instance-usage.md)

I följande avsnitt i förutsätter att du kör en Standard_D1_v2 Windows VM i east regionen USA och din reserverade instans information ser ut som i följande tabell:

| Fält | Värde |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Antal |1|
|SKU | Standard_D1|
|Region | usaöstra |

## <a name="reserved-instance-application"></a>Reserverade instans-program

Den maskinvara som del av den virtuella datorn omfattas eftersom den distribuerade virtuella datorn matchar reserverade-attribut. Om du vill se vilka Windows-program inte omfattas av reserverade instans, gå till Azure reserverade VM-instanser programvarukostnader, gå till [Azure reservera VM-instanser Windows programvarukostnader.](billing-reserved-instance-windows-software-costs.md)


### <a name="reserved-instance-usage-in-csv"></a>Användning av reserverade instans i csv
Du kan hämta EA användning csv från EA-portalen. Filtrera efter ytterligare information i hämtade csv-fil och skriva i dina **ReservationID**. Följande skärmbild visar relaterade fält till reserverade instans:

![Enterprise-avtal (EA) csv för reserverade Azure-instans](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** i ytterligare information om fältet motsvarar den reserverade instans som användes för att tillämpa fördelen att den virtuella datorn.
2. ConsumptionMeter är MeterId för den virtuella datorn.
3. Detta är Reservation mätaren med $0 kostnaden eftersom kostnaden för att köra VM har betalats av den reserverade instansen. 
4. Standard_D1 är en vCPU Virtuella och den virtuella datorn distribueras utan stöd för Azure-Hybrid. Den här mätaren omfattar därför extra kostnad för Windows-program. Se [Azure reservera VM-instanser Windows programvarukostnader.](billing-reserved-instance-windows-software-costs.md) att hitta mätaren motsvarar D-serien 1 kärna VM. Om Azure Hybrid förmånen används, tillämpas inte den här extra kostnad.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>Användning av reserverade instans i sammanfattningssida för användning i EA-portalen

Reserverade instans användning även visas i sammanfattningen för användning i EA portal: ![Användningsöversikt för Enterprise-avtal (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Du debiteras inte för maskinvarukomponent av den virtuella datorn som omfattas av reserverade instans. 
2. Du debiteras för Windows-program som Azure Hybrid förmånen inte används. 

## <a name="next-steps"></a>Nästa steg
Mer information om Azure reserverade instanser finns i följande artiklar:

- [Spara pengar på virtuella datorer med Azure reserverade instanser](billing-save-compute-costs-reservations.md)
- [Förskottsbetala virtuella datorer med reserverade instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera reserverade instanser](billing-manage-reserved-vm-instance.md)
- [Förstå hur de reserverade rabatterna används](billing-understand-vm-reservation-charges.md)
- [Förstå reserverade användning för din prenumeration med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Windows programvarukostnader ingår inte i reserverade instanser](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.