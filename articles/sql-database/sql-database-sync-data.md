---
title: "Azure SQL-datasynkronisering (förhandsversion) | Microsoft Docs"
description: "Den här översikten beskriver datasynkronisering för Azure SQL (förhandsgranskning)"
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 8bcecdff2bb9ac037e2cd71a431619883dfb5084
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync-preview"></a>Synkronisera data över flera molntjänster och lokala databaser med SQL-datasynkronisering (förhandsgranskning)

SQL-datasynkronisering är en tjänst som bygger på Azure SQL Database som gör att du kan synkronisera data som du väljer i båda riktningarna över flera SQL-databaser och SQL Server-instanser.

Datasynkronisering baseras kring begreppet en Sync-grupp. En synkronisering grupp är en grupp av databaser som du vill synkronisera.

En grupp för synkronisering har följande egenskaper:

-   Den **synkroniseringsschema** beskriver vilka data synkroniseras.

-   Den **Sync riktning** kan vara dubbelriktad eller kan flöda bara i en riktning. Det vill säga Sync-riktningen vara *hubb till medlem* eller *medlem till hubben*, eller båda.

-   Den **synkroniseringsintervall** är hur ofta synkronisering sker.

-   Den **konflikt upplösning princip** är en nivå Grupprincip, vilket kan vara *hubb wins* eller *medlem wins*.

Datasynkronisering använder en nav och eker-topologi för att synkronisera data. Du definierar en av databaserna i gruppen som NAV-databasen. Resten av databaserna är medlem databaser. Synkronisera sker bara mellan NAV- och enskilda medlemmarna.
-   Den **hubb databasen** måste vara en Azure SQL Database.
-   Den **medlem databaser** kan vara antingen SQL-databaser, lokal SQL Server-databaser eller SQL Server-instanser på virtuella Azure-datorer.
-   Den **Sync-databasen** innehåller metadata och loggfiler för datasynkronisering. Sync-databasen måste vara en Azure SQL-databas finns i samma region som NAV-databasen. Sync-databasen är kund har skapats och ägs av kunden.

