---
title: Artikel om felsökning av problem/fel som är associerade med att ansluta Azure Database Migration Service till källdatabaser kända | Microsoft Docs
description: Läs mer om hur du felsöker kända problem/fel som är associerade med att ansluta Azure Database Migration Service till källdatabaser.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: a4ebd1d4c85631cc6ebc1f5787e7545b78d62b5e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462855"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Felsöka DMS-fel när du ansluter till källdatabaser

I följande artikel finns information om hur du lösa potentiella problem som kan uppstå när du ansluter Azure Database Migration Service (DMS) till din källdatabas. Avsnitten nedan avser en viss typ av källdatabasen, visa en lista över fel som kan uppstå tillsammans med information och länkar till information om felsökning av anslutningen.

## <a name="sql-server"></a>SQL Server

Potentiella problem som är associerade med att ansluta till en SQL Server-databasen och hur du hantera dem finns i följande tabell.

| Fel         | Orsak och felsökning av information |
| ------------- | ------------- |
| Det gick inte att SQL-anslutning. Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig. Kontrollera att instansnamnet är korrekt och att SQL Server är konfigurerad för att tillåta fjärranslutningar.<br> | Det här felet uppstår om tjänsten inte kan hitta källservern. Om du vill åtgärda problemet finns i artikeln [gick inte att ansluta till SQL Server-källans när du använder dynamisk port eller namngiven instans](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Fel 53** -SQL-anslutningen misslyckades. (Dessutom för fel kodar 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Det här felet uppstår om tjänsten inte kan ansluta till källservern. Se följande resurser för att åtgärda problemet och försök sedan igen. <br><br>  [Interaktiva användarhandboken för att felsöka problem med nätverksanslutningen](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Förutsättningar för att migrera SQLServer till Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Förutsättningar för att migrera SQLServer till en Azure SQL Database-hanterad instans](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Fel 18456** -inloggningen misslyckades.<br> | Det här felet uppstår om tjänsten inte kan ansluta till källdatabasen med de angivna autentiseringsuppgifterna för T-SQL. Kontrollera de angivna autentiseringsuppgifterna för att åtgärda problemet. Du kan även gå till [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) eller att de felsökning dokument som anges i noteringen under den här tabellen, och försök sedan igen. |
| Felaktigt värde för AccountName '{0}' tillhandahålls. Förväntade formatet för kontonamn är Domännamn\användarnamn<br> | Det här felet uppstår om användaren väljer Windows-autentisering men innehåller användarnamnet i ett ogiltigt format. Om du vill åtgärda problemet antingen ange användarnamn i rätt format för Windows-autentisering eller välj **SQL-autentisering**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Potentiella problem som är associerade med att ansluta till en datakälla AWS RDS MySQL-databas och hur du hantera dem finns i följande tabell.

| Fel         | Orsak och felsökning av information |
| ------------- | ------------- |
| **Fel [2003]** [HY000] - anslutningen misslyckades. FEL [HY000] [MySQL] [ODBC-drivrutin för x.x(w)] kan inte ansluta till MySQL-server på {server} (10060) | Det här felet uppstår om MySQL ODBC-drivrutinen inte kan ansluta till källservern. Referera till de felsökning dokument som anges i noteringen under den här tabellen för att åtgärda problemet och försök sedan igen.<br> |
| **Fel [2005]** [HY000] - anslutningen misslyckades. FEL [HY000] [MySQL] [ODBC-drivrutin för x.x(w)] okänd MySQL server-värd {server} | Det här felet uppstår om tjänsten inte kan hitta källvärden på Fjärrskrivbordstjänster. Problemet kan antingen bero listade källan inte finns eller om det finns ett problem med RDS-infrastruktur. Referera till de felsökning dokument som anges i noteringen under den här tabellen för att åtgärda problemet och försök sedan igen.<br> |
| **Fel [1045]** [HY000] - anslutningen misslyckades. FEL [HY000] [MySQL] [ODBC-drivrutin för x.x(w)] åtkomst nekades för användaren {user}”@”{server} (med lösenord: YES) | Det här felet uppstår om MySQL ODBC-drivrutinen inte kan ansluta till källservern på grund av ogiltiga autentiseringsuppgifter. Kontrollera de autentiseringsuppgifter som du har angett. Om problemet kvarstår kontrollerar du att källdatorn har rätt autentiseringsuppgifter. Du kan behöva återställa lösenordet i konsolen. Om du fortfarande stöter på problemet kan referera till de felsökning dokument som anges i noteringen under den här tabellen och försök sedan igen.<br> |
| **Fel [9002]** [HY000] - anslutningen misslyckades. FEL [HY000] [MySQL] [ODBC-drivrutin för x.x(w)] anslutningssträngen kanske inte rätt. Gå till portalen för referenser.| Det här felet uppstår om anslutningen misslyckas på grund av ett problem med anslutningssträngen. Kontrollera den angivna anslutningssträngen är giltig. Referera till de felsökning dokument som anges i noteringen under den här tabellen för att åtgärda problemet och försök sedan igen.<br> |
| **Fel i binär loggning. Variabeln binlog_format har värdet {value}. Ändra den till ”rad”.** | Det här felet uppstår om det finns ett fel i binär loggning. variabeln binlog_format har fel värde. Ändra binlog_format i parametergruppen till ”rad” för att åtgärda problemet och starta sedan om instansen. Mer information finns i så här [binär loggningsalternativ och variabler](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) eller [AWS RDS MySQL-databasloggfiler dokumentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Mer information om hur du felsöker problem med att ansluta till en datakälla AWS RDS MySQL-databas finns i följande resurser:
> * [Felsökning för Amazon RDS-anslutningsproblem](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Hur löser problem med att ansluta till min Amazon RDS-databasinstansen?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS-PostgreSQL

Potentiella problem som är associerade med att ansluta till en datakälla AWS RDS PostgreSQL-databas och hur du hantera dem finns i följande tabell.

| Fel         | Orsak och felsökning av information |
| ------------- | ------------- |
| **Fel [101]** [08001] - anslutningen misslyckades. FEL [08001] tidsgränsen gick ut. | Det här felet uppstår om Postgres-drivrutinen inte kan ansluta till källservern. Referera till de felsökning dokument som anges i noteringen under den här tabellen för att åtgärda problemet och försök sedan igen. |
| **Fel: Parametern wal_level har värdet {value}. Ändra det till logiska så att replikeringen.** | Det här felet uppstår om parametern wal_level har fel värde. Ändra rds.logical_replication i parametergruppen till 1 för att åtgärda problemet och starta sedan om instansen. Mer information finns i så här [förutsättningar för att migrera till Azure PostgreSQL med DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) eller [PostgreSQL på Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Mer information om hur du felsöker problem med att ansluta till en datakälla AWS RDS PostgreSQL-databas finns i följande resurser:
> * [Felsökning för Amazon RDS-anslutningsproblem](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Hur löser problem med att ansluta till min Amazon RDS-databasinstansen?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Potentiella problem som är associerade med att ansluta till en datakälla AWS RDS SQL Server-databas och hur du hantera dem finns i följande tabell.

| Fel         | Orsak och felsökning av information |
| ------------- | ------------- |
| **Fel 53** -SQL-anslutningen misslyckades. Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig. Kontrollera att instansnamnet är korrekt och att SQL Server är konfigurerad för att tillåta fjärranslutningar. (providern: Named Pipes-Provider, fel: 40 - det gick inte att öppna en anslutning till SQL Server | Det här felet uppstår om tjänsten inte kan ansluta till källservern. Referera till de felsökning dokument som anges i noteringen under den här tabellen för att åtgärda problemet och försök sedan igen. |
| **Fel 18456** -inloggningen misslyckades. Inloggningen misslyckades för användaren {user} | Det här felet uppstår om tjänsten inte kan ansluta till källdatabasen med de angivna autentiseringsuppgifterna för T-SQL. Kontrollera de angivna autentiseringsuppgifterna för att åtgärda problemet. Du kan även gå till [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) eller att de felsökning dokument som anges i noteringen under den här tabellen, och försök igen. |
| **Fel 87** -anslutningssträngen är inte giltig. Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig. Kontrollera att instansnamnet är korrekt och att SQL Server är konfigurerad för att tillåta fjärranslutningar. (providern: SQL-nätverksgränssnitt, fel: 25 - anslutningssträngen är inte giltig) | Det här felet uppstår om tjänsten inte kan ansluta till källservern på grund av en ogiltig anslutningssträng. Kontrollera anslutningssträngen tillhandahålls för att åtgärda problemet. Om problemet kvarstår, referera till de felsökning dokument som anges i noteringen under den här tabellen och försök sedan igen. |
| **Fel – servercertifikatet inte är betrodd.** En anslutning upprättades har med servern, men ett fel uppstod under inloggningen. (providern: SSL-Provider, fel: 0 – certifikatkedjan har utfärdats av en utfärdare som inte är betrodd.) | Det här felet uppstår om certifikatet som används inte är betrodd. Om du vill åtgärda problemet måste du hitta ett certifikat som kan vara betrodda och sedan aktivera det på servern. Du kan även välja alternativet lita på certifikat vid anslutning. Ta den här åtgärden endast om du är bekant med det certifikat som används och har förtroende. <br> SSL-anslutningar som krypteras med hjälp av ett självsignerat certifikat ger inte en hög säkerhet – de är sårbar för man-in-the-middle-attacker. Förlita dig inte på SSL använder självsignerade certifikat i en produktionsmiljö eller servrar som är anslutna till internet. <br> Mer information finns i så här [med hjälp av SSL med en Microsoft SQL Server DB-instans](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) eller [självstudien: Migrera Fjärrskrivbordstjänster SQL Server till Azure med DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Fel 300** -användaren har inte behörighet. VIEW SERVER STATE behörighet nekas för objekt {server}, databas {databasen} | Det här felet uppstår om användaren inte har behörighet att utföra migreringen. Om du vill åtgärda problemet behöver referera till [serverbehörigheter GRANT - Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) eller [självstudien: Migrera Fjärrskrivbordstjänster SQL Server till Azure med DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) för mer information. |

> [!NOTE]
> Mer information om hur du felsöker problem med anslutning till en datakälla AWS RDS SQL Server finns i följande resurser:
>
> * [Lösa anslutningsfel till SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Hur löser problem med att ansluta till min Amazon RDS-databasinstansen?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Kända problem

* [Begränsningar för kända problem/migrering med online migrering till Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Begränsningar för kända problem/migrering med online migrering till Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Begränsningar för kända problem/migrering med online migrering till Azure Database för PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Nästa steg

* Visa artikeln [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Visa artikeln [hur du konfigurerar serverparametrar i Azure Database för MySQL med hjälp av Azure portal](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Visa artikeln [översikt över krav för att använda Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Se den [vanliga frågor och svar om hur du använder Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
