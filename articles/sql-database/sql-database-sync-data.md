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
ms.date: 06/27/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: fe11926cb7f6b2a80913895b685acfcc433e9805
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
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

## <a name="common-questions"></a>Vanliga frågor

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Hur ofta kan datasynkronisering synkroniserar Mina data? 
Minsta frekvens är var femte minut.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Kan jag använda datasynkronisering ska synkroniseras mellan SQL Server lokala databaser? 
Inte direkt. Du kan synkronisera mellan lokala SQL Server-databaser indirekt, men genom att skapa en Hub-databas i Azure och sedan lägga till de lokala databaserna i gruppen synkronisering.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>Kan jag använda datasynkronisering att fördefiniera data från min produktionsdatabasen i en tom databas och hålla dem synkroniserade? 
Ja. Skapa schemat manuellt i den nya databasen med hjälp av skript från ursprungligt. När du skapar schemat, lägga till tabeller i en synkronisering att kopiera data och hålla den synkroniserad.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Varför ser tabeller som jag inte kan skapa?  
Datasynkronisering skapar tabeller sida i databasen för ändringsspårning. Ta bort inte eller datasynkronisering slutar fungera.
   
### <a name="i-got-an-error-message-that-said-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-the-error"></a>Jag har fått ett felmeddelande som säger ”det går inte att infoga värdet NULL i kolumnen \<kolumnen\>. Kolumnen tillåter inte null-värden ”. Vad innebär det och hur kan jag åtgärda felet? 
Det här felmeddelandet innebär att ett av de två följande problem:
1.  Det kan finnas en tabell utan en primärnyckel. Åtgärda problemet genom att lägga till en primär nyckel till alla tabeller som du synkronisera.
2.  Det kan finnas en WHERE-sats i CREATE INDEX-instruktionen. Synkronisera hanterar inte det här villkoret. Åtgärda problemet genom att ta bort WHERE-satsen eller göra ändringarna manuellt för alla databaser. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Hur hanterar datasynkronisering cirkelreferenser? Det vill säga när samma data synkroniseras i flera synkroniseringsgrupper och ändras som ett resultat?
Datasynkronisering kan inte hantera cirkelreferenser. Se till att undvikas. 

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Hur kan exportera och importera en databas med datasynkronisering?
När du exporterar en databas som en `.bacpac` filen och importera filen om du vill skapa en ny databas måste du göra följande två saker du kan använda datasynkronisering i den nya databasen:
1.  Rensa datasynkronisering objekt och tabeller sida på den **ny databas** med hjälp av [skriptet](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql). Det här skriptet tar bort alla nödvändiga datasynkronisering objekt från databasen.
2.  Återskapa gruppen synkronisering med den nya databasen. Ta bort om du inte längre behöver den gamla sync-gruppen.

## <a name="sync-req-lim"></a>Krav och begränsningar

### <a name="general-requirements"></a>Allmänna krav

-   Varje tabell måste ha en primärnyckel. Ändra inte värdet för den primära nyckeln i en rad. Om du behöver göra detta kan ta bort raden och återskapa den med nya primärnyckelvärdet. 

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
