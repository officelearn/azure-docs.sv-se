---
title: IN-Memory OLTP förbättrar SQL txn perf
description: Anta IN-Memory OLTP för att förbättra transaktionsprestanda i en befintlig SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: 653ed75341d5d56ecbe06cb59f0efafa1e68aa0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067275"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Använda IN-Memory OLTP för att förbättra programmets prestanda i SQL Database

[In-Memory OLTP](sql-database-in-memory.md) kan användas för att förbättra prestanda för transaktionsbearbetning, datainmatning och tillfälliga datascenarier i [Premium- och affärskritiska nivådatabaser](sql-database-service-tiers-vcore.md) utan att öka prisnivån. 

> [!NOTE] 
> Lär dig hur [Quorum fördubblar nyckeldatabasens arbetsbelastning samtidigt som du sänker DTU med 70 % med SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Följ dessa steg för att anta IN-Memory OLTP i din befintliga databas.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Steg 1: Se till att du använder en premium- och affärskritisk nivådatabas

OLTP i minnet stöds endast i Premium- och affärskritiska nivådatabaser. In-Memory stöds om det returnerade resultatet är 1 (inte 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* står för *Extreme Transaction Processing*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Steg 2: Identifiera objekt som ska migreras till OLTP i minnet
SSMS innehåller en **översiktsrapport för analys av transaktionsprestanda** som du kan köra mot en databas med en aktiv arbetsbelastning. Rapporten identifierar tabeller och lagrade procedurer som är kandidater för migrering till IN-Memory OLTP.

I SSMS, för att generera rapporten:

* Högerklicka på databasnoden i **Objektutforskaren.**
* Klicka på **Rapporter** > **Standard rapporter** > **Transaktionsprestandaanalys Översikt**.

Mer information finns i [Bestämma om en tabell eller lagrad procedur ska portas till IN-Memory OLTP](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Steg 3: Skapa en jämförbar testdatabas
Anta att rapporten anger att databasen har en tabell som skulle ha nytta av att konverteras till en minnesoptimerad tabell. Vi rekommenderar att du först testar för att bekräfta indikationen genom att testa.

Du behöver en testkopia av produktionsdatabasen. Testdatabasen ska ha samma tjänstnivå som produktionsdatabasen.

För att underlätta testningen, justera testdatabasen enligt följande:

1. Anslut till testdatabasen med hjälp av SSMS.
2. Om du vill undvika att behöva alternativet MED (SNAPSHOT) i frågor anger du databasalternativet enligt följande T-SQL-uttryck:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Steg 4: Migrera tabeller
Du måste skapa och fylla i en minnesoptimerad kopia av tabellen som du vill testa. Du kan skapa den med hjälp av antingen:

* Guiden Minnesoptimering praktiskt minne i SSMS.
* Manuell T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Guiden Minnesoptimering i SSMS
Så här använder du det här migreringsalternativet:

1. Anslut till testdatabasen med SSMS.
2. Högerklicka på tabellen i **Objektutforskaren**och klicka sedan på **Memory Optimization Advisor**.
   
   * Guiden **Tabellminnesoptimerrådgivare** visas.
3. Klicka på **Migreringsvalidering** (eller knappen **Nästa)** i guiden för att se om tabellen har några funktioner som inte stöds i minnesoptimerade tabeller. Mer information finns i:
   
   * *Checklista för minnesoptimering* i [Memory Optimization Advisor](https://msdn.microsoft.com/library/dn284308.aspx).
   * [Transact-SQL-konstruerar stöds inte av IN-Memory OLTP](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrera till IN-Memory OLTP](https://msdn.microsoft.com/library/dn247639.aspx).
4. Om tabellen inte har några funktioner som inte stöds kan rådgivaren utföra det faktiska schemat och datamigrering för dig.

#### <a name="manual-t-sql"></a>Manuell T-SQL
Så här använder du det här migreringsalternativet:

1. Anslut till testdatabasen med hjälp av SSMS (eller ett liknande verktyg).
2. Skaffa det fullständiga T-SQL-skriptet för tabellen och dess index.
   
   * Högerklicka på tabellnoden i SSMS.
   * Klicka på **Skripttabell som** > **SKAPA i nytt** > **frågefönster**.
3. Lägg till MED (MEMORY_OPTIMIZED = ON) i SKAPA TABELL-satsen i skriptfönstret.
4. Om det finns ett CLUSTERED-index ändrar du det till NONCLUSTERED.
5. Byt namn på den befintliga tabellen med hjälp av SP_RENAME.
6. Skapa den nya minnesoptimerade kopian av tabellen genom att köra det redigerade CREATE TABLE-skriptet.
7. Kopiera data till den minnesoptimerade tabellen med hjälp av INSERT... VÄLJ * TILL:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Steg 5 (valfritt): Migrera lagrade procedurer
Funktionen I minnet kan också ändra en lagrad procedur för bättre prestanda.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Överväganden med inbyggda kompilerade lagrade procedurer
En inbyggt lagrad procedur måste ha följande alternativ på t-SQL WITH-satsen:

* NATIVE_COMPILATION
* SCHEMABINDING: vilket innebär att tabeller som den lagrade proceduren inte kan få sina kolumndefinitioner ändrade på något sätt som skulle påverka den lagrade proceduren, såvida du inte släpper den lagrade proceduren.

En inbyggd modul måste använda ett stort [ATOMIC-block](https://msdn.microsoft.com/library/dn452281.aspx) för transaktionshantering. Det finns ingen roll för en explicit BEGIN-TRANSAKTION eller för ROLLBACK-TRANSAKTION. Om din kod upptäcker ett brott mot en affärsregel kan den avsluta atomblocket med en [THROW-sats.](https://msdn.microsoft.com/library/ee677615.aspx)

### <a name="typical-create-procedure-for-natively-compiled"></a>Typisk CREATE-PROCEDUR för inbyggt kompilerat
Vanligtvis liknar T-SQL för att skapa en inbyggt lagrad procedur följande mall:

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* För TRANSACTION_ISOLATION_LEVEL är SNAPSHOT det vanligaste värdet för den inbyggda lagrade proceduren. En delmängd av de andra värdena stöds dock också:
  
  * REPETERBAR LÄSNING
  * Serialiseras
* Språkvärdet måste finnas i vyn sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Migrera en lagrad procedur
Migreringsstegen är:

1. Hämta skriptet SKAPA PROCEDUR till den vanliga tolkade lagrade proceduren.
2. Skriv om huvudet så att det matchar föregående mall.
3. Kontrollera om den lagrade proceduren T-SQL-kod använder några funktioner som inte stöds för inbyggda kompilerade lagrade procedurer. Implementera lösningar om det behövs.
   
   * Mer information finns i [Migreringsproblem för inbyggda lagrade procedurer](https://msdn.microsoft.com/library/dn296678.aspx).
4. Byt namn på den gamla lagrade proceduren med hjälp av SP_RENAME. Eller helt enkelt släppa den.
5. Kör det redigerade T-SQL-skriptet SKAPA PROCEDURE.

## <a name="step-6-run-your-workload-in-test"></a>Steg 6: Kör din arbetsbelastning i test
Kör en arbetsbelastning i testdatabasen som liknar den arbetsbelastning som körs i produktionsdatabasen. Detta bör avslöja prestandavinsten som uppnås genom din användning av funktionen I minnet för tabeller och lagrade procedurer.

Viktiga attribut för arbetsbelastningen är:

* Antal samtidiga anslutningar.
* Läs-/skrivförhållande.

Om du vill skräddarsy och köra testarbetsbelastningen kan du använda verktyget ostress.exe som visas [här](sql-database-in-memory.md).

För att minimera nätverksfördröjningen kör du ditt test i samma geografiska Azure-region där databasen finns.

## <a name="step-7-post-implementation-monitoring"></a>Steg 7: Övervakning efter genomförandet
Överväg att övervaka prestandaeffekterna av dina implementeringar i minnet i produktionen:

* [Bildskärm i minnet lagring](sql-database-in-memory-oltp-monitoring.md).
* [Övervaka Azure SQL Database med dynamiska hanteringsvyer](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Relaterade länkar
* [OLTP (optimering i minnet)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Introduktion till inbyggda kompilerade lagrade procedurer](https://msdn.microsoft.com/library/dn133184.aspx)
* [Rådgivare för minnesoptimering](https://msdn.microsoft.com/library/dn284308.aspx)

