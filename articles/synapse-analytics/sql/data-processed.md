---
title: Bearbetade data med Server lös SQL-pool
description: Det här dokumentet beskriver hur data som bearbetas beräknas när du frågar efter data i Azure Storage med hjälp av SQL-poolen utan server.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 06eb02aa3dd4d5fc8bd3605dac480d5afa52d5fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424432"
---
# <a name="data-processed-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Bearbetade data med Server lös SQL-pool i Azure Synapse Analytics

Bearbetade data är mängden data som lagras temporärt i systemet när en fråga körs och består av:

- Mängden data som läses från Storage – bland annat:
  - Data mängden läst vid inläsning av data
  - Data mängden läst vid läsning av metadata (för fil format som innehåller metadata, t. ex. Parquet)
- Mängden data i mellanliggande resultat – data som överförs mellan noder under frågekörningen, inklusive data överföring till din slut punkt, i okomprimerat format. 
- Data mängd som skrivs till lagring – om du använder CETAS för att exportera resultat uppsättningen till Storage debiteras du för byte som skrivs ut och mängden data som bearbetas för den valda delen av CETAS.

Läsning av filer från lagring är mycket optimerat och använder:

- För hämtning – som kan lägga till en liten omkostnader i mängden data som läses. Om en fråga läser en hel fil sker ingen överbelastning. Om en fil läses delvis, som i de x främsta frågorna, kommer en bit mer data att läsas med för hämtning.
- Optimerad CSV-parser – om du använder PARSER_VERSION = ' 2.0 ' för att läsa CSV-filer kommer det att leda till ett något ökat antal data som läses från lagringen.  Optimerad CSV-parser läser filer parallellt i segment med samma storlek. Det finns ingen garanti för att segment ska innehålla hela rader. Om du vill se till att alla rader tolkas, kommer små fragment av intilliggande segment att läsas och lägga till en liten del av omkostnaderna.

## <a name="statistics"></a>Statistik

SQL-poolens fråga optimering förlitar sig på statistik för att skapa optimala fråge körnings planer. Du kan skapa statistik manuellt eller så skapas de automatiskt av en server lös SQL-pool. I båda fallen skapas statistik genom att köra en separat fråga som returnerar en särskild kolumn vid angiven samplings frekvens. Den här frågan har en associerad mängd bearbetade data.

Om du kör samma eller någon annan fråga som skulle dra nytta av statistik som skapats, kommer statistik att återanvändas om det är möjligt och inga ytterligare data bearbetas för att skapa statistik.

När du skapar statistik för en Parquet-kolumn kan du bara läsa den relevanta kolumnen från filerna. När du skapar statistik för en CSV-kolumn blir hela filer lästa och tolkas.

## <a name="rounding"></a>Avrundning

Mängden data som bearbetas avrundas uppåt till närmaste MB per fråga, med minst 10 MB data som bearbetas per fråga.

## <a name="what-is-not-included-in-data-processed"></a>Det som inte ingår i data som bearbetas

- Metadata på server nivå (t. ex. inloggningar, roller, autentiseringsuppgifter på server nivå)
- Databaser som du skapar i slut punkten eftersom dessa databaser bara innehåller metadata (t. ex. användare, roller, scheman, vyer, infogade TVFs, lagrade procedurer, databasens begränsade autentiseringsuppgifter, externa data källor, externa fil format, externa tabeller)
  - Om du använder schema härledning kommer fragment av filer att läsas för att härleda kolumn namn och data typer
- DDL-instruktioner förutom att skapa statistik som bearbetar data från lagrings utrymme baserat på den angivna exempel procenten
- Frågor med enbart metadata

## <a name="reduce-amount-of-data-processed"></a>Minska mängden bearbetade data

Du kan optimera mängden data som bearbetas per fråga och få bättre prestanda genom att partitionera och konvertera data till ett komprimerat kolumn format som Parquet.

## <a name="examples"></a>Exempel

Anta att det finns två tabeller som har samma data i fem lika stora kolumner:

- population_csv tabell som backas upp av 5 TB CSV-filer
- population_parquet tabell som backas upp av 1 TB Parquet-filer – den här tabellen är mindre än den tidigare en som Parquet innehåller komprimerade data
- very_small_csv tabell som backas upp av 100 KB CSV-filer

**Fråga #1** : Välj sum (population) från population_csv

Den här frågan läser och tolkar hela filer för att hämta värden för populations kolumnen. Noderna bearbetar fragment i den här tabellen, populations summan för varje fragment överförs mellan noder och den slutliga summan överförs till din slut punkt. Den här frågan bearbetar 5 TB data plus små kostnader för att överföra summor av fragment.

**Fråga #2** : Välj sum (population) från population_parquet

Att fråga efter komprimerade och kolumnbaserade format som Parquet resulterar i att du läser mindre data än i föregående fråga, eftersom en server fri SQL-pool läser en komprimerad kolumn i stället för hela filen. I det här fallet kommer 0,2 TB att läsas (fem lika stora kolumner, 0,2 TB vardera). Noderna bearbetar fragment i den här tabellen, populations summan för varje fragment överförs mellan noder och den slutliga summan överförs till din slut punkt. Den här frågan bearbetar 0,2 TB plus en liten omkostnader för att överföra summor av fragment.

**Fråga #3** : Välj * från population_parquet

Den här frågan läser alla kolumner och överför alla data i okomprimerat format. Om komprimerings formatet är 5:1 kommer det att bearbeta 6 TB eftersom det läser 1 TB + överföring 5 TB av okomprimerade data.

**Fråga #4** : Välj Count (*) från very_small_csv

Den här frågan läser hela filer. Den totala storleken på filer som lagras i den här tabellen är 100 KB. Noderna bearbetar fragment i den här tabellen, summan för varje fragment överförs mellan noder och den sista summan överförs till din slut punkt. Den här frågan kommer att bearbeta en aning över 100 KB data. Mängden data som bearbetas för den här frågan avrundas uppåt till 10 MB som anges i [avrundning](#rounding).

## <a name="next-steps"></a>Nästa steg

Information om hur du optimerar dina frågor för prestanda finns i [metod tips för Server lös SQL-pool](best-practices-sql-on-demand.md).
