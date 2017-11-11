---
title: "Metodtips för Azure SQL datasynkronisering | Microsoft Docs"
description: "Metodtips för hur du konfigurerar och kör Azure SQL-datasynkronisering"
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: d23bd186300d451186dd4f3644290cb4178417a2
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="best-practices-for-sql-data-sync-preview"></a>Metodtips för SQL-datasynkronisering (förhandsgranskning) 

Den här artikeln beskrivs bästa praxis för SQL-datasynkronisering (förhandsversion).

En översikt över SQL datasynkronisering finns [synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering (förhandsgranskning)](sql-database-sync-data.md).

## <a name="security-and-reliability"></a>Säkerhet och tillförlitlighet

### <a name="client-agent"></a>Klientagenten

-   Installera klientagenten med minst följande konto med nätverksåtkomst för tjänsten.

-   Det är bäst om klientagenten är installerad på en dator separat från den lokala SQL Server-datorn.

-   Registrera inte en lokal databas med mer än en agent.

-   Även om synkroniserar olika tabeller för olika synkroniseringsgrupper.

-   Registrerar en lokal databas med flera klienten agenter utgör utmaningar när du tar bort någon av grupperna synkronisering.

### <a name="database-accounts-with-least-required-privilege"></a>Databasen konton med minsta nödvändiga behörighet

-   **För Konfigurera synkronisering**. Skapa/Alter Table, Alter Database, skapa proceduren, Välj / Alter Schema, skapa User-Defined Type.

-   **För en pågående synkronisering**. Välj / Infoga / Uppdatera / ta bort på tabeller som har valts för synkronisering och synka metadata och spåra tabeller, körbehörighet på lagrade procedurer som skapats av tjänsten Execute-behörighet för användardefinierade tabelltyper.

-   **För att etablera**. Ändra på tabeller en del av sync väljer / Ta bort på synka Metadata tabeller, kontroll på synkronisering spårning Sync spårning tabeller, lagrade procedurer och användardefinierade typer.

**Hur kan du använda den här informationen när det finns en enda autentiseringsuppgift för en databas i gruppen synkronisering?**

-   Ändra autentiseringsuppgifterna för olika faser (till exempel credential1 för installation och credential2 för pågående).

-   Ändra behörigheter för autentiseringsuppgifterna (d.v.s. Ändra behörighet när synkronisering har konfigurerats).

## <a name="locate-hub"></a>Var ska NAV-databas

### <a name="enterprise-to-cloud-scenario"></a>Enterprise-to-cloud-scenario

Låt hubb databasen nära största koncentrationen av gruppen synkronisera databastrafik för att minimera svarstider.

### <a name="cloud-to-cloud-scenario"></a>Scenariot molnet till molnet

-   När alla databaser i en grupp för synkronisering är i ett datacenter, måste navet finnas i samma datacenter. Den här konfigurationen minskar latens och kostnaden för att överföra data mellan datacenter.

-   När databaser i en grupp för synkronisering har flera Datacenter kan måste navet finnas i samma datacenter som de flesta databaser och databastrafik.

### <a name="mixed-scenarios"></a>Blandat scenarier

Gäller de föregående riktlinjerna för mer komplexa konfigurationer synkronisering.

## <a name="database-considerations-and-constraints"></a>Överväganden för databasen och begränsningar

### <a name="sql-database-instance-size"></a>Storlek på SQL-databas

När du skapar en ny instans av SQL-databas, ange den maximala storleken så att den alltid är större än databasen som du distribuerar. Om du inte anger den maximala storleken överskrider den distribuerade databasen, misslyckas synkroniseringen. När det finns ingen automatisk ökning - kan du göra en ALTER DATABASE för att öka storleken på databasen när den har skapats. Kontrollera att du hålla storleksbegränsningarna SQL Database-instans.

> [!IMPORTANT]
> SQL-datasynkronisering lagrar ytterligare metadata med varje databas. Glöm inte att ta hänsyn till dessa metadata när du beräknar utrymmet som krävs. Mängden ytterligare kostnader styrs av bredd tabeller (till exempel smala tabeller kräver mer resurser) och mängden trafik.

## <a name="table-considerations-and-constraints"></a>Tabell överväganden och begränsningar

### <a name="selecting-tables"></a>Om du väljer register

