---
title: Använda transaktioner i Synapse SQL-pool
description: Den här artikeln innehåller tips för att implementera transaktioner och utveckla lösningar i Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 558b16fc348728c507af1fa0260a67ccacefed0f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416151"
---
# <a name="use-transactions-in-synapse-sql-pool"></a>Använda transaktioner i Synapse SQL-pool

Den här artikeln innehåller tips för att implementera transaktioner och utveckla lösningar i SQL-poolen.

## <a name="what-to-expect"></a>Vad du kan förvänta dig

Som du förväntar dig stöder SQL-poolen transaktioner som en del av datalagerarbetsbelastningen. För att säkerställa att SQL-poolen underhålls i stor skala är vissa funktioner begränsade jämfört med SQL Server. I den här artikeln belysers skillnaderna.

## <a name="transaction-isolation-levels"></a>Isoleringsnivåer för transaktioner

SQL-poolen implementerar ACID-transaktioner. Isoleringsnivån för transaktionsstödet är standard för READ UNCOMMITTED.  Du kan ändra den till LÄS COMMITTED SNAPSHOT ISOLATION genom att aktivera READ_COMMITTED_SNAPSHOT databasalternativet för en användardatabas när den är ansluten till huvuddatabasen.  

När det har aktiverats körs alla transaktioner i den här databasen under LÄS COMMITTED SNAPSHOT ISOLATION och inställningen READ UNCOMMITTED på sessionsnivå kommer inte att uppfyllas. Mer information finns [i alter database set-alternativ (Transact-SQL).](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="transaction-size"></a>Transaktionsstorlek

En enda dataändringstransaktion är begränsad i storlek. Gränsen tillämpas per fördelning. Därför kan den totala allokeringen beräknas genom att multiplicera gränsen med fördelningsantalet.

Om du vill approximera det maximala antalet rader i transaktionen dividerar du distributionstaket med den totala storleken på varje rad. För kolumner med variabel längd bör du överväga att ta en genomsnittlig kolumnlängd i stället för att använda den maximala storleken.

I följande tabell har två antaganden gjorts:

* En jämn fördelning av data har skett
* Den genomsnittliga radlängden är 250 byte

## <a name="gen2"></a>Gen2 (På andra)

| [DWU](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Tak per fördelning (GB) | Antal fördelningar | MAX transaktionsstorlek (GB) | # Rader per fördelning | Max rader per transaktion |
| --- | --- | --- | --- | --- | --- |
| DW100c (på andra) |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c (på andra) |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c (på andra) |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c (på andra) |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4 500 |300,000,000 |18,000,000,000 |
| DW15000c |112.5 |60 |6 750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>Gen1 (På andra)

| [DWU](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Tak per fördelning (GB) | Antal fördelningar | MAX transaktionsstorlek (GB) | # Rader per fördelning | Max rader per transaktion |
| --- | --- | --- | --- | --- | --- |
| DW100 (PÅ) |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 (PÅ) |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 (PÅ) |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

Transaktionsstorleksgränsen tillämpas per transaktion eller åtgärd. Det tillämpas inte över alla samtidiga transaktioner. Därför är varje transaktion tillåten att skriva denna mängd data till loggen.

Om du vill optimera och minimera mängden data som skrivs till loggen läser du artikeln Om bästa praxis för transaktioner finns i artikeln För bästa praxis för [transaktioner.](sql-data-warehouse-develop-best-practices-transactions.md)

> [!WARNING]
> Den maximala transaktionsstorleken kan endast uppnås för HASH eller ROUND_ROBIN distribuerade tabeller där spridningen av data är jämn. Om transaktionen skriver data på ett skevt sätt till distributionerna kommer gränsen sannolikt att nås före den maximala transaktionsstorleken.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Transaktionstillstånd

SQL-poolen använder funktionen XACT_STATE() för att rapportera en misslyckad transaktion med värdet -2. Det här värdet innebär att transaktionen har misslyckats och markeras endast för återställning.

> [!NOTE]
> Användningen av -2 av funktionen XACT_STATE för att beteckna en misslyckad transaktion representerar ett annat beteende än SQL Server. SQL Server använder värdet -1 för att representera en transaktion som inte kan tillskrivas. SQL Server kan tolerera vissa fel i en transaktion utan att den behöver markeras som oförsändbar. Skulle till `SELECT 1/0` exempel orsaka ett fel men inte tvinga en transaktion till ett okommissabelt tillstånd.

SQL Server tillåter också läsningar i den icke-kommitterbara transaktionen. Sql-poolen låter dig dock inte göra detta. Om ett fel uppstår i en SQL-pooltransaktion går det automatiskt in i -2-tillståndet och du kan inte göra några ytterligare urvalssatser förrän uttrycket har återställts.

Därför är det viktigt att kontrollera att programkoden används för att se om den använder XACT_STATE() eftersom du kan behöva göra kodändringar.

I SQL Server kan du till exempel se en transaktion som ser ut så här:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Den föregående koden ger följande felmeddelande:

Msg 111233, nivå 16, stat 1, linje 1 111233; Den aktuella transaktionen har avbrutits och alla väntande ändringar har återställts. Orsaken till det här problemet är att en transaktion i ett återställningstillstånd inte uttryckligen återställs före en DDL-, DML- eller SELECT-sats.

Du får inte utdata från ERROR_*-funktionerna.

I SQL-poolen måste koden ändras något:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Det förväntade beteendet observeras nu. Felet i transaktionen hanteras och ERROR_*-funktionerna ger värden som förväntat.

Allt som har ändrats är att rollback för transaktionen måste ske innan läsningen av felinformationen i CATCH-blocket.

## <a name="error_line-function"></a>Error_Line() funktion

Det är också värt att notera att SQL-poolen inte implementerar eller stöder funktionen ERROR_LINE(). Om du har detta i koden måste du ta bort det för att vara kompatibel med SQL-poolen.

Använd frågeetiketter i koden i stället för att implementera motsvarande funktioner. Mer information finns i [label-artikeln.](sql-data-warehouse-develop-label.md)

## <a name="using-throw-and-raiserror"></a>Använda THROW och RAISERROR

THROW är den modernare implementeringen för att höja undantagen i SQL-poolen, men RAISERROR stöds också. Det finns några skillnader som är värda att uppmärksamma dock.

* Användardefinierade felmeddelandenummer kan inte finnas i intervallet 100 000 - 150 000 för THROW
* RAISERROR-felmeddelanden är fixerade till 50 000
* Användning av sys.messages stöds inte

## <a name="limitations"></a>Begränsningar

SQL-poolen har några andra begränsningar som relaterar till transaktioner.

Det här är skillnaderna:

* Inga distribuerade transaktioner
* Inga kapslade transaktioner tillåts
* Inga sparpoäng tillåtna
* Inga namngivna transaktioner
* Inga markerade transaktioner
* Inget stöd för DDL, till exempel SKAPA TABELL i en användardefinierad transaktion

## <a name="next-steps"></a>Nästa steg

Mer information om hur du optimerar transaktioner finns i [Metodtips för transaktioner](sql-data-warehouse-develop-best-practices-transactions.md). Mer information om andra metodtips för SQL-pool finns i [metodtips för SQL-pool](sql-data-warehouse-best-practices.md).
