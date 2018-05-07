---
title: Förstå Azure reserverade instans användning för din prenumeration med användningsbaserad betalning | Microsoft Docs
description: Lär dig att läsa din användning förstå tillämpningen av reserverade för prenumerationen betala per användning
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
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 142e172b3f4ab4b88bb3733f70d5e0fb252854ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Förstå reserverade användning för din prenumeration med användningsbaserad betalning

Förstå användning av reserverade instans med hjälp av ReservationId från [Reservation sidan](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) och användning av filen från [konton i Azure portal.](https://account.azure.com)


>[!NOTE]
>Den här artikeln gäller inte för EA-kunder. Om du är en EA-kund [förstå reserverade instans användning för Enterprise-registrering.](billing-understand-reserved-instance-usage-ea.md) Den här artikeln förutsätter också att reservationen tillämpas på en enda prenumeration. Om reservationen används för mer än en prenumeration, reservation förmån kan sträcka sig över flera användning csv-filer. 

I följande avsnitt i förutsätter att du kör en Standard_DS1_v2 Windows VM i östra USA region och dina reservation information ser ut som i följande tabell:

| Fält | Värde |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Antal |1|
|SKU | Standard_DS1_v2|
|Region | usaöstra |

## <a name="reservation-application"></a>Reservation program

Den maskinvara som del av den virtuella datorn omfattas eftersom den distribuerade virtuella datorn matchar reservation-attribut. Om du vill se vilka Windows-program inte omfattas av reserverade instans, gå till [Azure reservera VM-instanser Windows programvarukostnader.](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv"></a>Instruktionen avsnitt i csv
Det här avsnittet i din csv Visar total användning för din reservation. Använder du filtret på mätaren underkategorin fält som innehåller ”Reservation-” och dina data ser ut som följande skärmbild: ![direkt instruktionen Reservation](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Reservation Base VM raden innehåller det totala antalet timmar som omfattas av reservationen. Den här raden är $0.00 eftersom reserverade instansen täcker den. Windows-reservation Svr (1 kärna) rad täcker kostnaden för Windows-program.

### <a name="daily-usage-section-of-csv"></a>Daglig användning avsnitt i csv
Filtrera efter ytterligare information och Skriv i Reservation-ID. Följande skärmbild visar relaterade fält till reservationen. 

![Avgifter för daglig användning](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. ReservationId i ytterligare information om fältet är reservation som användes för att tillämpa fördelen att den virtuella datorn.
2. ConsumptionMeter är mätaren-Id för den virtuella datorn.
3. Reservation Base VM mätaren underkategorin rad representerar raden $0 kostnaden i instruktionen avsnittet. Kostnaden för att köra den här virtuella datorn har betalats av reservationen.
4. Detta är mätaren-Id för Reservation. Kostnaden för den här mätaren är 0. Varje virtuell dator som uppfyller kraven för reserverade har den här MeterId i CSV-fil för kontot för kostnaden. 
5. Standard_DS1_v2 är en vCPU Virtuella och den virtuella datorn distribueras utan stöd för Azure-Hybrid. Den här mätaren omfattar därför extra kostnad för Windows-program. Se [Azure reservera VM-instanser Windows programvarukostnader.](billing-reserved-instance-windows-software-costs.md) att hitta mätaren motsvarar D-serien 1 kärna VM. Om Azure Hybrid förmånen används, tillämpas inte den här extra kostnad. 

## <a name="next-steps"></a>Nästa steg
Mer information om reserverade instanser för virtuella datorer, finns i följande artiklar.

- [Förskottsbetala virtuella datorer med reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera reserverade virtuella Datorinstanser](billing-manage-reserved-vm-instance.md)
- [Spara pengar på virtuella datorer med den reserverade virtuella instanser](billing-save-compute-costs-reservations.md)
- [Förstå hur reserverade virtuella datorinstans rabatt används](billing-understand-vm-reservation-charges.md)
- [Förstå reserverade användning för Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Windows programvarukostnader ingår inte i reserverade instanser](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.