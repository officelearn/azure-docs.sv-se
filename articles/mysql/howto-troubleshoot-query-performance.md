---
title: Felsökning av frågeprestanda i Azure-databas för MySQL
description: Den här artikeln beskriver hur du använder FÖRKLARA för att felsöka prestanda för frågor i Azure-databas för MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 72b047c37ac88e4b33c8723f8df14c6794e84399
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266184"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Hur du använder FÖRKLARA att profilen frågeprestanda i Azure-databas för MySQL
**FÖRKLARAR** är ett bra verktyg för att optimera frågor. BESKRIVS uttryck kan användas för att få information om hur SQL-uttryck körs. Följande utdata visar ett exempel på körning av en förklaring-instruktion.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Som kan ses från det här exemplet är värdet för *nyckeln* är NULL. Den här utdatan innebär MySQL går inte att hitta några index som är optimerade för frågan och utför en fullständig tabellgenomsökning. Optimera vi den här frågan genom att lägga till ett index på den **ID** kolumn.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

Nya FÖRKLARA visar att MySQL nu använder ett index för att begränsa antalet rader till 1, vilket i sin tur kraftigt förkortat söktiden.
 
## <a name="covering-index"></a>Täcker index
Ett index omfattar består av alla kolumner i en fråga i indexet för att minska värdet hämtas från datatabeller. Här är en bild i följande **GROUP BY** instruktionen.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Som kan ses från utdata, använder inte MySQL index eftersom inga rätt index är tillgängliga. Den visar även *med tillfällig. Använda filen sortera*, vilket betyder att MySQL skapas en tillfällig tabell för att uppfylla de **GROUP BY** satsen.
 
Skapa ett index för kolumnen **c2** enbart görs ingen skillnad och MySQL fortfarande behövs för att skapa en tillfällig tabell:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

I det här fallet en **omfattas index** på både **c1** och **c2** kan skapas, innebär att lägga till värdet för **c2**”direkt i indexet för undvika ytterligare data sökning.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Som du ser ovan FÖRKLARA använder omfattas indexet nu MySQL och undvika att skapa en tillfällig tabell. 

## <a name="combined-index"></a>Kombinerade indexet
Kombinerade indexet består värden från flera kolumner och kan anses vara en matris med rader som ska sorteras efter sammanbinda värdena i de fulltextindexerade kolumnerna. Den här metoden kan vara användbar vid en **GROUP BY** instruktionen.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MySQL-utför en *filen sortera* åtgärd som är ganska långsam, särskilt när den har att sortera många rader. Om du vill optimera den här frågan kan kombinerade indexet skapas i både kolumner som sorteras.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

FÖRKLARING visas nu att MySQL kan använda kombinerade indexet för att undvika ytterligare sortering eftersom indexet sorteras redan.
 
## <a name="conclusion"></a>Sammanfattning
 
Med hjälp av förklaring och annan typ av index kan öka prestanda avsevärt. Bara för att du har ett index på tabellen inte nödvändigtvis MySQL skulle kunna använda det för dina frågor. Alltid verifiera din antaganden med förklaring och optimera dina frågor med index.


## <a name="next-steps"></a>Nästa steg
- Om du vill hitta peer svar på dina mest berörda frågor eller efter en ny fråga/svar, gå [MSDN-forum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
