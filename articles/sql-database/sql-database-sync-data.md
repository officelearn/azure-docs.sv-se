---
title: Azure SQL Data Sync | Microsoft Docs
description: Den här översikten introducerar Azure SQL Data Sync
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: data-sync
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: xiwu
ms.reviewer: douglasl
ms.openlocfilehash: ab5d042517716ee2b155d99803f4058772622036
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721457"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Synkronisera data i flera moln och lokala databaser med SQL Data Sync

SQL Data Sync är en tjänst som bygger på Azure SQL Database som gör att du synkroniserar data som du väljer riktningarna över flera SQL-databaser och SQL Server-instanser.

## <a name="architecture-of-sql-data-sync"></a>Arkitekturen för SQL Data Sync

Datasynkronisering baseras kring begreppet en Synkroniseringsgrupp. En Synkroniseringsgrupp är en grupp med databaser som du vill synkronisera.

Datasynkronisering använder en topologi med nav och ekrar för att synkronisera data. Du definierar en av databaserna i synkroniseringsgruppen som Hubbdatabasen. Resten av databaserna är databaser som medlem. Synkronisera sker bara mellan Hub och enskilda medlemmarna.
-   Den **Hubbdatabasen** måste vara en Azure SQL Database.
-   Den **medlem databaser** kan vara antingen SQL-databaser, en lokal SQL Server-databaser eller SQL Server-instanser på Azure virtual machines.
-   Den **Synkroniseringsdatabasen** innehåller metadata och loggfiler för datasynkronisering. Sync-databasen måste vara en Azure SQL Database finns i samma region som Hubbdatabasen. Sync-databasen är kund som har skapats och kundägda.

> [!NOTE]
> Om du använder en på plats-databas som en medlemsdatabas kan du behöva [installera och konfigurera en lokal synkroniseringsagenten](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synkronisera data mellan databaser](media/sql-database-sync-data/sync-data-overview.png)

En Synkroniseringsgrupp har följande egenskaper:

-   Den **synkroniseringsschemat** beskriver vilka data synkroniseras.

-   Den **synkroniseringsriktningen** kan vara dubbelriktad eller kan flöda i endast en riktning. Det vill säga synkroniseringsriktningen kan vara *Hub till medlemmen*, eller *medlem till hubben*, eller båda.

-   Den **synkroniseringsintervall** beskriver hur ofta synkronisering sker.

-   Den **principen för konfliktlösning** är en säkerhetsnivå för gruppen, som kan vara *Hub wins* eller *medlem wins*.

## <a name="when-to-use-data-sync"></a>När du ska använda datasynkronisering

Datasynkronisering är användbart i fall där data ska hållas uppdaterad över flera Azure SQL-databaser eller SQL Server-databaser. Här är huvudsakliga användningsområden för Data Sync:

-   **Hybrid datasynkronisering:** med datasynkronisering, kan du synkronisera data mellan dina lokala databaser och Azure SQL-databaser för att möjliggöra hybridprogram. Den här funktionen kan överklaga till kunder som överväger att flytta till molnet och vill placera några av sina program i Azure.

-   **Distribuerade program:** i många fall är det bra att separera olika arbetsbelastningar på olika databaser. Till exempel om du har en stor produktionsdatabas, men du måste också köra en arbetsbelastning för rapportering eller analyser på dessa data, är det bra att ha en andra databas för den här ytterligare arbetsbelastning. Denna metod minimerar prestandaförsämring på dina produktionsarbetsbelastningar. Du kan använda Data Sync för att hålla dessa två databaser synkroniseras.

-   **Globalt distribuerade program:** många företag sträcker sig över flera regioner och även flera länder. För att minimera Nätverksfördröjningen, är det bäst att ha dina data i en region nära dig. Du kan enkelt behålla databaser i regioner runtom i världen som synkroniseras med Data Sync.

Datasynkronisering är inte det en bättre lösningen för följande scenarier:

| Scenario | Några av de rekommenderade lösningar |
|----------|----------------------------|
| Haveriberedskap | [Azure geo-redundanta säkerhetskopieringar](sql-database-automated-backups.md) |
| Läsa skala | [Använd skrivskyddade repliker för att läsa in balansera skrivskyddad frågearbetsbelastningar (förhandsversion)](sql-database-read-scale-out.md) |
| ETL (OLTP till OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) eller [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services?view=sql-server-2017) |
| Migrering från en lokal SQLServer till Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-does-data-sync-work"></a>Hur fungerar Data Sync? 

-   **Spåra dataändringar:** datasynkronisering spårar ändringar med infoga, uppdatera och ta bort utlösare. Ändringarna sparas i en separat tabell i databasen.

-   **Synkronisera data:** datasynkronisering har utformats i en modell med nav och ekrar. Hubben synkroniserar individuellt med varje medlem. Ändringar från hubben laddas ned till medlemmen och sedan överförs ändringar från medlemmen till hubben.

