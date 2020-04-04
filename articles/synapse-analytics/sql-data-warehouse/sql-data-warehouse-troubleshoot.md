---
title: Felsökning
description: Felsöka Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: b24706943cdf59fba89a8007c4914b628b9e34d5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632969"
---
# <a name="troubleshooting-sql-analytics-in-azure-synapse"></a>Felsöka SQL Analytics i Azure Synapse

I den här artikeln visas vanliga felsökningsfråga.

## <a name="connecting"></a>Ansluta

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Inloggningen misslyckades för användaren NT AUTHORITY\ANONYMOUS LOGON. (Microsoft SQL Server, Fel: 18456) | Det här felet uppstår när en Azure AD-användare försöker ansluta till huvuddatabasen, men inte har en användare i huvudhanteraren.  Om du vill åtgärda problemet anger du antingen den SQL-pool som du vill ansluta till vid anslutning eller lägger till användaren i huvuddatabasen.  Mer information finns i artikeln [Säkerhetsöversikt.](sql-data-warehouse-overview-manage-security.md) |
| Serverns huvudnamn MyUserName kan inte komma åt huvuddatabasen i den aktuella säkerhetskontexten. Det går inte att öppna användarens standarddatabas. Det gick inte att logga in. Inloggningen misslyckades för användaren MyUserName. (Microsoft SQL Server, Fel: 916) | Det här felet uppstår när en Azure AD-användare försöker ansluta till huvuddatabasen, men inte har en användare i huvudhanteraren.  Om du vill åtgärda problemet anger du antingen den SQL-pool som du vill ansluta till vid anslutning eller lägger till användaren i huvuddatabasen.  Mer information finns i artikeln [Säkerhetsöversikt.](sql-data-warehouse-overview-manage-security.md) |
| CTAIP-fel                                                  | Det här felet kan uppstå när en inloggning har skapats i SQL-serverhuvuddatabasen, men inte i SQL-databasen.  Om du stöter på det här felet kan du ta en titt på artikeln [Säkerhetsöversikt.](sql-data-warehouse-overview-manage-security.md)  I den här artikeln beskrivs hur du skapar en inloggning och användare på huvudskärmen och hur du skapar en användare i SQL-databasen. |
| Blockerad av brandvägg                                          | SQL-pooler skyddas av brandväggar för att säkerställa att endast kända IP-adresser har åtkomst till en databas. Brandväggarna är säkra som standard, vilket innebär att du uttryckligen måste aktivera och IP-adress eller adressintervall innan du kan ansluta.  Om du vill konfigurera brandväggen för åtkomst följer du anvisningarna i [Konfigurera åtkomst till serverbrandväggen för klient-IP](create-data-warehouse-portal.md) i [etableringsinstruktionerna](create-data-warehouse-portal.md). |
| Det går inte att ansluta till verktyg eller drivrutin                           | Synapse SQL-pool rekommenderar att du använder [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15), [SSDT för Visual Studio](sql-data-warehouse-install-visual-studio.md)eller [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) för att fråga dina data. Mer information om drivrutiner och anslutning till Azure Synapse finns i [Drivrutiner för Azure Synapse](sql-data-warehouse-connection-strings.md) och [Connect to Azure Synapse-artiklar.](sql-data-warehouse-connect-overview.md) |

## <a name="tools"></a>Verktyg

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio-objektutforskaren saknar Azure AD-användare           | Detta är ett känt problem.  Som en lösning visar du användarna i [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15).  Se [Autentisering till Azure Synapse](sql-data-warehouse-authentication.md) om du vill veta mer om hur du använder Azure Active Directory med Synapse SQL-pool. |
| Manuell skript, med hjälp av skriptguiden eller anslutning via SSMS är långsam, svarar inte eller skapar fel | Kontrollera att användare har skapats i huvuddatabasen. I skriptalternativ, se också till att motorutgåvan är inställd som "Microsoft Azure SQL Data Warehouse Edition" och motortypen är "Microsoft Azure SQL Database". |
| Generera skript misslyckas i SSMS                               | Generera ett skript för Synapse SQL pool misslyckas om alternativet "Generera skript för beroende objekt" är inställt på "True". Som en lösning måste användarna manuellt gå till **Verktyg -> Alternativ ->SQL Server Object Explorer -> Generera skript för beroende alternativ och ange false** |

