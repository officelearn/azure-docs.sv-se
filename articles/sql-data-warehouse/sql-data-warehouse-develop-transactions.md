---
title: Transaktioner i SQL Data Warehouse | Microsoft Docs
description: "Tips för transaktioner i Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: ae621788-e575-41f5-8bfe-fa04dc4b0b53
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 29d53e18539f2c24dd64090b2ac6f9dd4c783961
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="transactions-in-sql-data-warehouse"></a>Transaktioner i SQL Data Warehouse
Precis som väntat stöder transaktioner som en del av arbetsbelastningen för informationslager i SQL Data Warehouse. För att säkerställa prestanda för SQL Data Warehouse underhålls i skala är vissa funktioner dock begränsad jämfört med SQL Server. Den här artikeln visar skillnaderna och visar en lista över de andra. 

## <a name="transaction-isolation-levels"></a>Transaktionsisoleringsnivåer
SQL Data Warehouse implementerar ACID-transaktioner. Isolering av det transaktionella stödet är dock begränsad till `READ UNCOMMITTED` och detta kan inte ändras. Du kan implementera ett antal kodning metoder för att förhindra felaktiga läsningar av data om det här är ett problem för dig. De mest populära metoderna utnyttja CTAS och tabellen partition växling (kallas ofta för glidande fönstret mönstret) för att förhindra att användare datafrågor som förbereds fortfarande. Vyer som före filtrerar data är också en populär metod.  

## <a name="transaction-size"></a>Transaktionsstorlek
En enda data ändras transaktion har begränsad storlek. Gränsen tillämpas idag ”per distribution”. Därför kan den totala fördelningen beräknas genom att multiplicera gränsen med antalet distribution. Att ungefärliga det maximala antalet rader i transaktionen delar taket för distribution av den totala storleken på varje rad. Överväg att tar en genomsnittlig kolumnlängden i stället för med den maximala storleken för kolumner med variabel längd.

I tabellen nedan följande antaganden har gjorts:

* Det uppstod en jämn fördelning av data 
* Genomsnittlig radlängden är 250 byte

| [DWU][DWU] | CAP per distribution (GiB) | Antal distributioner | Maxstorlek för transaktionen (GiB) | # Rader per distribution | Maximalt antal rader per transaktion |
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

Att optimera och minska mängden data som skrivs till loggfilen finns i den [transaktioner metodtips] [ Transactions best practices] artikel.

> [!WARNING]
> Den maximala transaktionsstorlek kan bara genomföras för HASH eller ROUND_ROBIN distribuerade tabeller där spridning av data är även. Om transaktionen skriver data i ett skeva sätt till distributioner är det troligt att nå innan den maximala transaktionsstorlek med gränsen.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Transaktionstillstånd
SQL Data Warehouse använder funktionen XACT_STATE() för att rapportera en misslyckad transaktion med värdet -2. Detta innebär att transaktionen misslyckades och har markerats för återställning endast

> [!NOTE]
> Använd 2 av funktionen XACT_STATE för att ange en misslyckad transaktion representerar olika beteenden till SQL Server. SQL Server använder värdet -1 för att representera en som transaktion. SQL Server kan tolerera fel inuti en transaktion utan att markeras som som. Till exempel `SELECT 1/0` skulle orsaka fel men inte att tvinga fram en transaktion till ett som tillstånd. SQL Server tillåter också läsningar som transaktionen. Dock kan SQL Data Warehouse du inte göra detta. Om ett fel uppstår i en transaktion i SQL Data Warehouse-2-tillståndet anges automatiskt och du kommer inte att kunna göra Markera ytterligare instruktioner tills instruktionen har återställts. Det är därför viktigt att kontrollera att din programkod för att se om det använder XACT_STATE() som du kan behöva göra ändringar i koden.
> 
> 

Till exempel i SQL Server kan du se en transaktion som ser ut så här:

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

Om du lämnar din kod som ovan får du följande felmeddelande:

Meddelande 111233, nivå 16, tillstånd 1, rad 1 111233; Den aktuella transaktionen har avbrutits och väntande ändringar har återställts. Orsak: En transaktion endast återställning har inte uttryckligen återställts innan DDL-, DML- eller SELECT-instruktion.

Du får också inte utdata från ERROR_ *-funktioner.

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

Alla som har ändrats är att den `ROLLBACK` av transaktionen hade ske innan läsning av information om felet i den `CATCH` block.

## <a name="errorline-function"></a>Error_Line() funktion
Det är också värt att nämna att SQL Data Warehouse inte implementera eller stöder funktionen ERROR_LINE(). Om du har du i koden behöver du ta bort det för att vara kompatibel med SQL Data Warehouse. Använd fråga etiketter i koden i stället för att genomföra motsvarande funktioner. Mer information finns i [etikett] [ LABEL] mer information om den här funktionen.

## <a name="using-throw-and-raiserror"></a>Med hjälp av THROW och RAISERROR
THROW är mer modern implementering för att öka undantag i SQL Data Warehouse men stöds även RAISERROR. Det finns några skillnader som är värt uppmärksam på men.

* Användardefinierade felmeddelanden siffror inte får vara i intervallet för THROW 150 100 000 000
* RAISERROR felmeddelanden korrigeras på 50 000
* Användning av sys.messages stöds inte

## <a name="limitiations"></a>Limitiations
SQL Data Warehouse har några andra begränsningar som relaterar till transaktioner.

De är följande:

* Inga distribuerade transaktioner
* Inga kapslade transaktioner tillåtna
* Spara punkter tillåts inte
* Inga namngivna transaktioner
* Inga markerade transaktioner
* Inget stöd för DDL som `CREATE TABLE` definieras inuti en transaktion

## <a name="next-steps"></a>Nästa steg
Mer information om hur du optimerar transaktioner finns [transaktioner metodtips][Transactions best practices].  Mer information om andra Metodtips för SQL Data Warehouse, se [SQL Data Warehouse metodtips][SQL Data Warehouse best practices].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Transactions best practices]: ./sql-data-warehouse-develop-best-practices-transactions.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
