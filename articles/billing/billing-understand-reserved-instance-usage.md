---
title: Förstå Azure reserverade instans användning för din prenumeration med användningsbaserad betalning | Microsoft Docs
description: Lär dig hur du läsa hur du använder för att förstå hur Azure reserverade VM-instansen för din prenumeration med användningsbaserad betalning används.
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
ms.openlocfilehash: 7e303f3e5ce0e618d941be4190f6fadb40f2e09d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064053"
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Förstå reserverade användning för din prenumeration med användningsbaserad betalning

Förstå användningen av en Azure-reserverade VM-instans med hjälp av ReservationId från [Reservation sidan](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) och användning av filen från den [konton i Azure portal](https://account.azure.com).


>[!NOTE]
>Den här artikeln gäller inte för EA-kunder. Om du är en EA-kund [förstå reserverade instans användning för Enterprise-registrering.](billing-understand-reserved-instance-usage-ea.md) Den här artikeln förutsätter också att reserverade instansen tillämpas på en enda prenumeration. Om den reserverade instansen används för mer än en prenumeration, reserverade förmån kan sträcka sig över flera användning csv-filer. 

I följande avsnitt i förutsätter att du kör en Standard_DS1_v2 Windows VM i östra USA region och dina reserverade information ser ut som i följande tabell:

| Fält | Värde |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Antal |1|
|SKU | Standard_DS1_v2|
|Region | usaöstra |

## <a name="reserved-instance-application"></a>Reserverade program

Den maskinvara som del av den virtuella datorn omfattas eftersom den distribuerade virtuella datorn matchar reserverade-attribut. Om du vill se vilka Windows-program inte omfattas av reserverade instans, gå till [Azure reservera VM-instanser Windows programvarukostnader.](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv"></a>Instruktionen avsnitt i csv
Det här avsnittet i din csv Visar total användning för din reserverade instans. Använder du filtret på mätaren underkategori fält som innehåller ”Reservation-” och dina data ser ut som följande skärmbild: ![Skärmbild av filtrerade reserverade instans användningsinformation och kostnader](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Reservation Base VM raden innehåller det totala antalet timmar som omfattas av den reserverade instansen. Den här raden är $0.00 eftersom reserverade instansen täcker den. Windows-reservation Svr (1 kärna) rad täcker kostnaden för Windows-program.

### <a name="daily-usage-section-of-csv"></a>Daglig användning avsnitt i csv
Filtrera efter ytterligare information och Skriv i din **Reservation ID**. Följande skärmbild visar relaterade fält till reserverade instansen. 

![Skärmbild av dagliga användningsinformation och kostnader](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** i ytterligare information om fältet är reserverade instansen som används för att tillämpa fördelen att den virtuella datorn.
2. ConsumptionMeter är mätaren-Id för den virtuella datorn.
3. Reservation Base VM mätaren underkategori rad representerar raden $0 kostnaden i instruktionen avsnittet. Kostnaden för att köra den här virtuella datorn har betalats av den reserverade instansen.
4. Detta är mätaren-Id för reserverade instans. Kostnaden för den här mätaren är 0. Varje virtuell dator som uppfyller kraven för reserverade har den här MeterId i CSV-fil för kontot för kostnaden. 
5. Standard_DS1_v2 är en vCPU Virtuella och den virtuella datorn distribueras utan stöd för Azure-Hybrid. Den här mätaren omfattar därför extra kostnad för Windows-program. Se [Azure reservera VM-instanser Windows programvarukostnader.](billing-reserved-instance-windows-software-costs.md) att hitta mätaren motsvarar D-serien 1 kärna VM. Om Azure Hybrid förmånen används, tillämpas inte den här extra kostnad. 

## <a name="next-steps"></a>Nästa steg
Mer information om reserverade instanser finns i följande artiklar:

- [Vad är Azure reserverade VM-instanser?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala virtuella datorer med Azure reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera reserverade instanser i Azure](billing-manage-reserved-vm-instance.md)
- [Förstå hur de reserverade rabatterna används](billing-understand-vm-reservation-charges.md)
- [Förstå reserverade användning för Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Windows programvarukostnader ingår inte i reserverade instanser](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
