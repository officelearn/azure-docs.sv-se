---
title: "Felsöka Azure SQL Data Warehouse | Microsoft Docs"
description: "Felsöka Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/15/2018
ms.author: kevin;barbkess
ms.openlocfilehash: 0829d448e8b925d0dcc032ed143d8fff42ab1b69
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Felsöka Azure SQL Data Warehouse
Det här avsnittet innehåller några av de vanligaste felsökning frågor som vi får höra från våra kunder.

## <a name="connecting"></a>Ansluter
| Problem | Lösning |
|:--- |:--- |
| Inloggningen misslyckades för användaren 'NT AUTHORITY\\ANONYMOUS LOGON ”. (Microsoft SQL Server, Error: 18456) |Det här felet uppstår när en AAD-användare försöker ansluta till master-databasen, men har inte en användare i master.  Om du vill åtgärda det här problemet, ange antingen SQL Data Warehouse som du vill ansluta till vid anslutningen, eller lägga till användaren i master-databasen.  Se [Säkerhetsöversikt] [ Security overview] mer information. |
| Servern huvudnamn ”MittAnvändarnamn” går inte att komma åt databasen ”master” under det aktuella säkerhetssammanhanget. Det går inte att öppna användarens standarddatabas. Inloggningen misslyckades. Inloggningen misslyckades för användaren 'MittAnvändarnamn'. (Microsoft SQL Server, Error: 916) |Det här felet uppstår när en AAD-användare försöker ansluta till master-databasen, men har inte en användare i master.  Om du vill åtgärda det här problemet, ange antingen SQL Data Warehouse som du vill ansluta till vid anslutningen, eller lägga till användaren i master-databasen.  Se [Säkerhetsöversikt] [ Security overview] mer information. |
| CTAIP fel |Det här felet kan inträffa när en inloggning som har skapats på SQL server-huvuddatabasen, men inte i SQL Data Warehouse-databas.  Om du får det här felet kan ta en titt på den [Säkerhetsöversikt] [ Security overview] artikel.  Den här artikeln förklarar hur du skapar en inloggning och användaren på master och hur du skapar en användare i SQL Data Warehouse-databas. |
| Blockeras av brandvägg |Azure SQL-databaser är skyddade av servern och databasen nivån brandväggar så bara kända IP-adresser som har åtkomst till en databas. Brandväggarna är säkra som standard, vilket innebär att du uttryckligen aktivera och IP-adressen eller adressintervallet innan du kan ansluta.  Om du vill konfigurera brandväggen för åtkomst, följer du stegen i [konfigurera brandväggen serveråtkomst för klientens IP-Adressen] [ Configure server firewall access for your client IP] i den [etablering instruktioner] [Provisioning instructions]. |
| Det går inte att ansluta med verktyget eller drivrutinen |SQL Data Warehouse rekommenderar att du använder [SSMS][SSMS], [SSDT för Visual Studio][SSDT for Visual Studio], eller [sqlcmd] [ sqlcmd] fråga dina data. Mer information om drivrutiner och ansluta till SQL Data Warehouse, se [drivrutiner för Azure SQL Data Warehouse] [ Drivers for Azure SQL Data Warehouse] och [Anslut till Azure SQL Data Warehouse] [ Connect to Azure SQL Data Warehouse] artiklar. |

## <a name="tools"></a>Verktyg
| Problem | Lösning |
|:--- |:--- |
| Visual Studio object explorer saknas i AAD-användare |Detta är ett känt problem.  Som en tillfällig lösning kan du visa användarna i [sys.database_principals][sys.database_principals].  Se [autentisering till Azure SQL Data Warehouse] [ Authentication to Azure SQL Data Warehouse] att lära dig mer om hur du använder Azure Active Directory med SQL Data Warehouse. |
|Manuell scripting, med hjälp av guiden skript eller ansluter via SSMS är långsam, låsta eller berörda fel| Kontrollera att användarna har skapats i master-databasen. I skriptalternativen, också kontrollera att motorn edition har angetts som ”Microsoft Azure SQL Data Warehouse Edition” och motor är ”Microsoft Azure SQL Database”.|

