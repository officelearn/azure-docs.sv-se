---
title: Med transaktioner i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för transaktioner i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 168fd15b5f93f59328a4b6a2d68b52500074c410
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Med hjälp av transaktioner i SQL Data Warehouse
Tips för transaktioner i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="what-to-expect"></a>Vad du kan förvänta dig
Precis som väntat stöder transaktioner som en del av arbetsbelastningen för informationslager i SQL Data Warehouse. För att säkerställa prestanda för SQL Data Warehouse underhålls i skala är vissa funktioner dock begränsad jämfört med SQL Server. Den här artikeln visar skillnaderna och visar en lista över de andra. 

## <a name="transaction-isolation-levels"></a>Transaktionsisoleringsnivåer
SQL Data Warehouse implementerar ACID-transaktioner. Dock är isoleringsnivå för transaktionell stöd begränsad till READ UNCOMMITTED; den här nivån kan inte ändras. Om READ UNCOMMITTED är ett problem kan implementera du ett antal metoder för att förhindra felaktiga läsningar av data-kodning. De mest populära metoderna använda CTAS och tabellen partition växling (kallas ofta för glidande fönstret mönstret) för att förhindra att användare datafrågor som förbereds fortfarande. Vyer som före filtrerar data är också en populär metod.  

## <a name="transaction-size"></a>Transaktionsstorlek
En enda data ändras transaktion har begränsad storlek. Gränsen tillämpas per distribution. Därför kan den totala fördelningen beräknas genom att multiplicera gränsen med antalet distribution. Att ungefärliga det maximala antalet rader i transaktionen delar taket för distribution av den totala storleken på varje rad. Överväg att tar en genomsnittlig kolumnlängden i stället för med den maximala storleken för kolumner med variabel längd.

I tabellen nedan följande antaganden har gjorts:

* Det uppstod en jämn fördelning av data 
* Genomsnittlig radlängden är 250 byte

| [DWU](sql-data-warehouse-overview-what-is.md) | CAP per distribution (GiB) | Antal distributioner | Maxstorlek för transaktionen (GiB) | # Rader per distribution | Maximalt antal rader per transaktion |
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

Storleksgräns för transaktionen används per transaktion eller åtgärden. Det har inte tillämpats över alla samtidiga transaktioner. Varje transaktion tillåts därför att skriva den här mängden data i loggen. 

För att optimera och minska mängden data som skrivs till loggfilen måste referera till den [transaktioner metodtips](sql-data-warehouse-develop-best-practices-transactions.md) artikel.

> [!WARNING]
> Den maximala transaktionsstorlek kan bara genomföras för HASH eller ROUND_ROBIN distribuerade tabeller där spridning av data är även. Om transaktionen skriver data i ett skeva sätt till distributioner är det troligt att nå innan den maximala transaktionsstorlek med gränsen.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Transaktionstillstånd
SQL Data Warehouse använder funktionen XACT_STATE() för att rapportera en misslyckad transaktion med värdet -2. Det här värdet innebär transaktionen misslyckades och har markerats för återställning endast.

> [!NOTE]
> Använd 2 av funktionen XACT_STATE för att ange en misslyckad transaktion representerar olika beteenden till SQL Server. SQL Server använder värdet -1 för att representera en transaktion. SQL Server kan tolerera fel inuti en transaktion utan har markerats som en icke-allokerad. Till exempel `SELECT 1/0` skulle orsaka fel men inte att tvinga fram en transaktion i ett tillstånd som en icke-allokerad. SQL Server tillåter också läsningar i en icke-allokerad transaktion. Dock kan SQL Data Warehouse du inte göra detta. Om ett fel uppstår i en transaktion i SQL Data Warehouse-2-tillståndet anges automatiskt och du kommer inte att kunna göra Markera ytterligare instruktioner tills instruktionen har återställts. Det är därför viktigt att kontrollera att din programkod för att se om det använder XACT_STATE() som du kan behöva göra ändringar i koden.
> 
> 

Till exempel i SQL Server kan du se en transaktion som ser ut som följande:

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
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Föregående kod ger följande felmeddelande visas:

Meddelande 111233, nivå 16, tillstånd 1, rad 1 111233; Den aktuella transaktionen har avbrutits och väntande ändringar har återställts. Orsak: En transaktion endast återställning har inte uttryckligen återställts innan en DDL-, DML- eller SELECT-instruktion.

Du kommer inte att hämta utdata för ERROR_ *-funktioner.

Koden måste vara något ändras i SQL Data Warehouse:

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

Förväntat beteende observeras nu. Fel i transaktionen hanteras och funktioner ERROR_ * ange värden som förväntat.

Alla som har ändrats är att ÅTERSTÄLLNINGEN av transaktionen måste inträffa innan läsning av information om felet i CATCH-blocket.

## <a name="errorline-function"></a>Error_Line() funktion
Det är också värt att nämna att SQL Data Warehouse inte implementera eller stöder funktionen ERROR_LINE(). Om du har du i koden kan behöva du ta bort den för att vara kompatibel med SQL Data Warehouse. Använd fråga etiketter i koden i stället för att genomföra motsvarande funktioner. Mer information finns i [etikett](sql-data-warehouse-develop-label.md) artikel.

## <a name="using-throw-and-raiserror"></a>Med hjälp av THROW och RAISERROR
THROW är mer modern implementering för att öka undantag i SQL Data Warehouse men stöds även RAISERROR. Det finns några skillnader som är värt uppmärksam på men.

* Användardefinierade meddelanden felnummer får inte vara i intervallet för THROW 150 100 000 000
* RAISERROR felmeddelanden korrigeras på 50 000
* Användning av sys.messages stöds inte

## <a name="limitations"></a>Begränsningar
SQL Data Warehouse har några andra begränsningar som relaterar till transaktioner.

De är följande:

* Inga distribuerade transaktioner
* Inga kapslade transaktioner tillåtna
* Spara punkter tillåts inte
* Inga namngivna transaktioner
* Inga markerade transaktioner
* Inget stöd för DDL, till exempel skapa tabell inuti en användardefinierad transaktion

## <a name="next-steps"></a>Nästa steg
Mer information om hur du optimerar transaktioner finns [transaktioner metodtips](sql-data-warehouse-develop-best-practices-transactions.md). Mer information om andra Metodtips för SQL Data Warehouse, se [SQL Data Warehouse metodtips](sql-data-warehouse-best-practices.md).

