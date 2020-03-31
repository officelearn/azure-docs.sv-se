---
title: Utnyttja sys_schema - Azure-databas för MySQL
description: Lär dig hur du använder sys_schema för att hitta prestandaproblem och underhålla databasen i Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: a35a586a519ff78e8b32d986b92bd008b2c6b858
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067873"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Så här använder du sys_schema för prestandajustering och databasunderhåll i Azure Database for MySQL

MySQL performance_schema, först tillgänglig i MySQL 5.5, ger instrumentering för många viktiga serverresurser såsom minnesallokering, lagrade program, metadata låsning, etc. Performance_schema innehåller dock mer än 80 tabeller, och för att få nödvändig information krävs ofta att du går med tabeller inom performance_schema, samt tabeller från information_schema. Sys_schema bygger på både performance_schema och information_schema och ger en kraftfull samling [användarvänliga vyer](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) i en skrivskyddad databas och är fullt aktiverad i Azure Database för MySQL version 5.7.

![synpunkter på sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Det finns 52 vyer i sys_schema och varje vy har något av följande prefix:

- Host_summary eller IO: I/O-relaterade svarstider.
- InnoDB: InnoDB buffertstatus och lås.
- Minne: Minnesanvändning av värden och användarna.
- Schema: Schemarelaterad information, till exempel automatisk ökning, index osv.
- Statement: Information om SQL-uttryck; Det kan vara en sats som resulterade i fullständig tabellgenomsökning eller lång frågetid.
- Användare: Resurser som förbrukas och grupperas av användare. Exempel är fil-I/Os, anslutningar och minne.
- Vänta: Vänta händelser grupperade efter värd eller användare.

Nu ska vi titta på några vanliga användningsmönster i sys_schema. Till att börja med grupperar vi användningsmönstren i två kategorier: **Prestandajustering** och **Databasunderhåll**.

## <a name="performance-tuning"></a>Prestandajustering

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

IO är den dyraste åtgärden i databasen. Vi kan ta reda på den genomsnittliga IO-svarstiden genom att fråga *sys.user_summary_by_file_io-vyn.* Med standard 125 GB etablerad lagring är min IO-svarstid cirka 15 sekunder.

![io latens: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Eftersom Azure Database for MySQL skalar IO med avseende på lagring, efter att ha ökat min etablerade lagring till 1 TB, minskar min IO-svarstid till 571 ms.

![io latens: 1TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Trots noggrann planering kan många frågor fortfarande resultera i fullständiga tabellgenomsökningar. Mer information om vilka typer av index som finns och hur du optimerar dem finns i den här artikeln: [Felsöka frågeprestanda](./howto-troubleshoot-query-performance.md). Fullständiga tabellgenomsökningar är resurskrävande och försämrar databasens prestanda. Det snabbaste sättet att hitta tabeller med fullständig tabellgenomsökning är att fråga *sys.schema_tables_with_full_table_scans-vyn.*

![fullständiga genomsökningar av tabeller](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Om du vill felsöka problem med databasens prestanda kan det vara fördelaktigt att identifiera de händelser som händer i databasen, och med hjälp av *vyn sys.user_summary_by_statement_type* kan det vara bara att göra susen.

![sammanfattning efter utdrag](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

I det här exemplet azure-databas för MySQL tillbringade 53 minuter tömning slog frågeloggen 44579 gånger. Det är en lång tid och många IOs. Du kan minska den här aktiviteten genom att antingen inaktivera din långsamma frågelogg eller minska frekvensen för långsam frågeinloggning Azure portal.

## <a name="database-maintenance"></a>Underhåll av databasen

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

InnoDB-buffertpoolen finns i minnet och är den viktigaste cachemekanismen mellan DBMS och lagring. Storleken på InnoDB-buffertpoolen är knuten till prestandanivån och kan inte ändras om inte en annan produkt SKU har valts. Som med minne i operativsystemet byts gamla sidor ut för att göra plats för nyare data. Om du vill ta reda på vilka tabeller som förbrukar det mesta av InnoDB-buffertpoolens minne kan du fråga *sys.innodb_buffer_stats_by_table* vyn.

![Buffertstatus för InnoDB](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

I bilden ovan är det uppenbart att andra än systemtabeller och vyer, varje tabell i mysqldatabase033-databasen, som är värd för en av mina WordPress-webbplatser, upptar 16 KB, eller 1 sida, av data i minnet.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Index är bra verktyg för att förbättra läsprestanda, men de medför extra kostnader för skär och lagring. *Sys.schema_unused_indexes* och *sys.schema_redundant_indexes* ger insikter om oanvända eller duplicerade index.

![oanvända index](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundanta index](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Slutsats

Sammanfattningsvis är sys_schema ett bra verktyg för både prestandajustering och databasunderhåll. Se till att dra nytta av den här funktionen i din Azure-databas för MySQL. 

## <a name="next-steps"></a>Nästa steg
- Om du vill hitta peer-svar på dina mest berörda frågor eller lägga upp en ny fråga /svar besöker du [MSDN-forumet](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
