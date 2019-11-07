---
title: InMemory OLTP förbättrar SQL TXN-prestanda
description: Använd minnes intern OLTP för att förbättra transaktions prestanda i en befintlig SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: 82b24b51a103d31bf20bbb7a9fc304095be523d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689838"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Använd minnes intern OLTP för att förbättra programmets prestanda i SQL Database

[Minnes intern OLTP](sql-database-in-memory.md) kan användas för att förbättra prestanda för transaktions bearbetning, data inmatning och tillfälliga data scenarier, i [Premium-och affärskritisk nivå](sql-database-service-tiers-vcore.md) databaser utan att öka pris nivån. 

> [!NOTE] 
> Lär dig hur [kvorum dubblerar nyckel databasens arbets belastning och sänker DTU med 70% med SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Följ dessa steg om du vill använda minnes intern OLTP i den befintliga databasen.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Steg 1: kontrol lera att du använder en Premium-och Affärskritisk nivå databas

Minnes intern OLTP stöds endast i Premium-och Affärskritisk-Tier-databaser. InMemory stöds om det returnerade resultatet är 1 (inte 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* står för *bearbetning av extrema transaktioner*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Steg 2: identifiera objekt som ska migreras till minnes intern OLTP
SSMS innehåller en **översikts rapport över transaktions prestanda analys** som du kan köra mot en databas med en aktiv arbets belastning. Rapporten identifierar tabeller och lagrade procedurer som är kandidater för migrering till InMemory OLTP.

För att generera rapporten i SSMS:

* Högerklicka på noden databas i **Object Explorer**.
* Klicka på **rapporter** > **standard rapporter** > **Översikt över transaktions prestanda analys**.

Mer information finns i [avgöra om en tabell eller lagrad procedur ska hamna på minnes intern OLTP](https://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Steg 3: skapa en jämförbar test databas
Anta att rapporten visar att databasen har en tabell som kan dra nytta av att konverteras till en minnesoptimerade tabell. Vi rekommenderar att du först testar att bekräfta indikeringen genom att testa.

Du behöver en test kopia av produktions data basen. Test databasen ska ha samma service nivå nivå som produktions databasen.

Du kan under lätta testningen genom att justera test databasen på följande sätt:

1. Anslut till test databasen med hjälp av SSMS.
2. För att undvika att du behöver alternativet WITH (ÖGONBLICKs bild) i frågor, anger du databas alternativet som det visas i följande T-SQL-uttryck:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Steg 4: Migrera tabeller
Du måste skapa och fylla i en minnesoptimerade kopia av den tabell som du vill testa. Du kan skapa den med hjälp av antingen:

* Guiden för praktisk minnes optimering i SSMS.
* Manuell T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Guiden minnes optimering i SSMS
Använd detta alternativ för migrering:

1. Anslut till test databasen med SSMS.
2. Högerklicka på tabellen i **Object Explorer**och klicka sedan på **guiden för minnes optimering**.
   
   * Guiden för **tabell minnes optimerings** guide visas.
3. I guiden klickar du på **migrerings verifiering** (eller **Nästa** ) för att se om tabellen har funktioner som inte stöds i minnesoptimerade tabeller. Mer information finns i:
   
   * *Check lista för minnes optimering* i [minnes optimerings Advisor](https://msdn.microsoft.com/library/dn284308.aspx).
   * [Transact-SQL-konstruktioner stöds inte av minnes intern OLTP](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migrera till InMemory OLTP](https://msdn.microsoft.com/library/dn247639.aspx).
4. Om tabellen inte innehåller funktioner som inte stöds kan Advisor utföra det faktiska schemat och datamigreringen.

#### <a name="manual-t-sql"></a>Manuell T-SQL
Använd detta alternativ för migrering:

1. Anslut till test databasen med hjälp av SSMS (eller ett liknande verktyg).
2. Hämta det fullständiga T-SQL-skriptet för din tabell och dess index.
   
   * I SSMS högerklickar du på noden tabell.
   * Klicka på **skript tabell som** > **skapa för att** > **fönstret ny fråga**.
3. I fönstret skript lägger du till WITH (MEMORY_OPTIMIZED = ON) i CREATE TABLE-instruktionen.
4. Om det finns ett GRUPPERat index ändrar du det till inklustrat.
5. Byt namn på den befintliga tabellen med hjälp av SP_RENAME.
6. Skapa den nya minnesoptimerade kopian av tabellen genom att köra det redigerade CREATE TABLE skriptet.
7. Kopiera data till den minnesoptimerade tabellen med hjälp av Infoga... VÄLJ * I:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Steg 5 (valfritt): Migrera lagrade procedurer
InMemory-funktionen kan också ändra en lagrad procedur för bättre prestanda.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Att tänka på med internt kompilerade lagrade procedurer
En internt kompilerad lagrad procedur måste ha följande alternativ i sin T-SQL WITH-sats:

* NATIVE_COMPILATION
* SCHEMABINDING: tabeller som den lagrade proceduren inte kan ha sina kolumn definitioner ändrade på något sätt som skulle påverka den lagrade proceduren, om du inte släpper den lagrade proceduren.

En ursprunglig modul måste använda ett Big [Atomic-block](https://msdn.microsoft.com/library/dn452281.aspx) för transaktions hantering. Det finns ingen roll för en explicit BEGIN TRANSACTION eller för återställnings transaktion. Om din kod identifierar en överträdelse av en affärs regel kan den avsluta Atomic-blocket med en [Throw](https://msdn.microsoft.com/library/ee677615.aspx) -instruktion.

### <a name="typical-create-procedure-for-natively-compiled"></a>Typisk skapa procedur för internt kompilerade
T-SQL för att skapa en internt kompilerad lagrad procedur liknar vanligt vis följande mall:

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

* För TRANSACTION_ISOLATION_LEVEL är ÖGONBLICKs bilden det vanligaste värdet för den internt kompilerade lagrade proceduren. En delmängd av de andra värdena stöds dock också:
  
  * UPPREPNINGS BAR LÄSNING
  * SERIALISERA
* Språkvärdet måste finnas i vyn sys. language.

### <a name="how-to-migrate-a-stored-procedure"></a>Så här migrerar du en lagrad procedur
Stegen för migreringen är:

1. Hämta skriptet för att skapa procedur till den vanliga tolkade lagrade proceduren.
2. Skriv om rubriken så att den matchar föregående mall.
3. Kontrol lera om den lagrade procedurens T-SQL-kod använder funktioner som inte stöds för internt kompilerade lagrade procedurer. Implementera lösningar vid behov.
   
   * Mer information finns i [migrerings problem för internt kompilerade lagrade procedurer](https://msdn.microsoft.com/library/dn296678.aspx).
4. Byt namn på den gamla lagrade proceduren med hjälp av SP_RENAME. Eller helt enkelt släppa det.
5. Kör den redigerade CREATE PROCEDURe T-SQL-skriptet.

## <a name="step-6-run-your-workload-in-test"></a>Steg 6: kör din arbets belastning i testet
Kör en arbets belastning i test databasen som liknar den arbets belastning som körs i produktions databasen. Detta bör se till att prestanda uppnås genom att använda InMemory-funktionen för tabeller och lagrade procedurer.

Större attribut för arbets belastningen är:

* Antal samtidiga anslutningar.
* Läs-/skriv förhållande.

För att skräddarsy och köra test arbets belastningen bör du överväga att använda det praktiska verktyget ostress. exe, som illustreras [här](sql-database-in-memory.md).

Du kan minimera nätverks fördröjningen genom att köra testet i samma Azure-geografiska region där databasen finns.

## <a name="step-7-post-implementation-monitoring"></a>Steg 7: övervakning efter implementering
Överväg att övervaka prestanda effekterna för dina minnes implementeringar i produktionen:

* [Övervaka minnes intern lagring](sql-database-in-memory-oltp-monitoring.md).
* [Övervaka Azure SQL Database med dynamiska hanteringsvyer](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Relaterade länkar
* [Minnes intern OLTP (minnes intern optimering)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Introduktion till internt kompilerade lagrade procedurer](https://msdn.microsoft.com/library/dn133184.aspx)
* [Optimering av minnes optimering](https://msdn.microsoft.com/library/dn284308.aspx)

