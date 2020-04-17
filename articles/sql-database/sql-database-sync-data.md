---
title: Datasynkronisering
description: Den här översikten introducerar Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 8708c458e1064e4b9ea7dc67f1a4d4fbce1547b0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481951"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Synkronisera data mellan flera molndatabaser och lokala databaser med SQL Data Sync

SQL Data Sync är en tjänst som bygger på Azure SQL Database och som gör att du kan synkronisera de data du väljer biriktad över flera SQL-databaser och SQL Server-instanser.

> [!IMPORTANT]
> Azure SQL Data Sync har inte stöd för Azure SQL Database Managed Instance just nu.

## <a name="when-to-use-data-sync"></a>När ska datasynkronisering användas

Datasynkronisering är användbart i fall där data måste hållas uppdaterade i flera Azure SQL-databaser eller SQL Server-databaser. Här är de viktigaste användningsfallen för Data sync:

- **Hybriddatasynkronisering:** Med Data Sync kan du synkronisera data mellan lokala databaser och Azure SQL-databaser för att aktivera hybridprogram. Den här funktionen kan tilltala kunder som funderar på att flytta till molnet och vill placera en del av sitt program i Azure.
- **Distribuerade program:** I många fall är det fördelaktigt att separera olika arbetsbelastningar mellan olika databaser. Om du till exempel har en stor produktionsdatabas, men du också måste köra en rapporterings- eller analysarbetsbelastning på dessa data, är det bra att ha en andra databas för den här extra arbetsbelastningen. Den här metoden minimerar prestandapåverkan på din produktionsarbetsbelastning. Du kan använda Datasynkronisering för att synkronisera dessa två databaser.
- **Globalt distribuerade program:** Många företag spänner över flera regioner och till och med flera länder/regioner. För att minimera nätverksfördröjningen är det bäst att ha data i en region nära dig. Med Data Sync kan du enkelt synkronisera databaser i regioner runt om i världen.

Datasynkronisering är inte den lösning som föredras för följande scenarier:

