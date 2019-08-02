---
title: Azure SQL Data Sync | Microsoft Docs
description: Den här översikten introducerar Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 24e340d25cb57f9a35f06f6dbd5a394d60a14fad
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566433"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Synkronisera data i flera moln och lokala databaser med SQL Data Sync

SQL Data Sync är en tjänst som bygger på Azure SQL Database som gör att du synkroniserar data som du väljer riktningarna över flera SQL-databaser och SQL Server-instanser.

> [!IMPORTANT]
> Azure SQL Data Sync har **inte** stöd för Azure SQL Database Managed Instance just nu.

## <a name="when-to-use-data-sync"></a>När du ska använda datasynkronisering

Datasynkronisering är användbart i fall där data ska hållas uppdaterad över flera Azure SQL-databaser eller SQL Server-databaser. Här är huvudsakliga användningsområden för Data Sync:

- **Synkronisering av hybrid data:** Med datasynkronisering kan du hålla data synkroniserade mellan dina lokala databaser och Azure SQL-databaser för att aktivera hybrid program. Den här funktionen kan överklaga till kunder som överväger att flytta till molnet och vill placera några av sina program i Azure.
- **Distribuerade program:** I många fall är det bra att separera olika arbets belastningar i olika databaser. Till exempel om du har en stor produktionsdatabas, men du måste också köra en arbetsbelastning för rapportering eller analyser på dessa data, är det bra att ha en andra databas för den här ytterligare arbetsbelastning. Denna metod minimerar prestandaförsämring på dina produktionsarbetsbelastningar. Du kan använda Data Sync för att hålla dessa två databaser synkroniseras.
- **Globalt distribuerade program:** Många företag sträcker sig över flera regioner och till och med flera länder/regioner. För att minimera Nätverksfördröjningen, är det bäst att ha dina data i en region nära dig. Du kan enkelt behålla databaser i regioner runtom i världen som synkroniseras med Data Sync.

Datasynkronisering är inte det en bättre lösningen för följande scenarier:

