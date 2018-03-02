---
title: "Så här används sys_schema för inställning av prestanda och underhåll i Azure-databas för MySQL-databasen"
description: "Den här artikeln beskriver hur du använder sys_schema att hitta prestandaproblem och underhålla databasen i Azure-databas för MySQL."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 2e5b6b859df06d686a97fc1b134da8d66df6783e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-use-sysschema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Hur du använder sys_schema för prestanda justera och databasunderhåll i Azure-databas för MySQL

MySQL-performance_schema, först i MySQL 5.5 innehåller instrumentation för många viktiga serverresurser som minnesallokering, lagrade program, metadata låsning, etc. Men performance_schema innehåller mer än 80 tabeller och hämta nödvändig information ofta kräver att ansluta till tabeller i performance_schema samt tabeller från information_schema. Bygger på både performance_schema och information_schema, sys_schema innehåller en kraftfull uppsättning [användarvänliga vyer](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) i en skrivskyddad databas och helt aktiverade i Azure-databas för MySQL version 5.7.

![vyer för sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Det finns 52 vyer i sys_schema och varje vy har något av följande prefix:

- Host_summary eller -i/o: i/o-relaterade svarstider.
- InnoDB: InnoDB Bufferstatus och lås.
- Minne: Minnesanvändning för värden och användare.
- Schema: Schema-relaterad information, till exempel AutoIncrement, index och så vidare.
- Instruktionen: Information om SQL-instruktioner; Det kan vara instruktion som resulterade i fullständig tabellsökningar eller länge Frågetid.
- Användare: Resurser används och grupperade efter användare. Exempel är filen I/o, anslutningar och minne.
- Vänta: Vänta händelser grupperade efter värden eller användare.

Nu ska vi titta på några vanliga användningsmönster för sys_schema. Vi ska börja med att gruppera användningsmönster i två kategorier: **prestandajustering** och **databasen Underhåll**.

## <a name="performance-tuning"></a>Prestandajustering

### <a name="sysusersummarybyfileio"></a>*sys.user_summary_by_file_io*

I/o är dyraste åtgärden i databasen. Vi kan ta reda på genomsnittlig svarstid för i/o genom att fråga den *sys.user_summary_by_file_io* vyn. Med standardvärdet 125 GB etablerade lagringsutrymme, min i/o-svarstid är ungefär 15 sekunder.

![i/o-svarstid: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Eftersom Azure-databas för MySQL skalar i/o med avseende på lagring, efter att ha ökat mitt etablerade lagringsutrymme till 1 TB, minskar mitt i/o-svarstid till 571 ms, som representerar ökar prestanda 26 X!

![i/o-svarstid: 1TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschematableswithfulltablescans"></a>*sys.schema_tables_with_full_table_scans*

Trots noggrann planering kan många frågor fortfarande ge fullständig tabellsökningar. Mer information om vilka typer av index och hur du optimerar dem kan du referera till den här artikeln: [felsökning av frågeprestanda](./howto-troubleshoot-query-performance.md). Fullständig tabellsökningar är resursintensiv och påverka databasens prestanda. Det snabbaste sättet att hitta tabeller med fullständig tabellsökningar är att fråga den *sys.schema_tables_with_full_table_scans* vyn.

![fullständig tabellsökningar](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysusersummarybystatementtype"></a>*sys.user_summary_by_statement_type*

Om du vill felsöka problem med databasprestanda, kan det vara klokt att identifiera vilka händelser som händer i din databas, och använder den *sys.user_summary_by_statement_type* vyn kan bara använda en urvalsfråga.

![Sammanfattning av instruktionen](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

I det här exemplet att Azure-databas för MySQL 53 minuter lokaliseraren frågeloggen slog 44579 gånger. Det är en lång tid och många IOs. Du kan minska den här aktiviteten genom att antingen inaktivera långsam fråga loggen eller minska frekvensen av långsam fråga inloggningen Azure-portalen.

## <a name="database-maintenance"></a>Databasunderhåll

### <a name="sysinnodbbufferstatsbytable"></a>*sys.innodb_buffer_stats_by_table*

Buffertpooltillägget InnoDB finns i minnet och är en mekanism för huvud-cache mellan DBMS och lagring. Storleken på buffertpooltillägget InnoDB är knutna till nivån av prestanda och kan inte ändras om inte en annan produkt SKU är valt. Precis som med minne i operativsystemet växlas gamla sidor över för att göra plats för fresher data. Om du vill ta reda på vilka tabeller använda de flesta InnoDB buffertens pool minne kan du fråga den *sys.innodb_buffer_stats_by_table* vyn.

![InnoDB Bufferstatus](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

Det är tydligt att än systemtabeller och vyer, varje tabell i mysqldatabase033-databasen, som är värd för en min WordPress-webbplats och upptar 16 KB eller 1 sida av data i minnet i bilden ovan.

### <a name="sysschemaunusedindexes--sysschemaredundantindexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Index är bra verktyg för att förbättra läsprestanda, men de innebära ytterligare kostnader för INSERT och lagring. *Sys.schema_unused_indexes* och *sys.schema_redundant_indexes* ger insikter om oanvända eller dubbla index.

![oanvända index](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundant index](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Sammanfattning

Sammanfattningsvis är sys_schema ett bra verktyg för både prestanda justera och databasunderhåll. Se till att dra nytta av den här funktionen i din Azure-databas för MySQL. 

## <a name="next-steps"></a>Nästa steg
- Om du vill hitta peer svar på dina mest berörda frågor eller efter en ny fråga/svar, gå [MSDN-forum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