| Scenario | Några rekommenderade lösningar |
|----------|----------------------------|
| Haveriberedskap | [Geosuperundanta säkerhetskopior i Azure](sql-database-automated-backups.md) |
| Läs skala | [Använda skrivskyddade repliker för att läsa upp skrivskyddade frågearbetsbelastningar (förhandsgranskning)](sql-database-read-scale-out.md) |
| ETL (OLTP till OLAP) | [Azure Data Factory-](https://azure.microsoft.com/services/data-factory/) eller [SQL Server-integrationstjänster](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migrering från lokal SQL Server till Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Översikt över SQL Data Sync

Data Sync bygger på konceptet med en synkroniseringsgrupp. En synkroniseringsgrupp är en grupp databaser som du vill synkronisera.

Data Sync använder en hubb och ekertopologi för att synkronisera data. Du definierar en av databaserna i synkroniseringsgruppen som Hub-databasen. Resten av databaserna är medlemsdatabaser. Synkronisering sker endast mellan hubben och enskilda medlemmar.

- **Hub-databasen** måste vara en Azure SQL-databas.
- **Medlemsdatabaserna** kan vara antingen SQL-databaser, lokala SQL Server-databaser eller SQL Server-instanser på virtuella Azure-datorer.
- **Synkroniseringsdatabasen** innehåller metadata och logg för datasynkronisering. Synkroniseringsdatabasen måste vara en Azure SQL-databas som finns i samma region som Hub-databasen. Synkroniseringsdatabasen är kundskapade och kundägda.

> [!NOTE]
> Om du använder en lokal databas som medlemsdatabas måste du [installera och konfigurera en lokal synkroniseringsagent](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synkronisera data mellan databaser](media/sql-database-sync-data/sync-data-overview.png)

En synkroniseringsgrupp har följande egenskaper:

- **Synkroniseringsschemat** beskriver vilka data som synkroniseras.
- **Synkroniseringsriktningen** kan vara dubbelriktad eller kan flöda i endast en riktning. Det vill än om synkroniseringsriktningen kan vara *Hub to Member*, eller Medlem till *Hub*, eller båda.
- **Synkroniseringsintervallet** beskriver hur ofta synkroniseringen sker.
- **Konfliktlösningsprincipen** är en gruppnivåprincip som kan vara *Hub-vinster* eller *Medlemsvinster*.

## <a name="how-does-data-sync-work"></a>Hur fungerar Data Sync

- **Spåra dataändringar:** Datasynkronisering spårar ändringar med hjälp av infognings-, uppdaterings- och borttagningsutlösare. Ändringarna registreras i en sidotabell i användardatabasen. Observera att BULK INSERT inte avfyrar utlösare som standard. Om FIRE_TRIGGERS inte anges körs inga infogningsutlösare. Lägg till alternativet FIRE_TRIGGERS så att Data Sync kan spåra dessa infogningar. 
- **Synkronisera data:** Data Sync är utformad i en Hub och Spoke-modell. Hubben synkroniseras med varje medlem individuellt. Ändringar från hubben hämtas till medlemmen och sedan överförs ändringar från medlemmen till hubben.
- **Lösa konflikter:** Data Sync ger två alternativ för konfliktlösning, *Hub wins* eller *Member wins*.
  - Om du väljer *Hub wins*skrivs ändringarna i navet alltid över ändringar i medlemmen.
  - Om du väljer *Medlem vinner*skrivs ändringarna i medlemmen över i navet. Om det finns fler än en medlem beror det slutliga värdet på vilken medlem som synkroniseras först.

## <a name="compare-data-sync-with-transactional-replication"></a>Jämföra datasynkronisering med transaktionsreplikering

| | Datasynkronisering | Transaktionsreplikering |
|---|---|---|
| Fördelar | - Aktivt stöd<br/>- Dubbelriktad mellan lokala och Azure SQL Database | - Lägre latens<br/>- Transaktionell konsekvens<br/>- Återanvänd befintlig topologi efter migreringen |
| Nackdelar | - 5 min eller mer latens<br/>- Ingen transaktionell konsekvens<br/>- Högre prestandapåverkan | - Det går inte att publicera från En enda databas eller poolad databas från Azure SQL Database<br/>- Höga underhållskostnader |

## <a name="get-started-with-sql-data-sync"></a>Komma igång med SQL Data Sync

### <a name="set-up-data-sync-in-the-azure-portal"></a>Konfigurera datasynkronisering i Azure-portalen

- [Konfigurera Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)
- Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Konfigurera datasynkronisering med PowerShell

- [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
- [Använd PowerShell för att synkronisera mellan en Azure SQL-databas och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Granska metodtipsen för datasynkronisering

- [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Gick något fel

- [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Konsekvens och prestanda

### <a name="eventual-consistency"></a>Slutlig konsekvens

Eftersom Data Sync är utlösande baseras transaktionskonsekvensen inte. Microsoft garanterar att alla ändringar görs så småningom och att Data Sync inte orsakar dataförlust.

### <a name="performance-impact"></a>Prestandapåverkan

Datasynkronisering använder infognings-, uppdaterings- och borttagningsutlösare för att spåra ändringar. Det skapar sidotabeller i användardatabasen för ändringsspårning. Dessa ändringsspårningsaktiviteter påverkar din databasarbetsbelastning. Utvärdera din tjänstnivå och uppgradera om det behövs.

Etablering och avetablering under skapande, uppdatering och borttagning av synkroniseringsgrupper kan också påverka databasens prestanda.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a>Krav och begränsningar

### <a name="general-requirements"></a>Allmänna krav

- Varje tabell måste ha en primärnyckel. Ändra inte värdet på primärnyckeln på någon rad. Om du måste ändra ett primärnyckelvärde tar du bort raden och återskapar den med det nya primärnyckelvärdet.

> [!IMPORTANT]
> Om du ändrar värdet för en befintlig primärnyckel uppstår följande felaktiga beteende:
> - Data mellan hubb och medlem kan gå förlorade även om synkroniseringen inte rapporterar något problem.
> - Synkronisering kan misslyckas eftersom spårningstabellen har en icke-befintlig rad från källan på grund av den primära nyckeländringen.

- Ögonblicksbildisolering måste vara aktiverat. Mer information finns i [Ögonblicksbildisolering i SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Allmänna begränsningar

- En tabell kan inte ha en identitetskolumn som inte är primärnyckeln.
- En primärnyckel kan inte ha följande datatyper: sql_variant, binär, varbinary, image, xml.
- Var försiktig när du använder följande datatyper som primärnyckel, eftersom precisionen som stöds bara är den andra: tid, datumtid, datetime2, datetimeoffset.
- Namnen på objekt (databaser, tabeller och kolumner) får inte innehålla den utskrivbara teckenperioden (.), vänster hakparentes ([) eller höger hakparentes (]).
- Azure Active Directory-autentisering stöds inte.
- Tabeller med samma namn men olika schema (till exempel dbo.customers och sales.customers) stöds inte.
- Kolumner med användardefinierade datatyper stöds inte
- Det går inte att flytta servrar mellan olika prenumerationer. 

#### <a name="unsupported-data-types"></a>Datatyper som inte stöds

- Filestream
- SQL/CLR UDT
- XMLSchemaCollection (XML stöds)
- Markör, RowVersion, Tidsstämpel, Hierarchyid

#### <a name="unsupported-column-types"></a>Kolumntyper som inte stöds

Datasynkronisering kan inte synkronisera skrivskyddade eller systemgenererade kolumner. Ett exempel:

- Beräknade kolumner.
- Systemgenererade kolumner för temporala tabeller.

#### <a name="limitations-on-service-and-database-dimensions"></a>Begränsningar av service- och databasdimensioner

| **Dimensioner**                                                  | **Gräns**              | **Workaround**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximalt antal synkroniseringsgrupper som en databas kan tillhöra.       | 5                      |                             |
| Maximalt antal slutpunkter i en enskild synkroniseringsgrupp              | 30                     |                             |
| Maximalt antal lokala slutpunkter i en enda synkroniseringsgrupp. | 5                      | Skapa flera synkroniseringsgrupper |
| Databas-, tabell-, schema- och kolumnnamn                       | 50 tecken per namn |                             |
| Tabeller i en synkroniseringsgrupp                                          | 500                    | Skapa flera synkroniseringsgrupper |
| Kolumner i en tabell i en synkroniseringsgrupp                              | 1000                   |                             |
| Radstorlek för data i en tabell                                        | 24 Mb                  |                             |
| Minsta synkroniseringsintervall                                           | 5 minuter              |                             |

> [!NOTE]
> Det kan finnas upp till 30 slutpunkter i en enda synkroniseringsgrupp om det bara finns en synkroniseringsgrupp. Om det finns mer än en synkroniseringsgrupp får det totala antalet slutpunkter i alla synkroniseringsgrupper inte överstiga 30. Om en databas tillhör flera synkroniseringsgrupper räknas den som flera slutpunkter, inte en.

## <a name="faq-about-sql-data-sync"></a>Vanliga frågor och svar om SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Hur mycket kostar SQL Data Sync-tjänsten

Det finns ingen avgift för själva SQL Data Sync-tjänsten. Du samlar dock fortfarande in dataöverföringsavgifter för dataflyttning in och ut ur SQL Database-instansen. Mer information finns i [SQL Database-priser](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Vilka regioner har stöd för Data Sync

SQL Data Sync är tillgängligt i alla regioner.

### <a name="is-a-sql-database-account-required"></a>Krävs ett SQL Database-konto

Ja. Du måste ha ett SQL Database-konto för att vara värd för Hub-databasen.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Kan jag använda Data Sync för att synkronisera mellan lokala SQL Server-databaser

Inte direkt. Du kan synkronisera mellan lokala SQL Server-databaser indirekt genom att skapa en Hub-databas i Azure och sedan lägga till lokala databaser i synkroniseringsgruppen.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Kan jag använda Data sync för att synkronisera mellan SQL-databaser som tillhör olika prenumerationer

Ja. Du kan synkronisera mellan SQL-databaser som tillhör resursgrupper som ägs av olika prenumerationer.

- Om prenumerationerna tillhör samma klient och du har behörighet till alla prenumerationer kan du konfigurera synkroniseringsgruppen i Azure-portalen.
- Annars måste du använda PowerShell för att lägga till synkroniseringsmedlemmar som tillhör olika prenumerationer.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Kan jag använda Data Sync för att synkronisera mellan SQL-databaser som tillhör olika moln (som Azure Public Cloud och Azure China 21Vianet)

Ja. Du kan synkronisera mellan SQL-databaser som tillhör olika moln, du måste använda PowerShell för att lägga till synkroniseringsmedlemmar som tillhör de olika prenumerationerna.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kan jag använda Data Sync för att dirigera data från min produktionsdatabas till en tom databas och sedan synkronisera dem

Ja. Skapa schemat manuellt i den nya databasen genom att skripta det från originalet. När du har skapat schemat lägger du till tabellerna i en synkroniseringsgrupp för att kopiera data och synkronisera dem.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Ska jag använda SQL Data Sync för att säkerhetskopiera och återställa mina databaser

Det rekommenderas inte att använda SQL Data Sync för att skapa en säkerhetskopia av dina data. Du kan inte säkerhetskopiera och återställa till en viss tidpunkt eftersom SQL Data Sync-synkronisering inte är version. Sql Data Sync säkerhetskopierar inte heller andra SQL-objekt, till exempel lagrade procedurer, och gör inte motsvarande en återställningsåtgärd snabbt.

En rekommenderad säkerhetskopieringsteknik finns i [Kopiera en Azure SQL-databas](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kan Data sync krypterade tabeller och kolumner

- Om en databas använder Alltid krypterad kan du bara synkronisera de tabeller och kolumner som *inte* är krypterade. Du kan inte synkronisera de krypterade kolumnerna eftersom Data Sync inte kan dekryptera data.
- Om en kolumn använder CLE (Column-Level Encryption) kan du synkronisera kolumnen så länge radstorleken är mindre än den maximala storleken på 24 Mb. Data Sync behandlar kolumnen krypterad med nyckel (CLE) som normala binära data. Om du vill dekryptera data på andra synkroniseringsmedlemmar måste du ha samma certifikat.

### <a name="is-collation-supported-in-sql-data-sync"></a>Stöds sortering i SQL Data Sync

Ja. SQL Data Sync stöder sortering i följande scenarier:

- Om de valda synkroniseringsschematabellerna inte redan finns i hubb- eller medlemsdatabaserna, skapar tjänsten automatiskt motsvarande tabeller och kolumner när du distribuerar synkroniseringsgruppen automatiskt motsvarande tabeller och kolumner med sorteringsinställningarna markerade i de tomma måldatabaserna.
- Om tabellerna som ska synkroniseras redan finns i både hubb- och medlemsdatabaserna kräver SQL Data Sync att primärnyckelkolumnerna har samma sortering mellan nav- och medlemsdatabaser för att distribuera synkroniseringsgruppen. Det finns inga sorteringsbegränsningar för andra kolumner än primärnyckelkolumnerna.

### <a name="is-federation-supported-in-sql-data-sync"></a>Stöds federation i SQL Data Sync

Federation Root Database kan användas i SQL Data Sync Service utan begränsning. Du kan inte lägga till slutpunkten federerad databas i den aktuella versionen av SQL Data Sync.

## <a name="next-steps"></a>Nästa steg

### <a name="update-the-schema-of-a-synced-database"></a>Uppdatera schemat för en synkroniserad databas

Måste du uppdatera schemat för en databas i en synkroniseringsgrupp? Schemaändringar replikeras inte automatiskt. Några lösningar finns i följande artiklar:

- [Automatisera replikering av schemaändringar i Azure SQL Data Sync](sql-database-update-sync-schema.md)
- [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Övervaka och felsök

Går SQL Data Sync som förväntat? Information om hur du övervakar problem med aktivitet och felsöker finns i följande artiklar:

- [Övervaka Azure SQL Data Sync med Azure Monitor-loggar](sql-database-sync-monitor-oms.md)
- [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Läs mer om Azure SQL Database

Mer information om SQL Database finns i följande artiklar:

- [Översikt över SQL Database](sql-database-technical-overview.md)
- [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
