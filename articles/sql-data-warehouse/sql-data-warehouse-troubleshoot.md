---
title: Felsöka Azure SQL Data Warehouse | Microsoft Docs
description: Felsöka Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/04/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: dc78fbc93d625b39379e07f240eef7fbad10d194
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003861"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Felsöka Azure SQL Data Warehouse
Den här artikeln innehåller vanliga frågor för felsökning.

## <a name="connecting"></a>Ansluter
| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Inloggningen misslyckades för användaren ”NT AUTHORITY\\ANONYMOUS LOGON”. (Microsoft SQL Server, fel: 18456) | Det här felet uppstår när en AAD-användare försöker ansluta till master-databasen, men har inte en användare i huvuddatabasen.  Om du vill åtgärda problemet antingen ange SQL Data Warehouse som du vill ansluta till vid anslutningstid eller lägga till användaren i master-databasen.  Se [Säkerhetsöversikt] [ Security overview] nedan för mer information. |
| Servern huvudnamn ”MyUserName” kan inte få åtkomst till databasen ”master” under det aktuella säkerhetssammanhanget. Det går inte att öppna användarens standarddatabas. Inloggningen misslyckades. Inloggningen misslyckades för användaren 'MyUserName'. (Microsoft SQL Server, fel: 916) | Det här felet uppstår när en AAD-användare försöker ansluta till master-databasen, men har inte en användare i huvuddatabasen.  Om du vill åtgärda problemet antingen ange SQL Data Warehouse som du vill ansluta till vid anslutningstid eller lägga till användaren i master-databasen.  Se [Säkerhetsöversikt] [ Security overview] nedan för mer information. |
| CTAIP fel                                                  | Det här felet kan inträffa när en inloggning som har skapats i SQL server master-databasen, men inte i SQL Data Warehouse-databas.  Om du får det här felet kan ta en titt på de [Säkerhetsöversikt] [ Security overview] artikeln.  Den här artikeln förklarar hur du skapar en inloggning och användare på master och hur du skapar en användare i SQL Data Warehouse-databas. |
| Blockeras av brandvägg                                          | Azure SQL-databaser skyddas av servern och databasen på brandväggar för att se till att endast kända IP-adresser som har åtkomst till en databas. Brandväggarna är säkert som standard, vilket innebär att du måste uttryckligen aktivera och IP-adressen eller adressintervallet innan du kan ansluta.  Om du vill konfigurera din brandvägg för åtkomst, följer du stegen i [konfigurera brandväggen serveråtkomst för din klient-IP] [ Configure server firewall access for your client IP] i den [etablering instruktioner] [Provisioning instructions]. |
| Det går inte att ansluta med verktyget eller drivrutinen                           | SQL Data Warehouse rekommenderar att du använder [SSMS][SSMS], [SSDT för Visual Studio][SSDT for Visual Studio], eller [sqlcmd] [ sqlcmd] att fråga data. Mer information om drivrutiner och ansluta till SQL Data Warehouse finns i [drivrutiner för Azure SQL Data Warehouse] [ Drivers for Azure SQL Data Warehouse] och [Anslut till Azure SQL Data Warehouse] [ Connect to Azure SQL Data Warehouse] artiklar. |

## <a name="tools"></a>Verktyg
| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio-Objektutforskaren saknar AAD-användare           | Det här är ett känt problem.  Som en lösning kan du visa användarna i [sys.database_principals][sys.database_principals].  Se [autentisera till Azure SQL Data Warehouse] [ Authentication to Azure SQL Data Warehouse] mer information om hur du använder Azure Active Directory med SQL Data Warehouse. |
| Manuell scripting, med hjälp av guiden skript eller ansluta via SSMS är långsam, inte svarar eller ger upphov till fel | Se till att användarna har skapats i huvuddatabasen. I skriptalternativ, också se till att versionen av motorn har angetts som ”Microsoft Azure SQL Data Warehouse Edition” och motortyp är ”Microsoft Azure SQL Database”. |
| Generera skript misslyckas i SSMS                             | Generera ett skript för SQL data warehouse misslyckas om alternativet ”generera skript för beroende objekt”-alternativet är inställt på ”True”. Som en lösning kan användare manuellt måste gå till Verktyg -> Alternativ -> SQL Server Object Explorer -> Generera skript för beroende alternativ och inställd på false |

