---
title: Felsöka Azure SQL Data Warehouse | Microsoft Docs
description: Felsöka Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 7/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 04d63b2c1583228a274c0ba21c87df08886f5cdb
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619064"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Felsöka Azure SQL Data Warehouse
Den här artikeln innehåller vanliga fel söknings frågor.

## <a name="connecting"></a>Ansluter
| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Inloggningen misslyckades för användaren NT AUTHORITY\ANONYMOUS-inloggning. (Microsoft SQL Server, Fel: 18456) | Felet uppstår när en AAD-användare försöker ansluta till huvud databasen, men inte har en användare i huvud gruppen.  Du kan åtgärda det här problemet genom att antingen ange SQL Data Warehouse som du vill ansluta till vid anslutningen eller lägga till användaren i huvud databasen.  Mer information finns i [säkerhets översikts][Security overview] artikeln. |
| Serverns huvud namn kan inte komma åt databasen "Master" i det aktuella säkerhets sammanhanget. Det går inte att öppna användarens standard databas. Inloggningen misslyckades. Inloggningen misslyckades för användaren ' användar namn '. (Microsoft SQL Server, Fel: 916) | Felet uppstår när en AAD-användare försöker ansluta till huvud databasen, men inte har en användare i huvud gruppen.  Du kan åtgärda det här problemet genom att antingen ange SQL Data Warehouse som du vill ansluta till vid anslutningen eller lägga till användaren i huvud databasen.  Mer information finns i [säkerhets översikts][Security overview] artikeln. |
| CTAIP-fel                                                  | Det här felet kan inträffa när en inloggning har skapats på SQL Server-huvuddatabasen, men inte i SQL Data Warehouse databasen.  Om du stöter på det här felet kan du ta en titt på artikeln [säkerhets översikt][Security overview] .  Den här artikeln förklarar hur du skapar en inloggning och användare på huvud servern och hur du skapar en användare i SQL Data Warehouse databasen. |
| Blockerad av brand väggen                                          | Azure SQL-databaser skyddas av brand väggar på Server-och databas nivå så att endast kända IP-adresser har åtkomst till en databas. Brand väggarna är säkra som standard, vilket innebär att du måste uttryckligen aktivera och IP-adresser eller adress intervall innan du kan ansluta.  Konfigurera brand väggen för åtkomst genom att följa stegen i [Konfigurera serverns brand Väggs åtkomst för klientens IP-adress][Configure server firewall access for your client IP] i [etablerings anvisningarna][Provisioning instructions]. |
| Det går inte att ansluta med verktyget eller driv rutinen                           | SQL Data Warehouse rekommenderar att du använder [SSMS][SSMS], [SSDT för Visual Studio][SSDT for Visual Studio]eller [SQLCMD][sqlcmd] för att fråga dina data. Mer information om driv rutiner och hur du ansluter till SQL Data Warehouse finns i [driv rutiner för Azure SQL Data Warehouse][Drivers for Azure SQL Data Warehouse] och [ansluta till Azure SQL Data Warehouse][Connect to Azure SQL Data Warehouse] artiklar. |

## <a name="tools"></a>Verktyg
| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| AAD-användare saknas i Visual Studio Object Explorer           | Detta är ett känt problem.  Som en lösning kan du Visa användarna i [sys. database_principals][sys.database_principals].  Mer information om hur du använder Azure Active Directory med SQL Data Warehouse finns i [autentisering till Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] . |
| Manuell skript användning med skript guiden eller anslutning via SSMS är långsam, svarar inte eller skapar fel | Se till att användarna har skapats i huvud databasen. I skript alternativ ser du också till att motor versionen är inställd på "Microsoft Azure SQL Data Warehouse Edition" och att motor typen är "Microsoft Azure SQL Database". |
| Generera skript Miss lyckas i SSMS                               | Det går inte att skapa ett skript för SQL Data Warehouse om alternativet "skapa skript för beroende objekt" är inställt på "true". Som en lösning måste användarna manuellt gå till Verktyg-> Alternativ-> SQL Server Object Explorer-> Skapa skript för beroende alternativ och ange värdet FALSKT |