## <a name="performance"></a>Prestanda
| Problem | Lösning |
|:--- |:--- |
| Felsökning av frågan prestanda |Om du vill felsöka en viss fråga startar med [Lär dig hur du övervakar dina frågor][Learning how to monitor your queries]. |
| Dåliga prestanda och planer är ofta ett resultat av saknas statistik |Den vanligaste orsaken till dåliga prestanda är statistik på tabellerna saknas.  Se [underhålla tabellstatistik] [ Statistics] för information om hur du skapar statistik och varför de är viktiga för din prestanda. |
| Låg samtidighet / frågor i kö |Förstå [hantering av arbetsbelastning] [ Workload management] är viktigt för att förstå hur du ska hantera minnesallokering med samtidighet. |
| Hur du implementerar bästa praxis |Är det enklast att börja lära dig att förbättra frågeprestanda [SQL Data Warehouse metodtips] [ SQL Data Warehouse best practices] artikel. |
| Hur vi kan förbättra prestanda med skalning |Ibland lösningen för att förbättra prestanda är att helt enkelt lägga till mer datorkraft frågor genom [skala ditt SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Dåliga prestanda på grund av dålig index kvalitet |Några tillfällen frågor kan sakta ned eftersom [dålig columnstore-index kvalitet][Poor columnstore index quality].  Finns den här artikeln för mer information och hur du [återskapa index för att förbättra kvaliteten segment][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Systemhantering
| Problem | Lösning |
|:--- |:--- |
| Felmeddelande 40847: Det gick inte att utföra åtgärden eftersom servern skulle överskrida den tillåtna kvoten Database Transaction Unit av 45000. |Minska den [DWU] [ DWU] av databasen som du försöker skapa eller [begära en ökad kvot][request a quota increase]. |
| Undersöka användningen |Se [tabell storlekar] [ Table sizes] att förstå användningen av systemet. |
| Att hantera tabeller |Finns det [tabell översikt] [ Overview] artikel för att få hjälp med att hantera dina tabeller.  Den här artikeln innehåller också länkar till mer detaljerad information som [tabell datatyper][Data types], [distribuerar en tabell][Distribute], [Indexering av en tabell][Index], [partitionering en tabell][Partition], [underhålla tabellstatistik] [ Statistics] och [temporära tabeller][Temporary]. |
|Transparent data kryptering (TDE) förloppsindikatorn uppdateras inte i Azure Portal|Du kan visa status för TDE via [powershell](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption).|

## <a name="polybase"></a>Polybase
| Problem | Lösning |
|:--- |:--- |
| Läs in misslyckas på grund av stora rader |Stor rad stöd är för närvarande inte tillgängligt för Polybase.  Det innebär att om tabellen innehåller VARCHAR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX), externa tabeller inte kan användas för att läsa in dina data.  Läser in stora rader är för närvarande stöds bara via Azure Data Factory (med BCP), Azure Stream Analytics, SSIS, BCP eller klassen .NET SqlBulkCopy körs. PolyBase-stöd för stora rader läggs till i en framtida version. |
| BCP belastningen på tabellen med MAX-datatypen kan inte |Det finns ett känt problem som kräver att VARCHAR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX) placeras i slutet av tabellen i vissa situationer.  Försök att flytta MAX-kolumner i slutet av tabellen. |

## <a name="differences-from-sql-database"></a>Skillnader från SQL-databas
| Problem | Lösning |
|:--- |:--- |
| Funktioner som inte stöds SQL-databas |Se [stöds inte i tabellen funktioner][Unsupported table features]. |
| Datatyper stöds inte SQL-databas |Se [datatyper][Unsupported data types]. |
| Ta bort och uppdatera begränsningar |Se [UPPDATERINGEN lösningar][UPDATE workarounds], [ta bort lösningar] [ DELETE workarounds] och [med CTAS undvika stöds inte UPPDATERINGEN och Ta bort syntax][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| MERGE-instruktionen stöds inte |Se [MERGE lösningar][MERGE workarounds]. |
| Begränsningar för lagrad procedur |Se [lagrade proceduren begränsningar] [ Stored procedure limitations] att förstå vissa begränsningar av lagrade procedurer. |
| UDF: er stöder inte SELECT-uttryck |Det här är en aktuell begränsning i vår UDF: er.  Se [CREATE FUNCTION] [ CREATE FUNCTION] syntaxen vi stöder. |

## <a name="next-steps"></a>Nästa steg
Här är några andra resurser som du kan försöka mer hjälp att hitta en lösning på problemet.

* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [CAT-teambloggar]
* [Skapa ett supportärende]
* [MSDN-forum]
* [Stack Overflow-forum]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT for Visual Studio]: ./sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: ./sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Skapa ett supportärende]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md
[request a quota increase]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: ./sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: ./sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: ./sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[Table sizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: ./resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md
[Working around the PolyBase UTF-8 requirement]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT-teambloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
