---
title: Problem med att ansluta källdatabaser
titleSuffix: Azure Database Migration Service
description: Lär dig mer om hur du felsöker kända problem/fel i samband med anslutning av Azure Database Migration Service till källdatabaser.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3eef7476a729c7b83290b9d8d86ba06524bed72b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297090"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Felsöka DMS-fel vid anslutning till källdatabaser

I följande artikel beskrivs hur du åtgärdar potentiella problem som kan uppstå när du ansluter Azure Database Migration Service (DMS) till källdatabasen. Varje avsnitt nedan gäller en viss typ av källdatabas, med en lista över det fel du kan stöta på tillsammans med detaljer och länkar till information om hur du felsöker anslutningen.

## <a name="sql-server"></a>SQL Server

Potentiella problem med anslutning till en SQL Server-databas och hur du åtgärdar dem finns i följande tabell.

| Fel         | Orsak och felsökningsdetaljer |
| ------------- | ------------- |
| SQL-anslutningen misslyckades. Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig. Kontrollera att instansnamnet är korrekt och att SQL Server är konfigurerat för att tillåta fjärranslutningar.<br> | Det här felet uppstår om tjänsten inte kan hitta källservern. Åtgärda problemet genom att se artikeln [Fel vid anslutning till källan SQL Server när du använder dynamisk port eller namngiven instans](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Fel 53** - SQL-anslutningen misslyckades. (Även för felkoder 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Det här felet uppstår om tjänsten inte kan ansluta till källservern. Information om problemet finns i följande resurser och försöker sedan igen. <br><br>  [Interaktiv användarhandbok för felsökning av anslutningsproblemet](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Förutsättningar för att migrera SQL Server till Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Förutsättningar för att migrera SQL Server till en hanterad Azure SQL-databas-hanterad instans](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Fel 18456** - Inloggning misslyckades.<br> | Det här felet uppstår om tjänsten inte kan ansluta till källdatabasen med hjälp av de medföljande T-SQL-autentiseringsuppgifterna. Kontrollera de angivna autentiseringsuppgifterna för att lösa problemet. Du kan också referera till [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) eller felsökningsdokumenten i anteckningen nedanför den här tabellen och sedan försöka igen. |
| Felformat kontonamnsvärde{0}' ' som tillhandahålls. Förväntat format för AccountName är DomainName\UserName<br> | Det här felet uppstår om användaren väljer Windows-autentisering men tillhandahåller användarnamnet i ett ogiltigt format. Om du vill åtgärda problemet anger du användarnamn i rätt format för **Windows-autentisering**eller väljer SQL Authentication . |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Potentiella problem med anslutning till en aws-databas för aws-mysql-källa och hur du åtgärdar dem finns i följande tabell.

| Fel         | Orsak och felsökningsdetaljer |
| ------------- | ------------- |
| **Fel [2003]**[HY000] - anslutningen misslyckades. ERROR [HY000] [MySQL][ODBC x.x(w)-drivrutin] Det går inte att ansluta till MySQL-servern på {server} (10060) | Det här felet uppstår om MySQL ODBC-drivrutinen inte kan ansluta till källservern. Information om problemet finns i felsökningsdokumenten i anteckningen nedanför den här tabellen och försöker sedan igen.<br> |
| **Fel [2005]**[HY000] - anslutningen misslyckades. FEL [HY000] [MySQL][ODBC x.x(w) drivrutin] Okänd MySQL-servervärd {server}' | Det här felet uppstår om tjänsten inte kan hitta källvärden på RDS. Problemet kan antingen bero på att den angivna källan inte finns eller att det finns ett problem med RDS-infrastrukturen. Information om problemet finns i felsökningsdokumenten i anteckningen nedanför den här tabellen och försöker sedan igen.<br> |
| **Fel [1045]**[HY000] - anslutningen misslyckades. FEL [HY000] [MySQL][ODBC x.x(w)-drivrutin] Åtkomst nekad för användaren {user}'@'{server}' (med lösenord: JA) | Det här felet uppstår om MySQL ODBC-drivrutinen inte kan ansluta till källservern på grund av ogiltiga autentiseringsuppgifter. Kontrollera de autentiseringsuppgifter du har angett. Om problemet kvarstår kontrollerar du att källdatorn har rätt autentiseringsuppgifter. Du kan behöva återställa lösenordet i konsolen. Om problemet kvarstår läser du felsökningsdokumenten i anteckningen nedanför den här tabellen och försöker sedan igen.<br> |
| **Fel [9002]**[HY000] - anslutningen misslyckades. FEL [HY000] [MySQL][ODBC x.x(w)-drivrutin] Anslutningssträngen kanske inte är rätt. Besök portalen för referenser.| Det här felet uppstår om anslutningen misslyckas på grund av ett problem med anslutningssträngen. Kontrollera att anslutningssträngen är giltig. Information om problemet finns i felsökningsdokumenten i anteckningen nedanför den här tabellen och försöker sedan igen.<br> |
| **Fel i binär loggning. Variabeln binlog_format har värdet {value}. Ändra den till "rad".** | Det här felet uppstår om det finns ett fel i binär loggning. variabeln binlog_format har fel värde. Om du vill åtgärda problemet ändrar du binlog_format i parametergruppen till "RAD" och startar sedan om instansen. Mer information finns i [binära loggningsalternativ och variabler](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) eller [dokumentation av AWS RDS MySQL Database Log Files](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Mer information om felsökning av problem som rör anslutning till en AWS RDS MySQL-databas finns i följande resurser:
> * [Felsökning för anslutningsproblem med Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Hur löser jag problem med att ansluta till min Amazon RDS-databasinstans?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Potentiella problem med anslutning till en källa AWS RDS PostgreSQL databas och hur man tar itu med dem finns i följande tabell.

| Fel         | Orsak och felsökningsdetaljer |
| ------------- | ------------- |
| **Fel [101]**[08001] - anslutningen misslyckades. FEL [08001] timeout har upphört att gälla. | Det här felet uppstår om Postgres-drivrutinen inte kan ansluta till källservern. Information om problemet finns i felsökningsdokumenten i anteckningen nedanför den här tabellen och försöker sedan igen. |
| **Fel: Parametern wal_level har värdet {value}. Ändra den till "logisk" så att replikering tillåts.** | Det här felet uppstår om parametern wal_level har fel värde. Om du vill åtgärda problemet ändrar du rds.logical_replication i parametergruppen till 1 och startar sedan om instansen. Mer information finns i [Förskäligare för migrering till Azure PostgreSQL med DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) eller [PostgreSQL på Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Mer information om felsökning av problem som rör anslutning till en AWS RDS PostgreSQL-databas finns i följande resurser:
> * [Felsökning för anslutningsproblem med Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Hur löser jag problem med att ansluta till min Amazon RDS-databasinstans?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Potentiella problem med anslutning till en AWS RDS SQL Server-databas för källa och hur du åtgärdar dem finns i följande tabell.

| Fel         | Orsak och felsökningsdetaljer |
| ------------- | ------------- |
| **Fel 53** - SQL-anslutningen misslyckades. Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig. Kontrollera att instansnamnet är korrekt och att SQL Server är konfigurerat för att tillåta fjärranslutningar. (provider: Named Pipes Provider, error: 40 - Det gick inte att öppna en anslutning till SQL Server | Det här felet uppstår om tjänsten inte kan ansluta till källservern. Information om problemet finns i felsökningsdokumenten i anteckningen nedanför den här tabellen och försöker sedan igen. |
| **Fel 18456** - Inloggning misslyckades. Inloggningen misslyckades för användaren {user} | Det här felet uppstår om tjänsten inte kan ansluta till källdatabasen med de T-SQL-autentiseringsuppgifter som tillhandahålls. Kontrollera de angivna autentiseringsuppgifterna för att lösa problemet. Du kan också referera till [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) eller felsökningsdokumenten i anteckningen nedanför den här tabellen och försöka igen. |
| **Fel 87** - Anslutningssträngen är ogiltig. Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig. Kontrollera att instansnamnet är korrekt och att SQL Server är konfigurerat för att tillåta fjärranslutningar. (provider: SQL Network Interfaces, fel: 25 - Anslutningssträngen är ogiltig) | Det här felet uppstår om tjänsten inte kan ansluta till källservern på grund av en ogiltig anslutningssträng. Kontrollera anslutningssträngen som tillhandahålls för att lösa problemet. Om problemet kvarstår läser du felsökningsdokumenten i anteckningen nedanför den här tabellen och försöker sedan igen. |
| **Fel - Servercertifikatet är inte betrott.** En anslutning har upprättats med servern, men sedan uppstod ett fel under inloggningsprocessen. (provider: SSL Provider, fel: 0 - Certifikatkedjan har utfärdats av en myndighet som inte är betrodd.) | Det här felet uppstår om certifikatet som används inte är betrott. För att lösa problemet måste du hitta ett certifikat som du kan lita på och sedan aktivera det på servern. Du kan också välja alternativet Förtroendecertifikat när du ansluter. Utför bara den här åtgärden om du är bekant med certifikatet som används och du litar på det. <br> TLS-anslutningar som krypteras med ett självsignerat certifikat ger inte stark säkerhet – de är mottagliga för attacker mellan människor. Lita inte på TLS med självsignerade certifikat i en produktionsmiljö eller på servrar som är anslutna till Internet. <br> Mer information finns i [Använda SSL med en Microsoft SQL Server DB-instans](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) eller [självstudiekurs: Migrera RDS SQL Server till Azure med DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Fel 300** - Användaren har inte nödvändiga behörigheter. BEHÖRIGHETEN VIEW SERVER STATE nekades på objektet {server}, databasen {database} | Det här felet uppstår om användaren inte har behörighet att utföra migreringen. Information om problemet finns i [BEVILJA serverbehörigheter - Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) eller [självstudiekurs: Migrera RDS SQL Server till Azure med DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) för mer information. |

> [!NOTE]
> Mer information om felsökning av problem som rör anslutning till en aws RDS SQL Server finns i följande resurser:
>
> * [Lösa anslutningsfel till SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Hur löser jag problem med att ansluta till min Amazon RDS-databasinstans?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Kända problem

* [Kända problem/migreringsbegränsningar med onlinemigreringar till Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Kända problem/migreringsbegränsningar med onlinemigreringar till Azure Database för MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Kända problem/migreringsbegränsningar med onlinemigreringar till Azure Database för PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Nästa steg

* Visa artikeln [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Visa artikeln [Så här konfigurerar du serverparametrar i Azure Database för MySQL med hjälp av Azure-portalen](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Visa artikeln [Översikt över förutsättningar för att använda Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Se [vanliga frågor och svar om hur du använder Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
