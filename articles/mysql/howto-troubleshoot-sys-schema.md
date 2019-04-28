---
title: Så här används sys_schema för prestandajustering och databasunderhåll i Azure Database for MySQL
description: Den här artikeln beskriver hur du använder sys_schema för att hitta problem med prestanda och underhåll databas i Azure Database för MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/01/2018
ms.openlocfilehash: 993c77056c09c1dc21d5317ddbfe8e937341718d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422379"
---
# <a name="how-to-use-sysschema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Hur du använder sys_schema för justering och databasen Underhåll i Azure Database for MySQL

MySQL-performance_schema, först i MySQL 5.5 innehåller instrumentering för många viktiga serverresurser som minnesallokering, lagrade program, metadata låsning, etc. Men performance_schema innehåller mer än 80 tabeller och informationen som krävs ofta kräver koppla tabeller inom performance_schema, samt tabeller från information_schema. Bygger på både performance_schema och information_schema, sys_schema ger en kraftfull uppsättning [användarvänliga vyer](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) i en skrivskyddad databas och är helt aktiverat i Azure Database för MySQL version 5.7.

![vyer för sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Det finns 52 vyer i sys_schema och vyerna har en av följande prefix:

- Host_summary eller i/o: I/o-relaterade svarstider.
- InnoDB: InnoDB Bufferstatus och lås.
- Minne: Minnesanvändning för värd och användare.
- Schema: Schema-relaterad information, om du till exempel automatisk ökning, index, osv.
- Instruktion: Information om SQL-uttryck Det kan vara instruktionen som resulterade i fullständig tabellsökning eller lång Frågetid.
- Användare: Resurser som används och grupperade efter användare. Exempel är filen I/o, anslutningar och minne.
- Vänta: Vänta händelser grupperade efter värden eller användare.

Nu ska vi titta på några vanliga användningsmönster för av sys_schema. Vi ska börja med att gruppera användningsmönster i två kategorier: **Prestandajustering** och **databasen Underhåll**.

## <a name="performance-tuning"></a>Prestandajustering

### <a name="sysusersummarybyfileio"></a>*sys.user_summary_by_file_io*

I/o är dyraste åtgärden i databasen. Vi kan se den genomsnittliga i/o-svarstiden genom att fråga den *sys.user_summary_by_file_io* vy. Med 125 GB allokerat lagringsutrymme, min i/o-svarstid är ungefär 15 sekunder.

![i/o-svarstid: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Eftersom Azure Database for MySQL kan skalas i/o med avseende på lagring, efter att ha ökat min etablerad lagring till 1 TB, minskar mitt i/o-svarstid till 571 ms.

![i/o-svarstid: 1TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschematableswithfulltablescans"></a>*sys.schema_tables_with_full_table_scans*

Många frågor kan fortfarande resultera i fullständiga genomsökningar trots noggrann planering. Mer information om vilka typer av index och hur du optimerar dem kan du referera till den här artikeln: [Så här felsöker du frågeprestanda](./howto-troubleshoot-query-performance.md). Fullständig tabellsökningar är resurskrävande och försämra databasens prestanda. Det snabbaste sättet att hitta tabeller med fullständig tabellsökning är att fråga den *sys.schema_tables_with_full_table_scans* vy.

![fullständig tabellsökningar](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysusersummarybystatementtype"></a>*sys.user_summary_by_statement_type*

Felsökning av problem med databasen prestanda kan det vara fördelaktigt att identifiera vilka händelser som händer i din databas och använder den *sys.user_summary_by_statement_type* vyn kan bara använda en urvalsfråga.

![Sammanfattning av instruktionen](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

I det här exemplet att Azure Database for MySQL 53 minuter tömdes frågeloggen slog 44579 gånger. Det är en lång tid och många IOs. Du kan minska den här aktiviteten genom att antingen inaktivera din långsam frågelogg eller minska frekvensen för långsam fråga inloggningen Azure-portalen.

## <a name="database-maintenance"></a>Databasunderhåll

### <a name="sysinnodbbufferstatsbytable"></a>*sys.innodb_buffer_stats_by_table*

InnoDB buffertpoolen finns i minnet och är den viktigaste cache mekanismen mellan DBMS och storage. Storleken på buffertpoolen InnoDB är knuten till prestandanivån och kan inte ändras om inte en annan produkt SKU är valt. Precis som med minne i ditt operativsystem, byta gamla sidor ut för att göra plats för nyare data. Om du vill ta reda på vilka tabeller använda de flesta av InnoDB pool buffertminne kan du fråga den *sys.innodb_buffer_stats_by_table* vy.

![InnoDB Bufferstatus](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

I bilden ovan är det tydligt att än systemtabeller och vyer, varje tabell i mysqldatabase033-databasen, som värd för någon av Mina WordPress-webbplatser, tar upp 16 KB eller 1 sida av data i minnet.

### <a name="sysschemaunusedindexes--sysschemaredundantindexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Index är fantastiska verktyg för att förbättra läsprestanda, men de medför ytterligare kostnader för infogningar och lagring. *Sys.schema_unused_indexes* och *sys.schema_redundant_indexes* ge insikter om oanvänt eller duplicerat index.

![oanvända index](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundant index](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Sammanfattning

Sammanfattningsvis är sys_schema ett bra verktyg för både prestanda justering och databasunderhåll. Se till att dra nytta av den här funktionen i din Azure Database för MySQL. 

## <a name="next-steps"></a>Nästa steg
- Om du vill hitta peer-svar på dina mest berörda frågor eller publicera en ny fråga/svar, besök [MSDN-forum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