> [!NOTE]
> Om du använder en på lokal databas måste du [konfigurera en lokal agent](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synkronisera data mellan databaser](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>När du ska använda datasynkronisering

Datasynkronisering är användbart i fall där data måste hållas uppdaterade över flera Azure SQL-databaser eller SQL Server-databaser. Här är de huvudsakliga användningsområden för datasynkronisering:

-   **Hybrid datasynkronisering:** med datasynkronisering, kan du synkronisera data mellan din lokala databaser och Azure SQL-databaser för att aktivera hybridprogram. Den här funktionen kan överklaga till kunder som överväger flytt till molnet och vill placera några av sina program i Azure.

-   **Distribuerade program:** i många fall är det bra att separera olika arbetsbelastningar över olika databaser. Till exempel om du har en stor produktionsdatabas, men du måste också köra en rapportering eller analytics arbetsbelastning på dessa data, är det praktiskt att ha en andra databas för den här ytterligare arbetsbelastning. Detta minimerar påverkan på prestandan för din arbetsbelastning i produktion. Du kan använda datasynkronisering att dessa två databaser som synkroniseras.

-   **Globalt distribuerade program:** många företag sträcker sig över flera regioner och även flera länder. För att minimera Nätverksfördröjningen, är det bäst att ha dina data i en region nära dig. Du kan enkelt behålla databaser i regioner runtom i världen som synkroniseras med synkronisering av Data.

Datasynkronisering är inte lämplig för följande scenarier:

-   Haveriberedskap

-   Läs skala

-   ETL (OLTP till OLAP)

-   Migrering från en lokal SQLServer till Azure SQL-databas

## <a name="how-does-data-sync-work"></a>Hur fungerar datasynkronisering? 

-   **Spåra dataändringar:** datasynkronisering spårar ändringar genom att använda Infoga, uppdatera och ta bort utlösare. Ändringarna som är registrerade i en sida tabell i databasen.

-   **Synkronisera data:** datasynkronisering har utformats i en nav och eker-modell. Hubben synkroniserar individuellt med varje medlem. Ändringar från navet hämtas till medlemmen och sedan ändringar från medlemmen överförs till hubben.

-   **Lösa konflikter:** datasynkronisering innehåller två alternativ för konfliktlösning, *hubb wins* eller *medlem wins*.
    -   Om du väljer *hubb wins*, ändringar i hubben alltid över ändringar i medlemmen.
    -   Om du väljer *medlem wins*, ändringar i medlemmen Skriv över ändringar i hubben. Om det finns mer än en medlem, beror det sista värdet på vilken medlem synkroniseras först.

## <a name="sync-req-lim"></a>Krav och begränsningar

### <a name="general-requirements"></a>Allmänna krav

-   Varje tabell måste ha en primärnyckel. Ändra inte värdet för den primära nyckeln i en rad. Om du behöver ändra värdet för en primärnyckel, ta bort raden och återskapa den med nya primärnyckelvärdet. 

-   En tabell kan inte ha en identitetskolumn som inte är den primära nyckeln.

-   Namnen på objekten (databaser, tabeller och kolumner) kan inte innehålla utskrivbara tecken punkt (.), vänster hakparentes ([) eller fyrkantiga höger hakparentes (]).

-   Ögonblicksbildisolering måste aktiveras. Mer information finns i [ögonblicksbildisolering i SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-considerations"></a>Allmänna överväganden

#### <a name="eventual-consistency"></a>Slutliga konsekvensen
Eftersom datasynkronisering utlösaren-baserade, garanteras inte transaktionskonsekvens. Microsoft garanterar att alla ändringar som görs till slut och att datasynkronisering inte leder till dataförlust.

#### <a name="performance-impact"></a>Inverkan på prestanda
Data Sync använder Infoga, uppdatera och ta bort utlösare för att spåra ändringar. Den skapar tabeller sida i databasen för ändringsspårning. Dessa aktiviteter för spårning av ändring kan påverka din arbetsbelastning i databasen. Utvärdera din tjänstnivå och uppgradera om det behövs.

### <a name="general-limitations"></a>Allmänna begränsningar

#### <a name="unsupported-data-types"></a>Datatyper

-   FileStream

-   SQL/CLR-UDT

-   XMLSchemaCollection (XML stöds)

-   Pekaren och tidsstämpel Hierarchyid

#### <a name="limitations-on-service-and-database-dimensions"></a>Begränsningar för tjänsten och databasen dimensioner

| **Dimensioner**                                                      | **Gränsen**              | **Lösning**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximalt antal synkroniseringsgrupper alla databaser kan tillhöra.       | 5                      |                             |
| Maximalt antal slutpunkter i en enda sync-grupp              | 30                     | Skapa flera synkroniseringsgrupper |
| Maximalt antal lokala slutpunkter i en enda sync-grupp. | 5                      | Skapa flera synkroniseringsgrupper |
| Namn på databasen, tabell, schemat och kolumn                       | 50 tecken per namn |                             |
| Tabeller i en grupp för synkronisering                                          | 500                    | Skapa flera synkroniseringsgrupper |
| Kolumner i en tabell i en grupp för synkronisering                              | 1000                   |                             |
| Datastorlek för rad i en tabell                                        | 24 mb                  |                             |
| Minsta synkroniseringsintervall                                           | 5 minuter              |                             |
|||

## <a name="faq-about-sql-data-sync"></a>Vanliga frågor om SQL-datasynkronisering

### <a name="how-much-does-the-sql-data-sync-preview-service-cost"></a>Hur mycket kostar tjänsten SQL-datasynkronisering (förhandsgranskning)

I förhandsversionen är gratis för tjänsten SQL-datasynkronisering (förhandsversion).  Men påförs du fortfarande data transfer kostnader för dataflytt till och från din SQL Database-instans. Mer information finns i [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Vilka regioner stöd för datasynkronisering?

SQL-datasynkronisering (förhandsversion) är tillgänglig i alla regioner för offentliga moln.

### <a name="is-a-sql-database-account-required"></a>Är ett konto för SQL-databas som krävs? 

Ja. Du måste ha ett konto för SQL-databas som värd för NAV-databasen.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Kan jag använda datasynkronisering ska synkroniseras mellan SQL Server lokala databaser? 
Inte direkt. Du kan synkronisera mellan lokala SQL Server-databaser indirekt, men genom att skapa en Hub-databas i Azure och sedan lägga till de lokala databaserna i gruppen synkronisering.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>Kan jag använda datasynkronisering att fördefiniera data från min produktionsdatabasen i en tom databas och hålla dem synkroniserade? 
Ja. Skapa schemat manuellt i den nya databasen med hjälp av skript från ursprungligt. När du skapar schemat, lägga till tabeller i en synkronisering att kopiera data och hålla den synkroniserad.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Bör jag använda SQL datasynkronisering du säkerhetskopierar och återställer min databaser?

Du bör inte använda SQL-datasynkronisering (förhandsgranskning) för att skapa en säkerhetskopia av dina data. Du kan inte säkerhetskopiera och återställa till en specifik tidpunkt eftersom SQL-datasynkronisering (förhandsgranskning) synkroniseringar inte är en ny version. Dessutom SQL datasynkronisering (förhandsgranskning) säkerhetskopieras inte andra SQL-objekt, till exempel lagrade procedurer och utför inte motsvarigheten till en återställningsåtgärd snabbt.

En rekommenderad säkerhetskopiering tekniken finns [kopiera en Azure SQL database](sql-database-copy.md).

### <a name="is-collation-supported-in-sql-data-sync"></a>Har stöd för sorteringen i SQL-datasynkronisering?

Ja. SQL-datasynkronisering stöder sortering i följande scenarier:

-   Om tabellerna valda sync-schemat inte är redan i din hubb eller medlem databaser och när du distribuerar gruppen synkronisering, skapar tjänsten automatiskt motsvarande tabeller och kolumner med sorteringsinställningar som valts i tomt mål-databaser.

-   Om tabellerna som synkroniseras redan finns i både din hubb och medlem, kräver SQL datasynkronisering att primärnyckelkolumnerna har samma sortering mellan NAV- och databaser för att distribuera sync-gruppen. Det finns ingen sortering begränsningar för kolumner utom primärnyckelkolumnerna.

### <a name="is-federation-supported-in-sql-data-sync"></a>Stöds federation i SQL-datasynkronisering

Federationsrotdatabas kan användas i tjänsten SQL-datasynkronisering (förhandsgranskning) utan någon begränsning. Du kan inte lägga till slutpunkten federerad databasen till den aktuella versionen av SQL-datasynkronisering (förhandsversion).

## <a name="next-steps"></a>Nästa steg

För mer information om SQL-datasynkronisering, se:

-   [Konfigurera Azure SQL datasynkronisering](sql-database-get-started-sql-data-sync.md)
-   [Metodtips för Azure SQL-datasynkronisering](sql-database-best-practices-data-sync.md)
-   [Övervaka Azure SQL-datasynkronisering med OMS logganalys](sql-database-sync-monitor-oms.md)
-   [Felsökning av problem med Azure SQL-datasynkronisering](sql-database-troubleshoot-data-sync.md)

-   Slutför PowerShell-exempel som visar hur du konfigurerar SQL datasynkronisering:
    -   [Använd PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Hämta SQL Data Sync REST API-dokumentation](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

För mer information om SQL-databasen, se:

-   [Översikt över SQL-databas](sql-database-technical-overview.md)
-   [Livscykelhantering för databasen](https://msdn.microsoft.com/library/jj907294.aspx)
