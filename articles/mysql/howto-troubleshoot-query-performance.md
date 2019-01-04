---
title: Så här felsöker du prestanda för frågor i Azure Database for MySQL
description: Den här artikeln beskriver hur du använder FÖRKLARA för att felsöka frågeprestanda i Azure Database för MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 819e2393619766d46385cdd6fe550fff1e1a7631
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537672"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Hur du använder FÖRKLARA att profil-frågeprestanda i Azure Database för MySQL
**FÖRKLARA** är ett praktiskt verktyg för att optimera frågor. FÖRKLARA instruktionen kan användas för att få information om hur SQL-uttryck körs. Följande utdata visar ett exempel på körning av en förklaring-instruktion.

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

Se från det här exemplet är värdet för *nyckel* är NULL. Dessa utdata innebär MySQL kan inte hitta några index som optimerats för frågan och genomför en fullständig tabellsökning. Nu ska vi optimera den här frågan genom att lägga till ett index på den **ID** kolumn.

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

Ny förklaring visar att MySQL nu använder ett index för att begränsa antalet rader till 1, vilket i sin tur dramatiskt förkortade söktiden.
 
## <a name="covering-index"></a>Som täcker index
Ett index omfattar består av alla kolumner i en fråga i indexet att minska värdet hämtning från datatabeller. Här är en bild i följande **GROUP BY** instruktionen.
 
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

Se från utdata, använder inte MySQL index eftersom inga rätt index är tillgängliga. Det visar även *med tillfällig. Med hjälp av filen sortera*, vilket innebär att MySQL skapar en temporär tabell att uppfylla den **GROUP BY** satsen.
 
Skapa ett index i kolumnen **c2** enbart gör ingen skillnad och MySQL fortfarande behövs för att skapa en tillfällig tabell:

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

I det här fallet en **omfattas index** på både **c1** och **c2** kan skapas, innebär att lägga till värdet för **c2**”direkt i indexet för eliminera ytterligare data sökning.

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

Så som visas i ovanstående FÖRKLARA, använder omfattas indexet MySQL nu och Undvik att skapa en tillfällig tabell. 

## <a name="combined-index"></a>Kombinerade index
En kombinerad index består värden från flera kolumner och kan ses som en matris med rader som sorteras genom att sammanfoga värden för de indexerade kolumnerna. Den här metoden kan vara användbart i en **GROUP BY** instruktionen.

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

MySQL-utför en *filen sortera* åtgärd som är ganska långsam, särskilt när det har att sortera många rader. För att optimera den här frågan kan en kombinerad index skapas i både kolumner som sorteras.

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

FÖRKLARA visas nu att MySQL ska kunna använda kombinerade index för att undvika ytterligare sortering eftersom indexet sorteras redan.
 
## <a name="conclusion"></a>Sammanfattning
 
Med hjälp av förklaring och annan typ av index kan öka prestanda avsevärt. Bara för att du har ett index i innebära tabellen inte MySQL skulle kunna använda det för dina frågor. Alltid verifiera dina antaganden med hjälp av förklaring och optimera dina frågor med hjälp av index.


## <a name="next-steps"></a>Nästa steg
- Om du vill hitta peer-svar på dina mest berörda frågor eller publicera en ny fråga/svar, besök [MSDN-forum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
