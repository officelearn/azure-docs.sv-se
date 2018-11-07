---
title: In-Memory OLTP förbättrar SQL txn perf | Microsoft Docs
description: Anta Minnesintern OLTP för att förbättra prestanda i en befintlig SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jodebrui
ms.author: jodebrui
ms.reviewer: MightyPen
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 4455e0c0f31c9026526820b50214efb83720da0d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228053"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Använda Minnesintern OLTP kan förbättra programprestanda i SQL-databas
[In-Memory OLTP](sql-database-in-memory.md) kan användas för att förbättra prestandan för transaktionsbearbetning och datainmatning tillfälliga datascenarier i [Premium och affärskritisk nivå](sql-database-service-tiers-vcore.md) databaser utan att öka prisnivån. 

> [!NOTE] 
> Lär dig hur [kvorum fördubblar viktiga databasarbetsbelastningen samtidigt som du minskar DTU med 70% med SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Följ dessa steg om du vill använda InMemory-OLTP i den befintliga databasen.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Steg 1: Kontrollera att du använder en databas för Premium och affärskritisk nivå
In-Memory OLTP stöds bara i databaser för Premium och affärskritisk nivå. InMemory-stöds om det returnerade resultatet är 1 (inte 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* står för *extrema transaktionsbearbetning*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Steg 2: Identifiera objekt för att migrera till In-Memory OLTP
SSMS innehåller en **transaktionsöversikt prestanda Analysis** rapport som du kan köra mot en databas med en aktiv arbetsbelastning. Rapporten identifierar tabeller och lagrade procedurer som lämpar sig för migrering till In-Memory OLTP.

I SSMS för att generera rapporten:

* I den **Object Explorer**, högerklicka på din databasnod.
* Klicka på **rapporter** > **standardrapporter** > **transaktionsöversikt prestanda Analysis**.

Mer information finns i [fastställa om en tabell eller en lagrad procedur ska flyttas till In-Memory OLTP](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Steg 3: Skapa en jämförbar testdatabas
Anta att rapporten anger din databas har en tabell som skulle med fördel omvandlas till en minnesoptimerad tabell. Vi rekommenderar att du först testar för att bekräfta uppgifter genom att testa.

Du behöver en kopia för testning av produktionsdatabasen. Testdatabasen ska vara på samma tjänstnivå av nivå som produktionsdatabasen.

För att underlätta testningen kan du justera testa databasen på följande sätt:

1. Ansluta till testdatabasen med hjälp av SSMS.
2. Om du vill slippa behöva alternativet WITH (SNAPSHOT) i frågor, ange databasalternativet enligt följande T-SQL-instruktion:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Steg 4: Migrera tabeller
Du måste skapa och fylla i en minnesoptimerade kopia av tabellen som du vill testa. Du kan skapa den genom att använda antingen:

* Praktisk minne optimering guiden i SSMS.
* Manuell T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Guiden för optimering av minne i SSMS
Använder det här alternativet för migrering:

1. Ansluta till testdatabasen med SSMS.
2. I den **Object Explorer**, högerklicka på tabellen och klicka sedan på **minne optimering Advisor**.
   
   * Den **tabell minne optimering Advisor** guiden visas.
3. I guiden klickar du på **verifiera migreringen** (eller **nästa** knappen) att se om tabellen innehåller alla funktioner som inte stöds i minnesoptimerade tabeller som inte stöds. Mer information finns i:
   
   * Den *minne optimering checklista* i [minne optimering Advisor](https://msdn.microsoft.com/library/dn284308.aspx).
   * [Transact-SQL-konstruktioner som inte stöds av In-Memory OLTP](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrera till In-Memory OLTP](https://msdn.microsoft.com/library/dn247639.aspx).
4. Om tabellen har inga funktioner som inte stöds, kan advisor utföra den faktiska schema- och migrering av data för dig.

#### <a name="manual-t-sql"></a>Manuell T-SQL
Använder det här alternativet för migrering:

1. Ansluta till testdatabasen med hjälp av SSMS (eller ett liknande verktyg).
2. Hämta det fullständiga T-SQL-skriptet för din tabell och dess index.
   
   * I SSMS högerklickar du på noden tabell.
   * Klicka på **skript tabell som** > **skapar till** > **nytt frågefönster**.
3. Lägg till WITH i skriptfönstret (MEMORY_OPTIMIZED = ON) i CREATE TABLE-instruktion.
4. Om det finns ett GRUPPERAT index kan du ändra den till NONCLUSTERED.
5. Byt namn på den befintliga tabellen med hjälp av SP_RENAME.
6. Skapa den nya minnesoptimerade kopian av tabellen genom att köra dina redigerade CREATE TABLE-skript.
7. Kopiera data till den minnesoptimerade tabellen med hjälp av INSERT... VÄLJ * TILL:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Steg 5 (valfritt): migrera lagrade procedurer
Funktionen minnesinterna kan också ändra en lagrad procedur för bättre prestanda.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Att tänka på med internt kompilerade lagrade procedurer
En internt kompilerad lagrad procedur måste ha följande alternativ på dess T-SQL med-sats:

* NATIVE_COMPILATION
* SCHEMABINDING: vilket innebär att tabeller som den lagrade proceduren inte kan ha sina kolumndefinitionerna ändras på något sätt som påverkar den lagrade proceduren, om inte du släpper den lagrade proceduren.

En inbyggd modul måste använda en stor [ATOMIC-block](https://msdn.microsoft.com/library/dn452281.aspx) för transaktionshantering. Det finns ingen roll för en explicit BEGIN TRANSACTION eller ROLLBACK TRANSACTION. Om din kod identifierar en överträdelse av en affärsregel, det kan avsluta atomic-block med en [THROW](https://msdn.microsoft.com/library/ee677615.aspx) instruktionen.

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

* För TRANSACTION_ISOLATION_LEVEL är ÖGONBLICKSBILDEN det vanligaste värdet för den internt kompilerade lagrade proceduren. Dock stöds också en delmängd av de andra värdena:
  
  * UPPREPBAR LÄSNING
  * SERIALISERBARA
* Värdet för SPRÅK måste finnas i vyn sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Så här migrerar du en lagrad procedur
Migreringssteg är:

1. Hämta skriptet CREATE PROCEDURE till den vanliga tolkad lagrade proceduren.
2. Skriv om huvudet för att matcha den tidigare mallen.
3. Fastställa om den lagrade proceduren T-SQL-kod använder alla funktioner som inte stöds för internt kompilerade lagrade procedurer. Implementera lösningar om det behövs.
   
   * Mer information finns i [migreringsproblem för internt kompilerade lagrade procedurer](https://msdn.microsoft.com/library/dn296678.aspx).
4. Byt namn på den gamla lagrade proceduren med hjälp av SP_RENAME. Eller helt enkelt.
5. Köra redigerade skapa proceduren T-SQL-skriptet.

## <a name="step-6-run-your-workload-in-test"></a>Steg 6: Kör arbetsbelastningen i testet
Köra en arbetsbelastning i din testdatabas som liknar den arbetsbelastning som körs i produktionsdatabasen. Detta bör visa prestandaökning genom din användning av funktionen i minnet för tabeller och lagrade procedurer.

Stora attribut för arbetsbelastningen är:

* Antalet samtidiga anslutningar.
* Läs/Skriv-förhållande.

Överväg att använda verktyget till hands ostress.exe, som illustreras i för att anpassa och köra testet arbetsbelastningen, [här](sql-database-in-memory.md).

Kör testet för att minimera Nätverksfördröjningen, i samma Azure geografiska regionen där databasen finns.

## <a name="step-7-post-implementation-monitoring"></a>Steg 7: Efter implementering övervakning
Överväg att övervaka prestanda effekterna av din InMemory-implementeringar i produktion:

* [Övervaka Minnesintern lagring](sql-database-in-memory-oltp-monitoring.md).
* [Övervaka Azure SQL Database med dynamiska hanteringsvyer](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Relaterade länkar
* [Minnesintern OLTP (minnesoptimering)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Introduktion till internt kompilerade lagrade procedurer](https://msdn.microsoft.com/library/dn133184.aspx)
* [Minne optimering Advisor](https://msdn.microsoft.com/library/dn284308.aspx)