## <a name="performance"></a>Prestanda
| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Prestandafelsökning för fråga                            | Om du vill felsöka en viss fråga, börjar du med [lära dig hur du övervakar dina frågor][Learning how to monitor your queries]. |
| Dålig frågeprestanda och planer är ofta ett resultat av saknas statistik | Den vanligaste orsaken med sämre prestanda är bristande statistik på dina tabeller.  Se [underhålla tabellstatistik] [ Statistics] mer information om hur du skapar statistik och varför de är viktiga för din prestanda. |
| Låg samtidighet / frågor i kö                             | Förstå [arbetsbelastningshantering] [ Workload management] är viktigt att förstå hur du balanserar minnesallokering med samtidighet. |
| Hur du implementerar Metodtips                              | Den bästa platsen för att lära dig att förbättra frågeprestanda är [Metodtips för SQL Data Warehouse] [ SQL Data Warehouse best practices] artikeln. |
| Förbättra prestandan med skalning                      | Ibland lösning för att förbättra prestanda är att helt enkelt lägga till mer datorkraft i dina frågor av [skala ditt SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Dåliga prestanda på grund av dålig index kvalitet     | Ibland kan saktas ned grund av [Poor columnstore index quality][Poor columnstore index quality].  Se den här artikeln för mer information och hur du [återskapa index för att förbättra segmentkvaliteten][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Systemhantering
| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Det gick inte att utföra åtgärden eftersom servern skulle överskrida den tillåtna Database Transaction Unit-kvoten för 45000. | Antingen den [DWU] [ DWU] av databasen som du försöker skapa eller [öka kvoten][request a quota increase]. |
| Undersöka användningen                              | Se [tabellen storlekar] [ Table sizes] att förstå användningen av systemet. |
| Hjälp med att hantera tabeller                                    | Se den [tabellöversikt] [ Overview] artikeln om du behöver hjälp med att hantera dina tabeller.  Den här artikeln innehåller också länkar till mer detaljerad information som [Tabelldatatyper][Data types], [distribuerar en tabell][Distribute], [Indexera en tabell][Index], [partitionering en tabell][Partition], [underhålla tabellstatistik] [ Statistics] och [temporära tabeller][Temporary]. |
| Transparent data encryption (TDE) förloppsindikatorn uppdateras inte i Azure Portal | Du kan visa status för transparent Datakryptering via [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |

## <a name="polybase"></a>Polybase
| Problem                                           | Lösning                                                   |
| :---------------------------------------------- | :----------------------------------------------------------- |
| Läs in misslyckas på grund av stora rader                | Stöd för stor rad är för närvarande inte tillgängligt för Polybase.  Det innebär att om tabellen innehåller VARCHAR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX), externa tabeller inte kan användas för att läsa in dina data.  Läser in stora rader är för närvarande stöds endast via Azure Data Factory (med BCP), Azure Stream Analytics, SSIS, BCP eller klassen .NET SqlBulkCopy körs. PolyBase-stöd för stora rader läggs till i en framtida version. |
| BCP belastningen på tabellen med MAX-datatypen kan inte | Det finns ett känt problem som kräver att VARCHAR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX) placeras i slutet av tabellen i vissa situationer.  Försök att flytta dina maximalt antal kolumner i slutet av tabellen. |

## <a name="differences-from-sql-database"></a>Skillnader från SQL-databas
| Problem                                 | Lösning                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| SQL Database-funktioner som inte stöds     | Se [table-funktioner som inte stöds][Unsupported table features]. |
| Datatyper stöds inte SQL-databas   | Se [datatyper][Unsupported data types].        |
| Ta bort och uppdatera begränsningar         | Se [uppdatering lösningar][UPDATE workarounds], [ta bort lösningar] [ DELETE workarounds] och [med hjälp av CTAS för att kringgå stöds inte UPPDATERINGEN och Ta bort syntax][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| MERGE-instruktion stöds inte      | Se [MERGE lösningar][MERGE workarounds].                  |
| Begränsningar för lagrad procedur          | Se [lagrade proceduren begränsningar] [ Stored procedure limitations] att förstå vissa begränsningar för lagrade procedurer. |
| UDF: er stöder inte SELECT-uttryck | Det här är en aktuell begränsning i vår UDF: er.  Se [CREATE FUNCTION] [ CREATE FUNCTION] syntaxen som vi har stöd för. |

## <a name="next-steps"></a>Nästa steg
För mer hjälp med att hitta lösning på problemet, är här några resurser som du kan prova.

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
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Skapa ett supportärende]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT-teambloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
