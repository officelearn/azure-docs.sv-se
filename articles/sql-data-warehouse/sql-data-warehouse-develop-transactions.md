---
title: Använda transaktioner i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att genomföra transaktioner i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 03/22/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 0b4ce6f4479552f42d32124149f64614b7e3cb70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439187"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Använda transaktioner i SQL Data Warehouse
Tips för att genomföra transaktioner i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="what-to-expect"></a>Vad du kan förvänta dig
Som förväntat, stöder SQL Data Warehouse transaktioner som en del av arbetsbelastningen för informationslager. Men för att säkerställa att prestandan för SQL Data Warehouse underhålls i stor skala är vissa funktioner begränsade jämfört med SQL Server. Den här artikeln visar skillnaderna och visar en lista över de andra. 

## <a name="transaction-isolation-levels"></a>Transaktionsisoleringsnivåer
SQL Data Warehouse implementerar ACID-transaktioner. Dock är isoleringsnivå för transaktionellt stöd begränsat till READ UNCOMMITTED; den här nivån kan inte ändras. Om READ UNCOMMITTED är ett problem kan implementera du ett antal kodning metoder för att förhindra felaktiga läsningar av data. De mest populära metoderna använder både CTAS och tabellen växla partition (kallas ofta för mönstret glidande fönstret) för att hindra användare från att köra frågor mot data som förbereds fortfarande. Vyer som förfiltrera data är också ett populärt sätt.  

## <a name="transaction-size"></a>Transaktionstorlek
En enda ändring av transaktion har begränsad storlek. Gränsen tillämpas per distribution. Därför kan den totala allokeringen beräknas genom att multiplicera gränsen med antalet distribution. Att ungefärliga det maximala antalet rader i transaktionen dividera taket för distribution av den totala storleken på varje rad. Överväg att tar en genomsnittlig Kolumnlängd i stället för med den maximala storleken för variabel längd kolumner.

I tabellen nedan följande antaganden har gjorts:

* En jämn fördelning av data har uppstått 
* Den genomsnittliga radlängden är 250 byte

## <a name="gen2"></a>Gen2

| [DWU](sql-data-warehouse-overview-what-is.md) | Gräns per distribution (GB) | Antal distributioner | Maxstorlek för transaktionen (GB) | Antal rader per distribution | Maximalt antal rader per transaktion |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4,500 |300,000,000 |18,000,000,000 |
| DW15000c |112.5 |60 |6,750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>Gen1

| [DWU](sql-data-warehouse-overview-what-is.md) | Gräns per distribution (GB) | Antal distributioner | Maxstorlek för transaktionen (GB) | Antal rader per distribution | Maximalt antal rader per transaktion |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

Storleksgräns för transaktioner används per transaktion eller åtgärden. Den används inte i alla samtidiga transaktioner. Varje transaktion tillåts därför att skriva den här mängden data in i. 

För att optimera och minska mängden data som skrivs till loggen, finns det [Metodtips för transaktioner](sql-data-warehouse-develop-best-practices-transactions.md) artikeln.

> [!WARNING]
> Den maximala transaktionstorlek kan bara ske för HASH eller ROUND_ROBIN distribuerade tabeller där spridningen av data är ännu. Om transaktionen skriver data på ett skeva sätt till distributionerna är det troligt att nå innan den maximala transaktionstorlek med gränsen.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Transaktionstillstånd
SQL Data Warehouse använder funktionen XACT_STATE() för att rapportera en misslyckad transaktion med hjälp av värdet -2. Det här värdet innebär transaktionen misslyckades och har markerats för endast återställning.

> [!NOTE]
> Användning av -2 av funktionen XACT_STATE att ange en misslyckad transaktion representerar olika beteenden till SQL Server. SQL Server används värdet -1 för att representera en transaktion. SQL Server kan tolerera fel i en transaktion utan har markerats som en icke-allokerad. Till exempel `SELECT 1/0` skulle orsaka ett fel men inte tvingar en transaktion i ett tillstånd som en icke-allokerad. SQL Server tillåter också läsningar i en icke-allokerad transaktion. SQL Data Warehouse tillåter dock inte att du gör detta. Om ett fel uppstår i en transaktion i SQL Data Warehouse-2-tillstånd anges automatiskt och du kommer inte att kunna göra Välj ytterligare instruktioner tills instruktionen har återställts. Det är därför viktigt att kontrollera att din programkod för att se om det använder XACT_STATE() som du kan behöva göra kodändringar.
> 
> 

Exempelvis kan du i SQL Server finns i en transaktion som ser ut som följande:

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

Föregående kod får följande felmeddelande visas:

Msg 111233, nivå 16, tillstånd 1, rad 1 111233; Den aktuella transaktionen har avbrutits och väntande ändringar har återställts. Orsak: En transaktion endast återställning återställdes inte uttryckligen innan en DDL-, DML- eller SELECT-instruktion.

Du kommer inte att hämta utdata för ERROR_ *-funktioner.

I SQL Data Warehouse måste koden ändras något:

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
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
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

Förväntat beteende observeras nu. Fel i transaktionen hanteras och funktionerna ERROR_ * ange värden som förväntat.

Allt som har ändrats är att ÅTERSTÄLLNINGEN av transaktionen måste inträffa innan läsning av information om felet i CATCH-blocket.

## <a name="errorline-function"></a>Error_Line() funktion
Det är också värt att SQL Data Warehouse inte implementera eller stöder funktionen ERROR_LINE(). Om du har du i din kod kan behöva du ta bort den för att vara kompatibel med SQL Data Warehouse. Använd fråga etiketter i din kod i stället för att implementera motsvarande funktioner. Mer information finns i den [etikett](sql-data-warehouse-develop-label.md) artikeln.

## <a name="using-throw-and-raiserror"></a>Med hjälp av THROW och RAISERROR
THROW är mer modern implementeringen för att öka undantag i SQL Data Warehouse men RAISERROR stöds också. Det finns några skillnader som är värda var uppmärksam på men.

* Användardefinierade fel meddelanden siffror får inte vara i intervallet 100 000 – 150 000 för THROW
* RAISERROR-felmeddelanden är högst 50 000
* Användning av sys.messages stöds inte

## <a name="limitations"></a>Begränsningar
SQL Data Warehouse har några andra begränsningar som är relaterade till transaktioner.

De är följande:

* Inga distribuerade transaktioner
* Inga kapslade transaktioner tillåts
* Spara punkter tillåts inte
* Inga namngivna transaktioner
* Inga markerade transaktioner
* Inget stöd för DDL, till exempel CREATE TABLE inuti en användardefinierad transaktion

## <a name="next-steps"></a>Nästa steg
Mer information om hur du optimerar transaktioner finns [Metodtips för transaktioner](sql-data-warehouse-develop-best-practices-transactions.md). Läs om andra Metodtips för SQL Data Warehouse i [Metodtips för SQL Data Warehouse](sql-data-warehouse-best-practices.md).

