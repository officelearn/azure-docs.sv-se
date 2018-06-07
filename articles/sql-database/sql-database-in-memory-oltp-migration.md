---
title: Minnesintern OLTP förbättrar prestanda för SQL-txn | Microsoft Docs
description: Anta Minnesintern OLTP att förbättra prestanda i en befintlig SQL-databas.
services: sql-database
author: jodebrui
manager: craigg
ms.reviewer: MightyPen
ms.service: sql-database
ms.custom: develop databases
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: jodebrui
ms.openlocfilehash: a2f0d901abfa0013a6f53bacd72a9f8db2e0fd99
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648055"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Använd InMemory-OLTP att förbättra din programprestanda i SQL-databas
[Minnesintern OLTP](sql-database-in-memory.md) kan användas för att förbättra prestanda för transaktionsbearbetning, datapåfyllning och tillfälligt datascenarier i [Premium-och Business kritiska](sql-database-service-tiers-vcore.md) databaser utan att öka prisnivån. 

> [!NOTE] 
> Lär dig hur [kvorum fördubblar viktiga databasen arbetsbelastning och sänka DTU med 70% med SQL-databas](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Följ dessa steg för att införa Minnesintern OLTP i den befintliga databasen.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Steg 1: Kontrollera att du använder en Premium och företag kritisk nivå-databas
Minnesintern OLTP stöds bara i Premium- och Business kritisk nivå-databaser. I minnet stöds om det returnerade resultatet är 1 (inte 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* står för *extrema transaktionsbearbetning*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Steg 2: Identifiera objekt för att migrera till InMemory-OLTP
SSMS innehåller en **översikt över transaktion prestanda Analysis** rapport som du kan köra mot en databas med en aktiv arbetsbelastning. Rapporten identifierar tabeller och lagrade procedurer som lämpar sig för migrering till InMemory-OLTP.

I SSMS för att generera rapporten:

* I den **Object Explorer**, högerklicka på databasnoden för din.
* Klicka på **rapporter** > **standardrapporter** > **översikt över transaktion prestanda Analysis**.

Mer information finns i [fastställa om en tabell eller en lagrad procedur ska flyttas till InMemory-OLTP](http://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Steg 3: Skapa en motsvarande testdatabas
Anta att rapporten anger din databas har en tabell som har nytta omvandlas till en minnesoptimerad tabell. Vi rekommenderar att du först testar för att bekräfta uppgifter genom att testa.

Du behöver en test kopia av produktionsdatabasen. Testdatabasen ska finnas på samma service tier nivå som produktionsdatabasen.

För att underlätta testningen kan justera testa databasen på följande sätt:

1. Ansluta till testdatabasen med hjälp av SSMS.
2. Ange databasalternativet enligt följande T-SQL-instruktion för att undvika att behöva alternativet WITH (SNAPSHOT) i frågor:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Steg 4: Migrera tabeller
Du måste skapa och fylla i en minnesoptimerad kopia av tabellen som du vill testa. Du kan skapa den med hjälp av antingen:

* Praktiska minne optimering guiden i SSMS.
* Manuell T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Guiden för optimering av minne i SSMS
Använder det här alternativet för migrering:

1. Ansluta till testdatabas med SSMS.
2. I den **Object Explorer**, högerklickar du på tabellen och klickar sedan på **minne optimering Advisor**.
   
   * Den **tabell minne optimering Advisor** guiden visas.
3. I guiden klickar du på **migrering validering** (eller **nästa** knappen) att se om tabellen innehåller alla funktioner som inte stöds i minnesoptimerade tabeller som inte stöds. Mer information finns i:
   
   * Den *minne optimering checklista* i [minne optimering Advisor](http://msdn.microsoft.com/library/dn284308.aspx).
   * [Transact-SQL-konstruktioner som inte stöds av Minnesintern OLTP](http://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrera till InMemory-OLTP](http://msdn.microsoft.com/library/dn247639.aspx).
4. Om tabellen innehåller inga funktioner som inte stöds, utföra advisor faktiska schemat och migrering av data åt dig.

#### <a name="manual-t-sql"></a>Manuell T-SQL
Använder det här alternativet för migrering:

1. Ansluta till din testdatabas med hjälp av SSMS (eller en liknande funktion).
2. Hämta det fullständiga T-SQL-skriptet för din tabell och dess index.
   
   * Högerklicka på noden tabell i SSMS.
   * Klicka på **skript tabell som** > **skapa till** > **nytt frågefönster**.
3. Lägg till WITH i skriptfönstret (MEMORY_OPTIMIZED = ON) i CREATE TABLE-instruktion.
4. Om det finns ett KLUSTRADE index måste du ändra det till NONCLUSTERED.
5. Byt namn på den befintliga tabellen med hjälp av SP_RENAME.
6. Skapa den nya minnesoptimerade kopian av tabellen genom att köra skriptet redigerade CREATE TABLE.
7. Kopiera data till en minnesoptimerad tabell med hjälp av INSERT... VÄLJ * TILL:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Steg 5 (valfritt): migrera lagrade procedurer
Funktionen i minnet kan också ändra en lagrad procedur för bättre prestanda.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Överväganden med internt kompilerade lagrade procedurer
En internt kompilerad lagrad procedur måste ha följande alternativ på dess T-SQL med-sats:

* NATIVE_COMPILATION
* SCHEMABINDING: vilket betyder att tabeller som den lagrade proceduren inte kan ha sina kolumndefinitionerna ändras på något sätt som påverkar den lagrade proceduren om du släppa den lagrade proceduren.

En inbyggd modul måste använda en stor [ATOMIC-block](http://msdn.microsoft.com/library/dn452281.aspx) för hantering av transaktioner. Det finns ingen roll för en explicit BEGIN TRANSACTION eller ROLLBACK TRANSACTION. Om din kod upptäcker ett brott mot en affärsregel, det kan säga upp atomic-block med en [UTLÖSA](http://msdn.microsoft.com/library/ee677615.aspx) instruktionen.

### <a name="typical-create-procedure-for-natively-compiled"></a>Vanliga CREATE PROCEDURE för internt kompilerade
T-SQL för att skapa en internt kompilerad lagrad procedur är vanligtvis liknar följande mall:

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

* För TRANSACTION_ISOLATION_LEVEL är ÖGONBLICKSBILDEN de vanligaste värdet för den internt kompilerade lagrade proceduren. En delmängd av de andra värdena kan också användas:
  
  * UPPREPBAR LÄSNING
  * SERIALISERBARA
* Värdet för SPRÅK måste finnas i vyn sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Så här migrerar du en lagrad procedur
Består av följande migreringssteg:

1. Hämta skriptet CREATE PROCEDURE till vanliga tolkad lagrad procedur.
2. Skriv om huvudet för att matcha den tidigare mallen.
3. Kontrollera om den lagrade proceduren T-SQL-kod använder alla funktioner som inte stöds för internt kompilerade lagrade procedurer. Implementera lösningar om det behövs.
   
   * Mer information finns i [migreringsproblem för internt kompilerade lagrade procedurer](http://msdn.microsoft.com/library/dn296678.aspx).
4. Byt namn på den gamla lagrade proceduren med hjälp av SP_RENAME. Eller helt enkelt.
5. Kör skriptet redigerade skapa proceduren T-SQL.

## <a name="step-6-run-your-workload-in-test"></a>Steg 6: Kör din arbetsbelastning i test
Köra en arbetsbelastning i testdatabasen som liknar den arbetsbelastning som körs i produktionsdatabasen. Detta bör visa prestandafördelar uppnås genom din användning av funktionen InMemory-tabeller och lagrade procedurer.

Viktiga attribut för arbetsbelastningen är:

* Antalet samtidiga anslutningar.
* Förhållandet mellan läsning och skrivning.

För att anpassa och köra testet arbetsbelastningen, Överväg att använda verktyget praktiska ostress.exe, som illustreras i [här](sql-database-in-memory.md).

Kör testet för att minimera Nätverksfördröjningen, i samma Azure geografiska region som där databasen finns.

## <a name="step-7-post-implementation-monitoring"></a>Steg 7: Efter implementering övervakning
Överväg att övervakning av prestanda effekterna av din InMemory-implementeringar i produktion:

* [Övervaka InMemory-lagringen](sql-database-in-memory-oltp-monitoring.md).
* [Övervaka Azure SQL Database med dynamiska hanteringsvyer](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Relaterade länkar
* [OLTP (i minnet optimering) i minnet](http://msdn.microsoft.com/library/dn133186.aspx)
* [Introduktion till internt kompilerade lagrade procedurer](http://msdn.microsoft.com/library/dn133184.aspx)
* [Klassificering av minne optimering](http://msdn.microsoft.com/library/dn284308.aspx)

