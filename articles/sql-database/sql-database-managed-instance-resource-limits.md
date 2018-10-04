---
title: Resursgränser för Azure SQL Database - hanterad instans | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure SQL Database-resursgränser för hanterade instanser.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop
manager: craigg
ms.date: 10/03/2018
ms.openlocfilehash: 6ae9b3f71cb5328c7f4a7ee8e43ec0aff8b5fcec
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2018
ms.locfileid: "48267792"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Översikt över Azure SQL Database Managed Instance resursbegränsningar

Den här artikeln innehåller en översikt över resursgränser för Azure SQL Database Managed Instance och innehåller information hur du skapar begäran om att öka regionala standardprenumerationsgränserna. 

> [!NOTE]
> Andra hanterad instans-begränsningar finns i [vCore-baserade inköpsmodellen](sql-database-managed-instance.md#vcore-based-purchasing-model) och [tjänstnivåer för hanterad instans](sql-database-managed-instance.md#managed-instance-service-tiers).

## <a name="instance-level-resource-limits"></a>På instansnivå resursbegränsningar

Hanterad instans har egenskaper och resursbegränsningar som beror på den underliggande infrastrukturen och arkitektur. Gränserna beror på maskinvara generation och service-nivån.

### <a name="hardware-generation-characteristics"></a>Generation maskinvaruegenskaper

Azure SQL Database Managed Instance kan distribueras på två maskinvara generationens (Gen4 och Gen5). Maskinvarugenerationer har olika egenskaper som beskrivs i följande tabell:

|   | **Generation 4** | **5: e generationen** |
| --- | --- | --- |
| Maskinvara | Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer, anslutna SSD vCore = 1 PP (fysiska kärnor) | Intel E5-2673 v4 (Broadwell) 2.3-GHz-processorer, snabb eNVM SSD, vCore = 1 LP (hyper-tråd) |
| Compute | 8, 16, 24 virtuella kärnor | 8, 16, 24, 32, 40, 64, 80 virtuella kärnor |
| Minne | 7 GB per vCore | 5.5 GB per vCore |
| Maximalt lagringsutrymme (affärskritisk) | 1TB | 1TB, 2TB, 4TB beroende på antalet kärnor |

### <a name="service-tier-characteristics"></a>Tjänstens nivån egenskaper

Hanterad instans har två tjänstnivåer - generell användning och affärskritisk (offentlig förhandsversion). Dessa nivåer ger olika funktioner som beskrivs i tabellen nedan:

| **Funktion** | **Generell användning** | **Alternativet affärskritisk (förhandsversion)** |
| --- | --- | --- |
| Antal virtuella kärnor\* | Gen4: 8, 16, 24<br/>Gen5: 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 8, 16, 24, 32, 40, 64, 80 |
| Minne | Gen4: 56GB - 156GB<br/>Gen5: 44GB – 440GB<br/>\*I proportion till antalet virtuella kärnor | Gen4: 56GB - 156GB <br/> Gen5: 44GB – 440GB<br/>\*I proportion till antalet virtuella kärnor |
| Maxstorlek för lagring | 8 TB | Gen 4: 1 TB <br/> 5: e generationen: <br/>-1 TB för 8, 16 virtuella kärnor<br/>– 2 TB för 24 virtuella kärnor<br/>-4 TB för 32, 40, 64, 80 virtuella kärnor |
| Maximalt lagringsutrymme per databas | Bestäms av den maximala lagringsstorleken per instans | Bestäms av den maximala lagringsstorleken per instans |
| Maximalt antal databaser per instans | 100 | 100 |
| Max databasfiler per instans | Upp till 280 | Obegränsat |
| Förväntade maximala lagringsutrymmet IOPS | 500-7500 IOPS per fil ([beror på data filstorlek](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)). | Beror på den underliggande SSD-hastigheten. |

## <a name="supported-regions"></a>Regioner som stöds

Hanterade Instanced kan bara skapas i [regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database). Om du vill skapa en hanterad instans i den region som inte stöds för närvarande kan du [skicka supportförfrågan via Azure-portalen](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Prenumerationstyper som stöds

Hanterad instans stöder för närvarande distribution endast för följande typer av prenumerationer:

- [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Molntjänstleverantör (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)

> [!NOTE]
> Den här begränsningen är tillfällig. Nya typer av prenumerationer aktiveras i framtiden.

## <a name="regional-resource-limitations"></a>Regionala resursbegränsningar

Stöds prenumerationstyper kan innehålla ett begränsat antal resurser per region. Hanterad instans har två standardgränser per Azure-region beroende på en typ av prenumerationstyp:

- **Undernät gränsen**: det maximala antalet undernät där hanterade instanser har distribuerats i en region.
- **Gränsen för antal instans**: det maximala antalet instanser som kan distribueras i en region.

I följande tabell visas regionala standardgränser för stöds prenumerationer:

|Prenumerationstyp| Maxantal undernät för hanterad instans | Maximalt antal instanser |Maxantal GP hanterade instanser *|Maxantal BC hanterade instanser *|
| :---| :--- | :--- |:--- |:--- |
|Användningsbaserad betalning|1 *|4 *|4 *|1 *|
|CSP |1 *|4 *|4 *|1 *|
|EA|3 **|12 **|12 **|3 **|

\* Du kan antingen distribuera 1 BC eller 4 GP-instanser i ett undernät så att det totala antalet ”instans enheter” i undernätet aldrig överstiger 4.

** Maximalt antal instanser i en tjänstnivå gäller om det finns inga instanser i en annan tjänstnivå. Om du planerar att blanda GP- och BC instanser inom samma undernät, Använd följande avsnitt som referens för tillåtna kombinationer. Det totala antalet undernät får inte överskrida 3 som en enkel regel och det totala antalet enheter för instansen får inte överstiga 12.

Dessa begränsningar kan utökas genom att skapa särskilda [supportförfrågan i Azure-portalen](#obtaining-a-larger-quota-for-sql-managed-instance) om du behöver fler hanterade instanser i den aktuella regionen. Alternativt kan skapa du nya hanterade instanser i en annan Azure-region utan att skicka supportförfrågningar.

> [!IMPORTANT]
> När du planerar din distribution, Överväg att en kritisk Business (BC)-instans (på grund av har lagts till redundans) Allmänt förbrukar 4 x mer kapacitet än en instans för allmänt syfte (GP). Så för dina beräkningar, 1 instans i GP = 1 instans enhet och 1 BC instans = 4 instans-enheter. För att förenkla din förbrukning analys mot standardgränserna sammanfatta instans enheter över alla undernät i den region där hanterade instanser har distribuerats och jämföra resultaten med enhet instansgränser för prenumerationstyp.

### <a name="deployment-options-for-gp-and-bc-deployments-within-the-same-subnet"></a>Distributionsalternativ för GP- och BC inom samma undernät

I följande exempel beskriver distributionen fall med icke-tomma undernät och blandat GP och BC tjänstnivåer.

|Antalet undernät|subnät 1|Subnät 2|Undernät 3|
|:---|:---|:---|:---|
|1|0 BC och upp till 12 GP<br>1 BC och upp till 8 GP<br>2 BC och upp till 4 GP<br>3 BC|Gäller inte| Gäller inte|
|2|0 BC, upp till 4 GP|0 BC, upp till 8 GP<br>1 BC, upp till 4 GP<br>2 BC|Gäller inte|
|2|1 BC|0 BC, upp till 8 GP<br>1 BC, upp till 4 GP<br>2 BC|Gäller inte|
|2|2 BC|0 BC, upp till 8 GP<br>1 BC, upp till 4 GP<br>2 BC|Gäller inte|
|3|1 BC, 0 GP|1 BC, 0 GP|0 BC, upp till 4 GP|
|3|1BC, 0 GP|0 BC, upp till 4 GP|1 BC, 0 GP|
|3|0 BC, upp till 4 GP|1 BC, 0 GP|1BC, 0 GP|

### <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Hämta en större kvot för SQL-hanterad instans

Så här initierar processen för att hämta en större kvot:

1. Öppna **hjälp + support**, och klicka på **ny supportbegäran**. 

   ![Hjälp och support](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. På fliken grunderna för ny supportbegäran:
   - För **typ av problem**väljer **begränsningar för tjänsten och -prenumeration (kvoter)**.
   - I fältet **Prenumeration** väljer du din prenumeration.
   - För **kvottypen**väljer **SQL Database Managed Instance**.
   - För **supportavtal**, Välj ditt supportavtal.

     ![Problemet typ av kvot](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Klicka på **Nästa**.
4. På fliken Problem för ny supportbegäran:
   - För **allvarlighetsgrad**, Välj problemets allvarlighetsgrad.
   - För **information**, ger ytterligare information om problemet, inklusive felmeddelanden.
   - För **filuppladdning**, bifoga en fil med mer information (upp till 4 MB).

     ![Uppgifter om problem](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > En giltig begäran bör innehålla:
     > - Region i vilken prenumeration behöver gränsen ökas
     > - Nödvändigt antal instanser per tjänstnivå i befintliga undernät efter kvoten öka (om någon av de befintliga undernät måste utvidgas till
     > - Obligatoriskt antal nya undernät och Totalt antal instanser per tjänstnivå inom de nya undernäten (om du behöver distribuera hanterade instanser i nya undernät).
5. Klicka på **Nästa**.
6. Ange önskad kontaktmetod (e-post eller telefon) och kontaktuppgifter på fliken kontaktuppgifter för ny supportbegäran.
7. Klicka på **Skapa**.

## <a name="next-steps"></a>Nästa steg

- Mer information om Managed Instance finns i [vad är en hanterad instans?](sql-database-managed-instance.md). 
- Information om priser finns i [priser för SQL Database Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Information om hur du skapar din första hanterad instans finns [snabbstartsguiden](sql-database-managed-instance-get-started.md).
