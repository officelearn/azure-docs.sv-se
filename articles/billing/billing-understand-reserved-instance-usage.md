---
title: Förstå användningen av Azure-reservationer för prenumeration med användningsbaserad betalning | Microsoft Docs
description: Lär dig hur du läser din användning för att förstå hur Azure reservation för prenumerationen med användningsbaserad betalning används.
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
ms.date: 08/08/2018
ms.author: manshuk
ms.openlocfilehash: 1226b2f73d556da2ff7d73f6f322e0bd1590f915
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307066"
---
# <a name="understand-azure-reservation-usage-for-your-pay-as-you-go-subscription"></a>Förstå användningen av Azure reservation för prenumerationen med användningsbaserad betalning

Använda ReservationId från [Reservation sidan](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) och användningsfil från den [konton i Azure portal](https://account.azure.com) att utvärdera din reservation användning.

Om du är en kund med ett Enterprise-avtal kan se [förstå användningen av reserverade för Enterprise-registreringen.](billing-understand-reserved-instance-usage-ea.md).

Den här artikeln förutsätter att reservationen ska gälla för en prenumeration. Om reservationen ska gälla i mer än en prenumeration, ditt reservationsförmånen kan sträcka sig över flera användning CSV-filer.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Användningen av reserverade VM-instanser

Anta att du kör en Standard_DS1_v2 Windows virtuell dator i östra USA och dina reserverade VM-instans information ut i följande tabell i följande avsnitt:

| Fält | Värde |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Antal |1|
|SKU | Standard_DS1_v2|
|Region | usaöstra |

Maskinvara-delen av den virtuella datorn ingår eftersom den distribuerade virtuella datorn matchar reservationen-attribut. Vilka Windows-program som inte omfattas av den reserverade VM-instansen finns i [Azure reserverade VM-instanser Windows kostnader för programvara](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>Instruktionen i CSV-fil för virtuella datorer

Det här avsnittet av CSV-filen visar den totala användningen av din reservation. Använder du filtret på den **Mätarunderkategori** fält som innehåller **”Reservation-”**. Det se ut ungefär som på följande skärmbild:

![Skärmbild som visar filtrerade reservationsinformation för användning och kostnader](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Den **reserverade instanser – bas-VM** raden innehåller det totala antalet timmar som omfattas av reservationen. Den här raden är $0,00 eftersom reservationen täcker den. Den **reserverade Windows Server-instanser (1 kärna)** rad täcker kostnaden för Windows-programvara.

### <a name="daily-usage-section-of-csv-file"></a>Daglig användning i CSV-fil

Filtrera på **ytterligare information** och skriver i din **reservations-ID**. Följande skärmbild visar relaterade fält till reservationen.

![Skärmbild som visar information om daglig användning och kostnader](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** i den **ytterligare information** fältet är reservationen som tillämpas på den virtuella datorn.
2. **ConsumptionMeter** är mätnings-ID för den virtuella datorn.
3. Den **reserverade instanser – bas-VM** **Mätarunderkategori** rad utgör $0 kostnaden i instruktionen avsnittet. Kostnaden för att köra den här virtuella datorn har betalats av reservationen.
4. **Mäter ID** är mätnings-ID för reservationen. Kostnaden för den här mätaren är $0. Den här mätnings-id visas för alla virtuella datorer som är kvalificerad för reservationsrabatten.
5. Standard_DS1_v2 är en virtuell processor virtuella datorn och den virtuella datorn har distribuerats utan Azure Hybrid-förmånen. Den här mätaren omfattar så visas extra kostnaden för Windows-programvara. Du hittar den mätning som motsvarar D-serien 1 kärna VM [Azure reserverade VM-instanser Windows programvarukostnader](billing-reserved-instance-windows-software-costs.md). Om du har Azure Hybrid-förmånen kan tillämpas inte den här extra kostnad.

## <a name="usage-for-sql-database-reserved-capacity-reservations"></a>Användningen av SQL Database reserverad kapacitetsreservationer

Anta att du kör en SQL-databas Gen 4 i östra USA och din reservation informationen ser ut som i följande tabell i följande avsnitt:

| Fält | Värde |
|---| --- |
|ReservationId |446ec809-423D-467c-8c5c-bbd5d22906b1|
|Antal |2|
|Produkt| SQL Database Gen 4 (2 kärnor)|
|Region | usaöstra |

### <a name="statement-section-of-csv-file"></a>Instruktionen i CSV-fil

Filtrera på **användningen av reserverade instanser** mätningsnamn. Det se ut ungefär som på följande skärmbild:

![CSV-fil för SQL Database reserverad kapacitet](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

Den **användningen av reserverade instanser** raden innehåller det totala antalet kärntimmar som omfattas av reservationen. Priset som gäller $0 för den här raden som reservationen beskrivs kostnaden.

### <a name="detail-section-of-csv-file"></a>Avsnittet för information om i CSV-fil

Filtrera på **ytterligare information** och skriver i din **reservations-ID**. Följande skärmbild visar relaterade fält till SQL Database reserverad kapacitetsreservationen.

![CSV-fil för SQL Database reserverad kapacitet](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. **ReservationId** i den **ytterligare information** fältet är SQL Database reserverad kapacitetsreservationen som tillämpas på SQL-databasresurser.
2. **ConsumptionMeter** är mätnings-ID för SQL Database-resursen.
3. Den **mätnings-Id** är mätning reservation. Kostnaden för den här mätaren är $0. Alla SQL Database-resurser som uppfyller kraven för reservationsrabatten visar den här mätnings-ID i CSV-filen.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för virtuella datorer med Azure reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Betala i förskott för beräkningsresurser för SQL-databas med Azure SQL Database reserverad kapacitet](../sql-database/sql-database-reserved-capacity.md)
- [Hantera Azure-reservationer](billing-manage-reserved-vm-instance.md)
- [Förstå hur reservationsrabatten tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
