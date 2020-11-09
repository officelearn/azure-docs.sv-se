---
title: Bearbetade data med hjälp av Server lös SQL-pool
description: Det här dokumentet beskriver hur det data som bearbetas beräknas när du frågar efter data i data Lake.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a108e5fdd30c21cdb7771e3f683dad22773653a4
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381209"
---
# <a name="data-processed-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Bearbetade data med hjälp av Server lös SQL-pool i Azure Synapse Analytics

*Bearbetade data* är mängden data som systemet lagrar tillfälligt medan en fråga körs. Bearbetade data består av följande kvantiteter:

- Mängden data som läses från lagringen. Detta belopp inkluderar:
  - Data lästes vid läsning av data.
  - Data lästes vid läsning av metadata (för fil format som innehåller metadata, t. ex. Parquet).
- Mängden data i mellanliggande resultat. Dessa data överförs mellan noder medan frågan körs. Den innehåller data överföringen till din slut punkt i ett okomprimerat format. 
- Mängden data som skrivs till lagringen. Om du använder CETAS för att exportera resultat uppsättningen till Storage, läggs mängden data som skrivs ut till i mängden data som bearbetas för den valda delen av CETAS.

Läsning av filer från lagring är starkt optimerat. Processen använder:

- För hämtning, som kan lägga till viss overhead till mängden data som läses. Om en fråga läser en hel fil finns det ingen omkostnader. Om en fil läses delvis, som i de x främsta frågorna, läses en bit mer data med hjälp av för hämtning.
- En optimerad CSV-parser (kommaavgränsade värden). Om du använder PARSER_VERSION = 2.0 för att läsa CSV-filer ökar mängden data som läses från lagrings utrymme något. En optimerad CSV-parser läser filer parallellt, i segment med samma storlek. Segment innehåller inte nödvändigt vis hela rader. För att se till att alla rader tolkas, läser den optimerade CSV-parsern också små fragment av intilliggande segment. Den här processen lägger till en liten del av omkostnaderna.

## <a name="statistics"></a>Statistik

SQL-poolens fråga optimering förlitar sig på statistik för att skapa optimala fråge körnings planer. Du kan skapa statistik manuellt. Annars skapar Server lös SQL-poolen automatiskt. I båda fallen skapas statistik genom att köra en separat fråga som returnerar en särskild kolumn i en angiven samplings frekvens. Den här frågan har en associerad mängd bearbetade data.

Om du kör samma eller någon annan fråga som skulle dra nytta av statistik som skapats, kommer statistik att återanvändas om möjligt. Det finns inga ytterligare data som bearbetas för att skapa statistik.

När statistik skapas för en Parquet-kolumn läses endast den relevanta kolumnen in från filer. När statistik skapas för en CSV-kolumn läses och tolkas hela filer.

## <a name="rounding"></a>Avrundning

Mängden data som bearbetas avrundas uppåt till närmaste MB per fråga. Varje fråga har minst 10 MB bearbetade data.

## <a name="what-data-processed-doesnt-include"></a>Vilka data som bearbetas omfattar inte

- Metadata på server nivå (t. ex. inloggningar, roller och autentiseringsuppgifter på server nivå).
- Databaser som du skapar i slut punkten. Dessa databaser innehåller endast metadata (t. ex. användare, roller, scheman, vyer, infogade tabell värdes funktioner [TVFs], lagrade procedurer, autentiseringsuppgifter för databas, externa data källor, externa fil format och externa tabeller).
  - Om du använder schema härledning, läses filfragmenten för att härleda kolumn namn och data typer och mängden data som läses läggs till i mängden data som bearbetas.
- DDL-uttryck (Data Definition Language), förutom CREATE STATISTICS-instruktionen, eftersom den bearbetar data från lagring baserat på den angivna exempel procenten.
- Frågor med enbart metadata.

## <a name="reducing-the-amount-of-data-processed"></a>Minska mängden bearbetade data

Du kan optimera mängden data som bearbetas per fråga och förbättra prestandan genom att partitionera och konvertera data till ett komprimerat kolumnbaserade format som Parquet.

## <a name="examples"></a>Exempel

Föreställ dig tre tabeller.

- Den population_csv tabellen backas upp med 5 TB CSV-filer. Filerna är ordnade i fem lika stora kolumner.
- Population_parquet tabellen har samma data som population_csvs tabellen. Den backas upp med 1 TB av Parquet-filer. Den här tabellen är mindre än föregående, eftersom data komprimeras i Parquet-format.
- Very_small_csv tabellen backas upp av 100 KB CSV-filer.

**Fråga 1** : Välj sum (population) från population_csv

Den här frågan läser och tolkar hela filer för att hämta värden för populations kolumnen. Noderna bearbetar fragmenten i den här tabellen och populations summan för varje fragment överförs mellan noder. Den sista summan överförs till din slut punkt. 

Den här frågan bearbetar 5 TB data plus ett litet antal omkostnader för att överföra summor av fragment.

**Fråga 2** : Välj sum (population) från population_parquet

När du frågar efter komprimerade och kolumnbaserade format som Parquet läses mindre data än i fråga 1. Du ser det här resultatet eftersom SQL-poolen utan Server läser en komprimerad kolumn i stället för hela filen. I det här fallet är 0,2 TB läst. (Fem lika stora kolumner är 0,2 TB vardera.) Noderna bearbetar fragmenten i den här tabellen och populations summan för varje fragment överförs mellan noder. Den sista summan överförs till din slut punkt. 

Den här frågan bearbetar 0,2 TB plus en liten del av omkostnader för att överföra summor av fragment.

**Fråga 3** : Välj * från population_parquet

Den här frågan läser alla kolumner och överför alla data i ett okomprimerat format. Om komprimerings formatet är 5:1, bearbetar frågan 6 TB eftersom den läser 1 TB och överför 5 TB okomprimerade data.

**Fråga 4** : Välj Count (*) från very_small_csv

Den här frågan läser hela filer. Den totala storleken på filer som lagras i den här tabellen är 100 KB. Noderna bearbetar fragment i den här tabellen och summan för varje fragment överförs mellan noder. Den sista summan överförs till din slut punkt. 

Den här frågan bearbetar något mer än 100 KB data. Mängden data som bearbetas för den här frågan avrundas uppåt till 10 MB, enligt vad som anges i avsnittet [Avrunda](#rounding) i den här artikeln.

## <a name="next-steps"></a>Nästa steg

Information om hur du optimerar dina frågor för prestanda finns i [metod tips för Server lös SQL-pool](best-practices-sql-on-demand.md).