-   **Lösa konflikter:** datasynkronisering innehåller två alternativ för konfliktlösning, *Hub wins* eller *medlem wins*.
    -   Om du väljer *Hub wins*, ändringar i navet alltid över ändringar i medlemmen.
    -   Om du väljer *medlem wins*, ändringar i medlem Skriv över ändringar i hubben. Om det finns mer än en medlem, beror det slutliga värdet på vilka medlem synkroniseras först.

## <a name="get-started-with-sql-data-sync"></a>Kom igång med SQL-datasynkronisering

### <a name="set-up-data-sync-in-the-azure-portal"></a>Konfigurera datasynkronisering i Azure portal

-   [Konfigurera Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)

### <a name="set-up-data-sync-with-powershell"></a>Konfigurera datasynkronisering med PowerShell

-   [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)

-   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Läs igenom metodtipsen för datasynkronisering

-   [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Något går fel?

-   [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Konsekvens och prestanda

#### <a name="eventual-consistency"></a>Slutlig konsekvens
Eftersom Data Sync är utlösningsbaserade kan garanteras inte transaktionell konsekvens. Microsoft garanterar att alla ändringar som görs till slut och att synkronisering inte leder till förlust av data.

#### <a name="performance-impact"></a>Prestandapåverkan
Data Sync använder Infoga, uppdatera och ta bort utlösare för att spåra ändringar. Tabeller sida skapas i databasen för ändringsspårning. Dessa aktiviteter för spårning av ändringen påverka på din databas-arbetsbelastning. Utvärdera din tjänstenivå och uppgradera om det behövs.

Etablering och borttagning under synkroniseringsgruppen, uppdatering och borttagning kan också påverka databasens prestanda. 

## <a name="sync-req-lim"></a> Krav och begränsningar

### <a name="general-requirements"></a>Allmänna krav

-   Varje tabell måste ha en primärnyckel. Ändra inte värdet för den primära nyckeln i en rad. Om du måste ändra värdet för en primärnyckel, ta bort rad och återskapa den med det nya värdet för primär nyckel. 

-   Ögonblicksbildisolering måste aktiveras. Mer information finns i [ögonblicksbildisolering i SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Allmänna begränsningar

-   En tabell kan inte ha en identity-kolumn som inte är den primära nyckeln.

-   En primär nyckel får inte ha följande datatyper: sql_variant, binary, varbinary, bild, xml. 

-   Var försiktig när du använder följande datatyper som en primär nyckel eftersom stöds precisionen är endast till andra: tid, datetime, datetime2, datetimeoffset.

-   Namnen på objekten (databaser, tabeller och kolumner) kan inte innehålla utskrivbara tecken punkt (.), vänster hakparentes ([) eller fyrkantiga höger hakparentes (]).

-   Azure Active Directory-autentisering stöds inte.

#### <a name="unsupported-data-types"></a>Datatyper

-   FileStream

-   SQL/CLR UDT

-   XMLSchemaCollection (XML stöds)

-   Markören RowVersion, tidsstämpel, Hierarchyid

#### <a name="unsupported-column-types"></a>Stöds inte kolumntyper

Datasynkronisering kan inte synkronisera skrivskyddad eller systemgenererade kolumner. Exempel:

-   Beräknade kolumner.

-   Systemgenererade kolumner för temporala tabeller.

#### <a name="limitations-on-service-and-database-dimensions"></a>Begränsningar för tjänsten och databasen dimensioner

| **Dimensioner**                                                      | **Gränsen**              | **Lösning**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximala antalet synkroniseringsgrupper alla databaser kan höra till.       | 5                      |                             |
| Maximalt antal slutpunkter i en enda synkroniseringsgrupp              | 30                     | Skapa flera synkroniseringsgrupper |
| Maximalt antal lokala slutpunkter i en enda synkroniseringsgrupp. | 5                      | Skapa flera synkroniseringsgrupper |
| Databas-, tabell-, schema-och kolumnnamn                       | 50 tecken per namn |                             |
| Tabeller i en synkroniseringsgrupp                                          | 500                    | Skapa flera synkroniseringsgrupper |
| Kolumner i en tabell i en synkroniseringsgrupp                              | 1000                   |                             |
| Data Radstorleken på en tabell                                        | 24 mb                  |                             |
| Minsta intervall                                           | 5 minuter              |                             |
|||

## <a name="faq-about-sql-data-sync"></a>Vanliga frågor och svar om SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Hur mycket kostar SQL Data Sync-tjänsten?

Det finns ingen avgift för själva SQL Data Sync-tjänsten.  Men påförs du fortfarande kostnader för dataöverföring för dataförflyttning och från SQL Database-instansen. Mer information finns i [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Vilka regioner har stöd för Data Sync?

SQL Data Sync är tillgänglig i alla regioner.

### <a name="is-a-sql-database-account-required"></a>Är ett konto för SQL-databas som krävs? 

Ja. Du måste ha ett konto för SQL-databas som värd för Hubbdatabasen.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Kan jag använda Data Sync för att synkronisera mellan SQL Server lokala databaser? 
Inte direkt. Du kan synkronisera mellan en lokal SQL Server-databaser indirekt, men genom att skapa en hubb-databasen i Azure och sedan lägga till lokala databaser i synkroniseringsgruppen.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Kan jag använda Data Sync för att synkronisera mellan SQL-databaser som tillhör olika prenumerationer?
Ja. Du kan synkronisera mellan SQL-databaser som hör till resursgrupper som ägs av olika prenumerationer.
-   Om prenumerationerna som tillhör samma klientorganisation och du har behörighet till alla prenumerationer, kan du konfigurera synkroniseringsgruppen i Azure-portalen.
-   Annars kan behöva du använda PowerShell för att lägga till synkroniseringsmedlemmar som tillhör olika prenumerationer.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>Kan jag använda Data Sync för att synkronisera mellan SQL-databaser som tillhör olika moln (som Azures offentliga moln och Azure Kina)?
Ja. Du kan synkronisera mellan SQL-databaser som tillhör olika moln måste du använda PowerShell för att lägga till synkroniseringsmedlemmar som tillhör olika prenumerationer.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kan jag använda datasynkronisering att fördefiniera data från min produktionsdatabas till en tom databas och sedan synkronisera dem?

Ja. Skapa schemat manuellt i den nya databasen med hjälp av skript från ursprungligt. När du skapar schemat kan du lägga till tabeller till en synkroniseringsgrupp för att kopiera data och hålla den synkroniserad.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Bör jag använda SQL Data Sync för att säkerhetskopiera och återställa Mina databaser?

Du bör inte använda SQL Data Sync för att skapa en säkerhetskopia av dina data. Du kan inte säkerhetskopiera och återställa till en specifik tidpunkt eftersom SQL Data Sync synkroniseringar inte är en ny version. Dessutom kan SQL Data Sync säkerhetskopieras inte andra SQL-objekt, till exempel lagrade procedurer och göra inte motsvarigheten till en återställningsåtgärd snabbt.

En rekommenderad säkerhetskopiering tekniken finns i [kopiera en Azure SQL database](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Datasynkronisering kan synkronisera krypterade tabeller och kolumner?

-   Om en databas använder Always Encrypted kan du synkronisera de tabeller och kolumner som är *inte* krypterade. Du kan inte synkronisera krypterade kolumner eftersom datasynkronisering inte kan dekryptera data.

-   Om en kolumn använder på kolumnnivå kryptering (CLE), kan du synkronisera kolumnen så länge Radstorleken är mindre än den maximala storleken för 24 Mb. Datasynkronisering behandlar den kolumn som krypterats av nyckel (CLE) som normala binära data. Du måste ha samma certifikat för att dekryptera data på andra synkroniseringsmedlemmar.

### <a name="is-collation-supported-in-sql-data-sync"></a>Har stöd för sorteringen i SQL Data Sync?

Ja. SQL Data Sync har stöd för sorteringen i följande scenarier:

-   Om tabellerna valda sync-schemat inte är redan i din hubb eller medlem databaser och sedan när du distribuerar synkroniseringsgruppen, skapas automatiskt av tjänsten motsvarande tabeller och kolumner med inställningarna för sortering som valts i tomt mål-databaser.

-   Om tabeller att synkronisera redan finns i både din hubb och medlemmen databaser, kräver SQL Data Sync att primärnyckelkolumnerna har samma sortering mellan hub och medlemmen databaser ska kunna distribuera synkroniseringsgruppen. Det finns inga begränsningar för sortering på kolumner utom primärnyckelkolumnerna.

### <a name="is-federation-supported-in-sql-data-sync"></a>Stöds federation i SQL Data Sync?

Federationsrotdatabas kan användas i SQL Data Sync-tjänsten utan någon begränsning. Du kan inte lägga till federerad databas-slutpunkt i den aktuella versionen av SQL Data Sync.

## <a name="next-steps"></a>Nästa steg

### <a name="update-the-schema-of-a-synced-database"></a>Uppdatera schemat för en synkroniserad databas

Behöver du uppdatera schemat för en databas i en synkroniseringsgrupp? Schemaändringar replikeras inte automatiskt. Vissa lösningar finns i följande artiklar:

-   [Automatisera replikeringen av schemaändringar i Azure SQL Data Sync](sql-database-update-sync-schema.md)

-   [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Övervaka och felsök

SQL Data Sync som fungerar som förväntat? Om du vill övervaka och felsöka problem, finns i följande artiklar:

-   [Övervaka Azure SQL Data Sync med Log Analytics](sql-database-sync-monitor-oms.md)

-   [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Läs mer om Azure SQL Database

Mer information om SQL Database finns i följande artiklar:

-   [Översikt över SQL Database](sql-database-technical-overview.md)

-   [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