| Scenario | Några av de rekommenderade lösningar |
|----------|----------------------------|
| Haveriberedskap | [Azure geo-redundanta säkerhetskopieringar](sql-database-automated-backups.md) |
| Läsa skala | [Använd skrivskyddade repliker för att läsa in balansera skrivskyddad frågearbetsbelastningar (förhandsversion)](sql-database-read-scale-out.md) |
| ETL (OLTP till OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) eller [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migrering från en lokal SQLServer till Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Översikt över SQL Data Sync

Datasynkronisering baseras kring begreppet en Synkroniseringsgrupp. En Synkroniseringsgrupp är en grupp med databaser som du vill synkronisera.

Datasynkronisering använder en topologi med nav och ekrar för att synkronisera data. Du definierar en av databaserna i synkroniseringsgruppen som Hubbdatabasen. Resten av databaserna är databaser som medlem. Synkronisera sker bara mellan Hub och enskilda medlemmarna.

- Den **Hubbdatabasen** måste vara en Azure SQL Database.
- Den **medlem databaser** kan vara antingen SQL-databaser, en lokal SQL Server-databaser eller SQL Server-instanser på Azure virtual machines.
- Den **Synkroniseringsdatabasen** innehåller metadata och loggfiler för datasynkronisering. Sync-databasen måste vara en Azure SQL Database finns i samma region som Hubbdatabasen. Sync-databasen är kund som har skapats och kundägda.

> [!NOTE]
> Om du använder en på plats-databas som en medlemsdatabas kan du behöva [installera och konfigurera en lokal synkroniseringsagenten](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synkronisera data mellan databaser](media/sql-database-sync-data/sync-data-overview.png)

En Synkroniseringsgrupp har följande egenskaper:

- Den **synkroniseringsschemat** beskriver vilka data synkroniseras.
- Den **synkroniseringsriktningen** kan vara dubbelriktad eller kan flöda i endast en riktning. Det vill säga synkroniseringsriktningen kan vara *Hub till medlemmen*, eller *medlem till hubben*, eller båda.
- Den **synkroniseringsintervall** beskriver hur ofta synkronisering sker.
- Den **principen för konfliktlösning** är en säkerhetsnivå för gruppen, som kan vara *Hub wins* eller *medlem wins*.

## <a name="how-does-data-sync-work"></a>Hur fungerar synkronisering av data?

- **Spårar data ändringar:** Datasynkronisering spårar ändringar med hjälp av INSERT-, Update-och Delete-utlösare. Ändringarna sparas i en separat tabell i databasen. Observera att BULK INSERT inte aktiveras utlösare som standard. Om FIRE_TRIGGERS inte anges kör inga insert-utlösare. Lägga till alternativet FIRE_TRIGGERS så att Data Sync kan spåra dessa infogningar. 
- **Synkroniserar data:** Datasynkronisering är utformad i en nav-och eker-modell. Hubben synkroniserar individuellt med varje medlem. Ändringar från hubben laddas ned till medlemmen och sedan överförs ändringar från medlemmen till hubben.
- **Lösa konflikter:** Datasynkronisering innehåller två alternativ för konflikt lösning, *hubb-WINS* eller *medlems-WINS*.
  - Om du väljer *Hub wins*, ändringar i navet alltid över ändringar i medlemmen.
  - Om du väljer *medlem wins*, ändringar i medlem Skriv över ändringar i hubben. Om det finns mer än en medlem, beror det slutliga värdet på vilka medlem synkroniseras först.

## <a name="compare-data-sync-with-transactional-replication"></a>Jämför datasynkronisering med transaktionell replikering

| | Datasynkronisering | Transaktionsreplikering |
|---|---|---|
| Fördelar | – Stöd för aktiv-aktiv<br/>– Dubbelriktad mellan lokala och Azure SQL Database | -Nedre latens<br/>– Transaktionell konsekvens<br/>-Återanvänd befintlig topologi efter migrering |
| Nackdelar | – 5 min eller mer svars tid<br/>– Ingen transaktionell konsekvens<br/>-Högre prestanda påverkan | -Det går inte att publicera från Azure SQL Database enskild databas eller databas i pooler<br/>– Kostnad för hög underhåll |
| | | |

## <a name="get-started-with-sql-data-sync"></a>Kom igång med SQL-datasynkronisering

### <a name="set-up-data-sync-in-the-azure-portal"></a>Konfigurera datasynkronisering i Azure portal

- [Konfigurera Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)
- Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Konfigurera datasynkronisering med PowerShell

- [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
- [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Läs igenom metodtipsen för datasynkronisering

- [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Har något gå fel

- [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Konsekvens och prestanda

#### <a name="eventual-consistency"></a>Slutlig konsekvens

Eftersom Data Sync är utlösningsbaserade kan garanteras inte transaktionell konsekvens. Microsoft garanterar att alla ändringar som görs till slut och att synkronisering inte leder till förlust av data.

#### <a name="performance-impact"></a>Prestandapåverkan

Data Sync använder Infoga, uppdatera och ta bort utlösare för att spåra ändringar. Tabeller sida skapas i databasen för ändringsspårning. Dessa aktiviteter för spårning av ändringen påverka på din databas-arbetsbelastning. Utvärdera din tjänstenivå och uppgradera om det behövs.

Etablering och borttagning under synkroniseringsgruppen, uppdatering och borttagning kan också påverka databasens prestanda. 

## <a name="sync-req-lim"></a> Krav och begränsningar

### <a name="general-requirements"></a>Allmänna krav

- Varje tabell måste ha en primärnyckel. Ändra inte värdet för den primära nyckeln i en rad. Om du måste ändra värdet för en primärnyckel, ta bort rad och återskapa den med det nya värdet för primär nyckel. 
- Ögonblicksbildisolering måste aktiveras. Mer information finns i [ögonblicksbildisolering i SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Allmänna begränsningar

- En tabell kan inte ha en identity-kolumn som inte är den primära nyckeln.
- En primär nyckel får inte ha följande datatyper: sql_variant, binary, varbinary, bild, xml. 
- Var försiktig när du använder följande datatyper som en primär nyckel eftersom stöds precisionen är endast till andra: tid, datetime, datetime2, datetimeoffset.
- Namnen på objekten (databaser, tabeller och kolumner) kan inte innehålla utskrivbara tecken punkt (.), vänster hakparentes ([) eller fyrkantiga höger hakparentes (]).
- Azure Active Directory-autentisering stöds inte.
- Tabeller med samma namn men olika schemat (till exempel dbo.customers och sales.customers) stöds inte.
- Kolumner med användardefinierade data typer stöds inte

#### <a name="unsupported-data-types"></a>Datatyper

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (XML stöds)
- Markören RowVersion, tidsstämpel, Hierarchyid

#### <a name="unsupported-column-types"></a>Stöds inte kolumntyper

Datasynkronisering kan inte synkronisera skrivskyddad eller systemgenererade kolumner. Exempel:

- Beräknade kolumner.
- Systemgenererade kolumner för temporala tabeller.

#### <a name="limitations-on-service-and-database-dimensions"></a>Begränsningar för tjänsten och databasen dimensioner

| **Dimensioner**                                                      | **Gränsen**              | **Lösning**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximala antalet synkroniseringsgrupper alla databaser kan höra till.       | 5                      |                             |
| Maximalt antal slutpunkter i en enda synkroniseringsgrupp              | 30                     |                             |
| Maximalt antal lokala slutpunkter i en enda synkroniseringsgrupp. | 5                      | Skapa flera synkroniseringsgrupper |
| Databas-, tabell-, schema-och kolumnnamn                       | 50 tecken per namn |                             |
| Tabeller i en synkroniseringsgrupp                                          | 500                    | Skapa flera synkroniseringsgrupper |
| Kolumner i en tabell i en synkroniseringsgrupp                              | 1000                   |                             |
| Data Radstorleken på en tabell                                        | 24 mb                  |                             |
| Minsta intervall                                           | 5 minuter              |                             |
|||
> [!NOTE]
> Det kan vara upp till 30 slutpunkter i en enda synkroniseringsgrupp om det finns endast en synkroniseringsgrupp. Om det finns fler än en synkroniseringsgrupp, får inte det totala antalet slutpunkter över alla synkroniseringsgrupper överskrida 30. Om en databas tillhör flera synkroniseringsgrupper, räknas det som flera slutpunkter kan inte en.

## <a name="faq-about-sql-data-sync"></a>Vanliga frågor och svar om SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Hur mycket kostar SQL Data Sync tjänsten

Det finns ingen avgift för själva SQL Data Sync-tjänsten.  Men påförs du fortfarande kostnader för dataöverföring för dataförflyttning och från SQL Database-instansen. Mer information finns i [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Vilka regioner stöder datasynkronisering

SQL Data Sync är tillgänglig i alla regioner.

### <a name="is-a-sql-database-account-required"></a>Är ett SQL Database konto obligatoriskt

Ja. Du måste ha ett konto för SQL-databas som värd för Hubbdatabasen.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Kan jag endast använda datasynkronisering för att synkronisera mellan SQL Server lokala databaser

Inte direkt. Du kan synkronisera mellan en lokal SQL Server-databaser indirekt, men genom att skapa en hubb-databasen i Azure och sedan lägga till lokala databaser i synkroniseringsgruppen.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Kan jag använda Data Sync för att synkronisera mellan SQL-databaser som tillhör olika prenumerationer

Ja. Du kan synkronisera mellan SQL-databaser som hör till resursgrupper som ägs av olika prenumerationer.

- Om prenumerationerna som tillhör samma klientorganisation och du har behörighet till alla prenumerationer, kan du konfigurera synkroniseringsgruppen i Azure-portalen.
- Annars kan behöva du använda PowerShell för att lägga till synkroniseringsmedlemmar som tillhör olika prenumerationer.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>Kan jag använda datasynkronisering för att synkronisera mellan SQL-databaser som tillhör olika moln (till exempel Azures offentliga moln och Azure Kina)

Ja. Du kan synkronisera mellan SQL-databaser som tillhör olika moln måste du använda PowerShell för att lägga till synkroniseringsmedlemmar som tillhör olika prenumerationer.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kan jag använda datasynkronisering för att dirigera data från min produktions databas till en tom databas och sedan synkronisera dem

Ja. Skapa schemat manuellt i den nya databasen med hjälp av skript från ursprungligt. När du skapar schemat kan du lägga till tabeller till en synkroniseringsgrupp för att kopiera data och hålla den synkroniserad.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Bör jag använda SQL Data Sync för att säkerhetskopiera och återställa mina databaser

Du bör inte använda SQL Data Sync för att skapa en säkerhetskopia av dina data. Du kan inte säkerhetskopiera och återställa till en specifik tidpunkt eftersom SQL Data Sync synkroniseringar inte är en ny version. Dessutom kan SQL Data Sync säkerhetskopieras inte andra SQL-objekt, till exempel lagrade procedurer och göra inte motsvarigheten till en återställningsåtgärd snabbt.

En rekommenderad säkerhetskopiering tekniken finns i [kopiera en Azure SQL database](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kan synkronisera krypterade tabeller och kolumner med datasynkronisering

- Om en databas använder Always Encrypted kan du synkronisera de tabeller och kolumner som är *inte* krypterade. Du kan inte synkronisera krypterade kolumner eftersom datasynkronisering inte kan dekryptera data.
- Om en kolumn använder på kolumnnivå kryptering (CLE), kan du synkronisera kolumnen så länge Radstorleken är mindre än den maximala storleken för 24 Mb. Datasynkronisering behandlar den kolumn som krypterats av nyckel (CLE) som normala binära data. Du måste ha samma certifikat för att dekryptera data på andra synkroniseringsmedlemmar.

### <a name="is-collation-supported-in-sql-data-sync"></a>Stöds sortering i SQL Data Sync

Ja. SQL Data Sync har stöd för sorteringen i följande scenarier:

- Om tabellerna valda sync-schemat inte är redan i din hubb eller medlem databaser och sedan när du distribuerar synkroniseringsgruppen, skapas automatiskt av tjänsten motsvarande tabeller och kolumner med inställningarna för sortering som valts i tomt mål-databaser.
- Om tabeller att synkronisera redan finns i både din hubb och medlemmen databaser, kräver SQL Data Sync att primärnyckelkolumnerna har samma sortering mellan hub och medlemmen databaser ska kunna distribuera synkroniseringsgruppen. Det finns inga begränsningar för sortering på kolumner utom primärnyckelkolumnerna.

### <a name="is-federation-supported-in-sql-data-sync"></a>Stöds Federation i SQL Data Sync

Federationsrotdatabas kan användas i SQL Data Sync-tjänsten utan någon begränsning. Du kan inte lägga till federerad databas-slutpunkt i den aktuella versionen av SQL Data Sync.

## <a name="next-steps"></a>Nästa steg

### <a name="update-the-schema-of-a-synced-database"></a>Uppdatera schemat för en synkroniserad databas

Behöver du uppdatera schemat för en databas i en synkroniseringsgrupp? Schemaändringar replikeras inte automatiskt. Vissa lösningar finns i följande artiklar:

- [Automatisera replikeringen av schemaändringar i Azure SQL Data Sync](sql-database-update-sync-schema.md)
- [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Övervaka och felsök

SQL Data Sync som fungerar som förväntat? Om du vill övervaka och felsöka problem, finns i följande artiklar:

- [Övervaka Azure-SQL Data Sync med Azure Monitor loggar](sql-database-sync-monitor-oms.md)
- [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Läs mer om Azure SQL Database

Mer information om SQL Database finns i följande artiklar:

- [Översikt över SQL Database](sql-database-technical-overview.md)
- [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