## <a name="performance"></a>Prestanda
| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Felsöka prestanda fel sökning                            | Om du försöker Felsöka en viss fråga börjar du med [att lära dig hur du övervakar dina frågor][Learning how to monitor your queries]. |
| Dåliga frågor och prestanda är ofta en följd av statistik som saknas | Den vanligaste orsaken till dåliga prestanda är brist på statistik för dina tabeller.  Mer information om hur du skapar statistik och varför de är viktiga för dina prestanda finns i [underhålla tabell statistik][Statistics] . |
| Låg concurrency/frågor i kö                             | Det är viktigt att förstå [arbets belastnings hanteringen][Workload management] för att förstå hur du balanserar minnesallokering med samtidighet. |
| Så här implementerar du bästa metoder                              | Den bästa platsen för att lära dig hur du kan förbättra prestanda för frågor är [SQL Data Warehouse metod tips][SQL Data Warehouse best practices] . |
| Förbättra prestanda med skalning                      | Ibland är lösningen för att förbättra prestandan att helt enkelt lägga till mer beräknings kraft för dina frågor genom [att skala SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Dåliga frågeresultat på grund av dålig index kvalitet     | Vissa gånger kan frågor sakta ned på grund av [dålig kolumn kvalitet i columnstore-index][Poor columnstore index quality].  Se den här artikeln för mer information och hur du [bygger om index för att förbättra segment kvaliteten][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>System hantering
| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| MSG 40847: Det gick inte att utföra åtgärden eftersom servern skulle överskrida den tillåtna kvoten för databas transaktions enheten på 45000. | Du kan antingen minska [DWU][DWU] för den databas som du försöker skapa eller [begära en kvot ökning][request a quota increase]. |
| Undersöka utrymmes användningen                              | Se [tabell storlekar][Table sizes] för att förstå systemets utrymmes användning. |
| Hjälp med att hantera tabeller                                    | Mer information om hur du hanterar tabeller finns i artikeln [Översikt över][Overview] tabeller.  Den här artikeln innehåller också länkar till mer detaljerade ämnen som [tabell data typer][Data types], [distribuera en tabell][Distribute], [indexera en tabell][Index], [partitionera en tabell][Partition], [underhålla tabell statistik][Statistics] och [temporära tabeller][Temporary]. |
| Förlopps indikatorn för transparent data kryptering (TDE) uppdateras inte i Azure Portal | Du kan visa statusen för TDE via [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Skillnader från SQL Database
| Problem                                 | Lösning                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| SQL Database funktioner som inte stöds     | Se [tabell funktioner som inte stöds][Unsupported table features]. |
| SQL Database data typer som inte stöds   | Se [data typer som inte stöds][Unsupported data types].        |
| TA bort och uppdatera begränsningar         | Se [uppdaterings lösningar][UPDATE workarounds], [ta bort lösningar][DELETE workarounds] och [Använd CTAs för att arbeta runt en uppdaterings-och borttagnings-syntax som inte stöds][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| MERGE-instruktionen stöds inte      | Se [sammanfognings lösningar][MERGE workarounds].                  |
| Begränsningar för lagrade procedurer          | Se [begränsningar för lagrade][Stored procedure limitations] procedurer för att förstå vissa begränsningar för lagrade procedurer. |
| UDF: er stöder inte SELECT-instruktioner | Detta är en aktuell begränsning i vår UDF: er.  Se [skapa funktion][CREATE FUNCTION] för den syntax som vi stöder. |

## <a name="next-steps"></a>Nästa steg
Om du vill ha hjälp med att hitta en lösning på problemet kan du göra något av följande resurser.

* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [CAT team-Bloggar]
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
[CAT team-Bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