Alla tabeller i en databas måste vara i en [sync grupp](#sync-group). Val av vilka tabeller som ska ingå i en grupp för synkronisering och som du vill exkludera (eller ingå i en annan grupp Sync) kan påverka effektiviteten och kostnader. Ta bara de tabellerna i en grupp för synkronisering att verksamheten behöver begäran och tabeller som de är beroende.

### <a name="primary-keys"></a>Primära nycklar

Varje tabell i en grupp för synkronisering måste ha en primärnyckel. Tjänsten SQL datasynkronisering (förhandsversion) är inte synkronisera någon tabell som inte har en primärnyckel.

Testa prestanda för inledande och pågående synkronisering innan du distribuerar i produktionen.

## <a name="provisioning-destination-databases"></a>Etablerar mål databaser

Förhandsgranska SQL datasynkronisering (förhandsversion) ger grundläggande databasen automatiskt-etablering.

Det här avsnittet beskrivs begränsningar i SQL-datasynkronisering (förhandsversion) etablering.

### <a name="auto-provisioning-limitations"></a>Automatisk etablering begränsningar

Följande är begränsningar i SQL-datasynkronisering (förhandsgranskning) Automatisk etablering.

-   Endast kolumner valt skapas i måltabellen.
Därför om vissa kolumner inte tillhör gruppen sync dessa kolumner inte har etablerats i mål-tabeller.

-   Index skapas endast för de markerade kolumnerna.
Om käll-Tabellindex har kolumner som inte ingår i gruppen sync dessa index inte har etablerats i mål-tabeller.

-   Index för kolumner av typen XML etableras inte.

-   Kontrollbegränsningar etableras inte.

-   Befintliga utlösare på källtabellerna etableras inte.

-   Vyer och lagrade procedurer skapas inte till måldatabasen.

### <a name="recommendations"></a>Rekommendationer

-   Använd funktionen för automatisk etablering endast för försöker tjänsten.

-   För produktion, ska du etablera databasschemat.

## <a name="avoid-a-slow-and-costly-initial-synchronization"></a>Undvik en långsam och kostsamt inledande synkronisering

Det här avsnittet beskrivs den första synkroniseringen av en grupp för synkronisering och vad du kan göra för att undvika en inledande synkronisering tar längre tid än nödvändigt och kostnaden är mer än den borde.

### <a name="how-initial-synchronization-works"></a>Hur den första synkroniseringen fungerar

När du skapar en grupp för synkronisering, börja med data i en enda databas. Om du har data i flera databaser behandlar SQL datasynkronisering (förhandsgranskning) varje rad som en konflikt som måste lösning. Den här konfliktlösning gör den inledande synkroniseringen att gå långsamt, tar flera dagar till flera månader, beroende på databasstorleken.

Dessutom om databaserna är i olika datacenter, är kostnaderna för inledande synkronisering högre än nödvändigt, eftersom varje rad måste åka mellan olika datacenter.

### <a name="recommendation"></a>Rekommendation

Varje gång startar möjligt med data i endast en av gruppen synkronisera databaser.

## <a name="design-to-avoid-synchronization-loops"></a>Design så att loopar undviks synkronisering

En loop synkronisering resulterar när det finns cirkelreferenser i en grupp för synkronisering så att varje ändring i en databas replikeras via databaserna i gruppen sync cirkulärt och oändlighet. Du vill undvika slingor synkronisering eftersom de försämra prestanda och avsevärt öka kostnaderna.

## <a name="avoid-out-of-date-databases-and-sync-groups"></a>Undvika inaktuella databaser och synkronisera grupper

En sync-grupp eller en databas i en grupp för synkronisering kan bli inaktuell. När en synkronisering grupp status är ”inaktuell”, slutar fungera. När en databas status är ”inaktuell”, kan data gå förlorade. Det är bäst att undvika dessa situationer i stället för att återställa från dem.

### <a name="avoid-out-of-date-databases"></a>Undvika inaktuella databaser

Status för en databas har angetts till inaktuella när det har varit offline i 45 dagar eller mer. Undvik inaktuella status på en databas genom att säkerställa att ingen av databaserna är offline i 45 dagar eller mer.

### <a name="avoid-out-of-date-sync-groups"></a>Undvika inaktuella synkroniseringsgrupper

En Gruppstatus för synkronisering har angetts till inaktuella när ändringar inom gruppen synkronisering misslyckas att spridas till resten av gruppen synkronisering i 45 dagar eller mer. Undvik inaktuella status på en grupp för synkronisering genom att regelbundet kontrollera loggen för gruppen sync-historik. Kontrollera att alla konflikter har lösts och har sprids ändringar i hela synkronisera grupp databaser.

En grupp för synkronisering kanske inte kan tillämpa en ändring anledningar:

-   Schemainkompatibilitet mellan tabeller.

-   Data inkompatibilitet mellan tabeller.

-   Infoga en rad med ett null-värde i en kolumn som inte tillåter null-värden.

-   Uppdatera en rad med ett värde som bryter mot villkoret för en sekundärnyckel.

Du kan förhindra att inaktuella synkroniseringsgrupper av:

-   Uppdatera schemat så att värdena i de felaktiga raderna.

-   Uppdatera sekundärnyckelvärden för att inkludera de värden som finns i de felaktiga raderna.

-   Uppdatera datavärdena i raden gick inte att vara kompatibel med schemat eller sekundärnycklar i måldatabasen.

## <a name="avoid-deprovisioning-issues"></a>Undvika avetablering problem

Avregistrerar en databas med en klientagent kan i vissa fall kan orsaka synkroniseringar misslyckas.

### <a name="scenario"></a>Scenario

1. Synkronisera grupp A har skapats med en instans av SQL-databas och en lokal SQL Server-databas som är kopplat till lokal agent 1.

2. Samma lokala databas har registrerats med lokal agent 2 (den här agenten inte är associerad med någon synkronisering grupp).

3. Avregistrerar den lokala databasen från lokal agent 2 tar bort tabellerna spårning/metadata för synkroniseringen grupp A för den lokala databasen.

4. Nu synkroniseringsåtgärder grupp A misslyckas med följande fel – ”den aktuella åtgärden kunde inte slutföras eftersom databasen inte har etablerats för synkronisering eller du har inte behörighet att synkronisera configuration tabeller”.

### <a name="solution"></a>Lösning

Undvik situationen genom att helt aldrig registrera en databas med mer än en agent.

För att återställa från den här situationen:

1. Ta bort databasen från varje synkronisering grupp det tillhör.

2. Lägga till databasen i varje sync-grupp som du precis har tagit bort den från igen.

3. Distribuera varje berörda sync-grupp (som etablerar databasen).

## <a name="handling-changes-that-fail-to-propagate"></a>Hantering av ändringar som inte kan sprida

### <a name="reasons-that-changes-fail-to-propagate"></a>Det gick inte att sprida skäl som ändras

Ändringar kan det hända att sprida på grund av flera orsaker. Vissa orsaker är:

-   Schema-datatyp inkompatibilitet.

-   Om du försöker infoga null i icke-nullbara kolumner.

-   Brott mot sekundärnyckelbegränsningar.

### <a name="what-happens-when-changes-fail-to-propagate"></a>Vad händer när det gick inte att sprida ändringarna?

-   Synkronisera grupp visas den är i ett varningstillstånd.

-   Information finns i portalen UI Loggvisaren.

-   Om problemet kvarstår för 45 dagar databasen börjar bli inaktuell.

> [!NOTE]
> De här ändringarna sprida aldrig. Det enda sättet att återställa är att skapa gruppen synkronisering.

### <a name="recommendation"></a>Rekommendation

Övervaka hälsan för synkronisering av gruppen och databasen regelbundet via gränssnittet Företagsportalen och logga.

## <a name="modifying-your-sync-group"></a>Ändra en grupp för synkronisering

Försök inte att ta bort en databas från en grupp för synkronisering och sedan redigera gruppen sync utan att först distribuera en av ändringarna.

Först, ta bort en databas från en grupp för synkronisering. Sedan distribuerar ändringen och vänta på att ta bort etableringen har slutförts. När den här åtgärden är klar, kan du redigera gruppen synkronisera och distribuera ändringarna.

Om du försöker ta bort en databas och sedan redigera en sync-grupp utan att först distribuera ändringarna, en eller den andra åtgärden misslyckas och gränssnittet portal kan hamna i ett inkonsekvent tillstånd. I det här fallet kan du uppdatera sidan om du vill återställa rätt tillstånd.

## <a name="next-steps"></a>Nästa steg
För mer information om SQL-datasynkronisering, se:

-   [Synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering](sql-database-sync-data.md)
-   [Konfigurera Azure SQL datasynkronisering](sql-database-get-started-sql-data-sync.md)
-   [Övervaka Azure SQL-datasynkronisering med OMS logganalys](sql-database-sync-monitor-oms.md)
-   [Felsökning av problem med Azure SQL-datasynkronisering](sql-database-troubleshoot-data-sync.md)

-   Slutför PowerShell-exempel som visar hur du konfigurerar SQL datasynkronisering:
    -   [Använd PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Hämta SQL Data Sync REST API-dokumentation](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

För mer information om SQL-databasen, se:

-   [Översikt över SQL-databas](sql-database-technical-overview.md)
-   [Livscykelhantering för databasen](https://msdn.microsoft.com/library/jj907294.aspx)
