---
title: Förstå användningen av Azure-reservationer för företag | Microsoft Docs
description: Lär dig mer om att läsa din användning för att förstå hur Azure reservation för Enterprise-registreringen tillämpas.
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
ms.date: 09/28/2018
ms.author: cwatson
ms.openlocfilehash: a33a360d25930f1468f1c9240be2d0f306d682c7
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585755"
---
# <a name="understand-azure-reservation-usage-for-your-enterprise-enrollment"></a>Förstå användningen av Azure reservation för din Enterprise-registrering

Använd den **ReservationId** från [reservationer sidan](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) och användningsfil från den [EA-portalen](https://ea.azure.com) att utvärdera din reservation användning. Du kan också se reservation-användning i sammanfattningen för användning i [EA-portalen](https://ea.azure.com).

Om du har köpt reservationen i en användningsbaserad fakturering kontext [förstå användningen av reservation för prenumerationen med användningsbaserad betalning.](billing-understand-reserved-instance-usage.md)

## <a name="usage-for-reserved-virtual-machines-instances"></a>Användningen av reserverade instanser för virtuella datorer

Anta att du kör en Standard_D1_v2 Windows virtuell dator i östra USA och din reservation informationen ser ut som i följande tabell i följande avsnitt:

| Fält | Värde |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Antal |1|
|SKU | Standard_D1|
|Region | usaöstra |

Maskinvara-delen av den virtuella datorn ingår eftersom den distribuerade virtuella datorn matchar reservationen-attribut. Om du vill se vilka Windows-program som inte omfattas av reservationen, se [Azure reserverade VM-instanser Windows programvarukostnader](billing-reserved-instance-windows-software-costs.md).

### <a name="usage-in-csv-file-for-reserved-vm-instances"></a>Användning i CSV-filen för reserverade VM-instanser

Du kan hämta Enterprise användning CSV-fil från Enterprise portal. Filtrera efter i CSV-filen **ytterligare information** och skriver i din **ReservationID**. Följande skärmbild visar relaterade fält till reservationen:

![Enterprise Agreement (EA) csv för Azure reservation](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** i **ytterligare information** fältet motsvarar reservationen som tillämpas på den virtuella datorn.
2. **ConsumptionMeter** är mätnings-ID för den virtuella datorn.
3. **Mäter ID** är mätning för reservation med $0 kostnaden. Kostnaden för virtuell dator som körs betalas av den reserverade VM-instansen.
4. Standard_D1 är en virtuell processor virtuella datorn och den virtuella datorn har distribuerats utan Azure Hybrid-förmånen. Den här mätaren omfattar så visas extra kostnaden för Windows-programvara. Du hittar den mätning som motsvarar D-serien 1 kärna VM [Azure reserverade VM-instanser Windows programvarukostnader](billing-reserved-instance-windows-software-costs.md).  Om du har Azure Hybrid-förmånen används inte den här extra kostnad.

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>Användning för SQL Database och Cosmos DB reserverad kapacitetsreservationer

I följande avsnitt använder Azure SQL Database som exempel för att beskriva användningsrapporten. Du kan använda samma steg för att hämta användning för Azure Cosmos DB samt. 

Anta att du kör en SQL-databas Gen 4 i östra USA och din reservation informationen ser ut som i följande tabell:

| Fält | Värde |
|---| --- |
|ReservationId |8244e673-83e9-45ad-B54B-3f5295d37cae|
|Antal |2|
|Produkt| SQL Database Gen 4 (2 kärnor)|
|Region | usaöstra |

### <a name="usage-in-csv-file"></a>Användning i CSV-fil 

Filtrera på **ytterligare information** och skriver i din **reservations-ID**, och välj de nödvändiga **Mätningskategori** – Azure SQL database eller Azure Cosmos DB. Följande skärmbild visar relaterade fält till reservationen.

![Enterprise Agreement (EA) csv för SQL Database reserverad kapacitet](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. **ReservationId** i den **ytterligare information** fältet är reservationen som tillämpas på den SQL Database-resursen.
2. **ConsumptionMeter** är mätnings-ID för SQL Database-resursen.
3. **Mäter ID** är mätning för reservation med $0 kostnaden. En SQL Database-resurs som är kvalificerad för reservationen visar den här mätnings-ID i CSV-filen.

## <a name="usage-summary-page-in-enterprise-portal"></a>Sammanfattningssida för användning i Enterprise portal

Din användning av Azure reservation även visas i sammanfattningen för användning i Enterprise portal: ![Användningsöversikt för Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Du debiteras inte för maskinvarukomponent för den virtuella datorn eftersom de omfattas av reservation. För en SQL Database-reservation, visas en rad med **tjänstnamn** reserverad kapacitet som Azure SQL Database.
2. I det här exemplet kan har du inte Azure Hybrid-förmånen så debiteras du för den Windows-programvara som används med den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för virtuella datorer med Azure reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Betala i förskott för beräkningsresurser för SQL-databas med Azure SQL Database reserverad kapacitet](../sql-database/sql-database-reserved-capacity.md) 
- [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Förstå hur reservationsrabatten tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