## <a name="performance"></a>Prestanda

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Felsökning av frågeprestanda                            | Om du försöker felsöka en viss fråga börjar du med [Att lära dig hur du övervakar dina frågor](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problem med TempDB-utrymme | [Övervaka](sql-data-warehouse-manage-monitor.md#monitor-tempdb) TempDB-utrymmesanvändning.  Vanliga orsaker till att TempDB-utrymmet tar är:<br>- Inte tillräckligt med resurser som allokerats till frågan vilket gör att data spills till TempDB.  Se [Hantering av arbetsbelastning](resource-classes-for-workload-management.md) <br>- Statistik saknas eller är föråldrad orsakar överdriven dataförflyttning.  Se [Underhålla tabellstatistik](sql-data-warehouse-tables-statistics.md) för mer information om hur du skapar statistik<br>- TempDB-utrymme allokeras per servicenivå.  [Skala din SQL-pool](sql-data-warehouse-manage-compute-overview.md#scaling-compute) till en högre DWU-inställning allokerar mer TempDB utrymme.|
| Dåliga frågeprestanda och planer är ofta ett resultat av saknad statistik | Den vanligaste orsaken till dåliga resultat är brist på statistik i tabellerna.  Mer information om hur du skapar statistik och varför de är viktiga för dina resultat finns i [Underhålla tabellstatistik.](sql-data-warehouse-tables-statistics.md) |
| Låg samtidighet / frågor i kö                             | Att förstå [arbetsbelastningshantering](resource-classes-for-workload-management.md) är viktigt för att förstå hur du balanserar minnesallokering med samtidighet. |
| Så här implementerar du bästa praxis                              | Det bästa stället att börja lära sig sätt att förbättra frågeprestanda är [Synapse SQL pool bästa praxis](sql-data-warehouse-best-practices.md) artikel. |
| Så här förbättrar du prestanda med skalning                      | Ibland lösningen för att förbättra prestanda är att helt enkelt lägga till mer beräkningskraft till dina frågor genom [att skala din SQL-pool](sql-data-warehouse-manage-compute-overview.md). |
| Dåliga frågeprestanda till följd av dålig indexkvalitet     | Vissa gånger frågor kan sakta ner på grund av [Dålig columnstore indexkvalitet](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Mer information finns i den här artikeln om du vill ha fler information om och hur du [återskapar index för att förbättra segmentkvaliteten](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Systemhantering

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Det gick inte att utföra åtgärden eftersom servern skulle överskrida den tillåtna kvoten databastransaktionsenhet på 45000. | Antingen minska [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) för den databas som du försöker skapa eller [begära en kvotökning](sql-data-warehouse-get-started-create-support-ticket.md). |
| Undersöka utrymmesutnyttjande                              | Se [Tabellstorlekar](sql-data-warehouse-tables-overview.md#table-size-queries) för att förstå systemets utrymmesanvändning. |
| Hjälp med att hantera tabeller                                    | Mer information om hur du hanterar tabellerna finns i översiktsartikeln i [tabell.](sql-data-warehouse-tables-overview.md)  Den här artikeln innehåller också länkar till mer detaljerade ämnen som [Tabelldatatyper](sql-data-warehouse-tables-data-types.md), [Distribuera en tabell](sql-data-warehouse-tables-distribute.md), [Indexera en tabell,](sql-data-warehouse-tables-index.md) [Partitionera en tabell,](sql-data-warehouse-tables-partition.md) [Underhålla tabellstatistik](sql-data-warehouse-tables-statistics.md) och [Temporära tabeller](sql-data-warehouse-tables-temporary.md). |
| Förloppsindikatorn för transparent datakryptering (TDE) uppdateras inte i Azure-portalen | Du kan visa tillståndet för TDE via [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |

## <a name="differences-from-sql-database"></a>Skillnader från SQL Database

| Problem                                 | Lösning                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Funktioner i SQL Database som inte stöds     | Se [Tabellfunktioner som inte stöds](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Datatyper som inte stöds av SQL Database   | Se [Datatyper som inte stöds](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Begränsningar för ta bort och uppdatera         | Se [UPDATE-lösningar,](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements) [TA bort lösningar](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) och Använda [CTAS för att komma runt syntaxen UPDATE och DELETE som inte stöds](sql-data-warehouse-develop-ctas.md). |
| MERGE-satsen stöds inte      | Se [SAMMANFOGA lösningar](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Begränsningar för lagrade procedurer          | Se [Begränsningar för lagrade procedurer](sql-data-warehouse-develop-stored-procedures.md#limitations) för att förstå några av begränsningarna i lagrade procedurer. |
| UDF stöder inte SELECT-satser | Detta är en nuvarande begränsning av våra UDF.  Se [SKAPA FUNKTION](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) för den syntax vi stöder. |

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer hjälp med att hitta lösningen på problemet kan du prova några andra resurser.

* [Bloggar](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Funktionsbegäranden](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videor](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md)
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
