---
title: Felsöka frågeprestanda – Azure Database för MySQL
description: Lär dig hur du använder EXPLAIN för att felsöka frågeprestanda i Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 6b27e47339b80cc46290065c4d17150a301f2534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067828"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Så här använder du EXPLAIN för att profilera frågeprestanda i Azure Database for MySQL
**EXPLAIN** är ett praktiskt verktyg för att optimera frågor. EXPLAIN-satsen kan användas för att få information om hur SQL-uttryck körs. Följande utdata visar ett exempel på körningen av en EXPLAIN-sats.

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

Som framgår av det här exemplet är värdet på *nyckeln* NULL. Den här utdata innebär att MySQL inte kan hitta några index som är optimerade för frågan och att den utför en fullständig tabellgenomsökning. Låt oss optimera den här frågan genom att lägga till ett index i **ID-kolumnen.**

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

Den nya EXPLAIN visar att MySQL nu använder ett index för att begränsa antalet rader till 1, vilket i sin tur dramatiskt förkortade söktiden.
 
## <a name="covering-index"></a>Täckindex
Ett täckindex består av alla kolumner i en fråga i indexet för att minska värdehämtningen från datatabeller. Här är en illustration i följande **GROUP BY** uttalande.
 
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

Som framgår av utdata använder MySQL inga index eftersom inga korrekta index är tillgängliga. Det visar också *Använda tillfälliga; Använda filsortering*, vilket innebär att MySQL skapar en tillfällig tabell för att uppfylla **GROUP BY-satsen.**
 
Att skapa ett index enbart för kolumn **c2** gör ingen skillnad, och MySQL behöver fortfarande skapa en tillfällig tabell:

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

I det här fallet kan ett **täckt index** på både **c1** och **c2** skapas, varvid värdet av **c2**" direkt i indexet för att eliminera ytterligare datasökning.

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

Som ovanstående EXPLAIN visar använder MySQL nu det täckta indexet och undviker att skapa en tillfällig tabell. 

## <a name="combined-index"></a>Kombinerat index
Ett kombinerat index består av värden från flera kolumner och kan betraktas som en matris med rader som sorteras efter sammanfogande värden för de indexerade kolumnerna.Den här metoden kan vara användbar i en **GROUP BY-sats.**

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

MySQL utför en *filsorteringsåtgärd* som är ganska långsam, särskilt när den måste sortera många rader. För att optimera den här frågan kan ett kombinerat index skapas på båda kolumnerna som sorteras.

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

FÖRKLARA visar nu att MySQL kan använda kombinerat index för att undvika ytterligare sortering eftersom indexet redan är sorterat.
 
## <a name="conclusion"></a>Slutsats
 
Med hjälp av EXPLAIN och olika typer av index kan öka prestanda avsevärt. Att ha ett index på bordet betyder inte nödvändigtvis MySQL skulle kunna använda den för dina frågor. Verifiera alltid dina antaganden med hjälp av FÖRKLARA och optimera dina frågor med hjälp av index.


## <a name="next-steps"></a>Nästa steg
- Om du vill hitta peer-svar på dina mest berörda frågor eller lägga upp en ny fråga /svar besöker du [MSDN-forumet](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
