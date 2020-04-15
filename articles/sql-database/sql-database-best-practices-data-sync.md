---
title: Metodtips för Data Sync
description: Lär dig mer om metodtips för att konfigurera och köra Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 2b72d52463164c2a059fce316cc11a63aad62e7c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380938"
---
# <a name="best-practices-for-sql-data-sync"></a>Regelverk för SQL Data Sync 

I den här artikeln beskrivs metodtips för Azure SQL Data Sync.

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync har **inte** stöd för Azure SQL Database Managed Instance just nu.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a>Säkerhet och tillförlitlighet

### <a name="client-agent"></a>Klientagent

-   Installera klientagenten med hjälp av det minst privilegierade användarkontot som har åtkomst till nätverkstjänsten.  
-   Installera klientagenten på en dator som inte är den lokala SQL Server-datorn.  
-   Registrera inte en lokal databas med mer än en agent.    
    -   Undvik detta även om du synkroniserar olika tabeller för olika synkroniseringsgrupper.  
    -   Att registrera en lokal databas med flera klientagenter innebär utmaningar när du tar bort en av synkroniseringsgrupperna.

### <a name="database-accounts-with-least-required-privileges"></a>Databaskonton med minst nödvändiga privilegier

-   **För synkroniseringsinställning**. Skapa/ändra tabell; Ändra databas; Skapa procedur; Välj/ Ändra schema; Skapa användardefinierad typ.

-   **För pågående synkronisering**. Välj/ Infoga/uppdatera/ta bort på tabeller som har valts för synkronisering och på synkroniseringsmetadata och spårningstabeller. Kör behörighet för lagrade procedurer som skapats av tjänsten. Kör behörighet för användardefinierade tabelltyper.

-   **För avetablering**. Ändra på tabeller del av synkronisering; Välj/ Ta bort tabeller med synkroniseringsmetadata. Kontroll över synkroniseringsspårningstabeller, lagrade procedurer och användardefinierade typer.

Azure SQL Database stöder endast en enda uppsättning autentiseringsuppgifter. Om du vill utföra dessa uppgifter inom det här villkoret bör du tänka på följande alternativ:

-   Ändra autentiseringsuppgifterna för olika faser (till exempel *autentiseringsuppgifter1* för inställningar och *autentiseringsuppgifter2* för pågående).  
-   Ändra behörigheten för autentiseringsuppgifterna (det vill än ändra behörigheten när synkroniseringen har konfigurerats).

## <a name="setup"></a>Installation

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a>Databasöverväganden och begränsningar

#### <a name="sql-database-instance-size"></a>SQL Database-instansstorlek

När du skapar en ny SQL Database-instans anger du den maximala storleken så att den alltid är större än den databas du distribuerar. Om du inte anger den maximala storleken till större än den distribuerade databasen misslyckas synkroniseringen. Även om SQL Data Sync inte erbjuder automatisk `ALTER DATABASE` tillväxt kan du köra kommandot för att öka storleken på databasen när den har skapats. Se till att du håller dig inom storleksgränserna för SQL Database-instansen.

> [!IMPORTANT]
> SQL Data Sync lagrar ytterligare metadata med varje databas. Se till att du står för dessa metadata när du beräknar det utrymme som behövs. Mängden tillsatta omkostnader är relaterad till tabellernas bredd (till exempel kräver smala tabeller mer omkostnader) och mängden trafik.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a>Tabellöverväganden och begränsningar

#### <a name="selecting-tables"></a>Välja tabeller

Du behöver inte inkludera alla tabeller som finns i en databas i en synkroniseringsgrupp. De tabeller som du inkluderar i en synkroniseringsgrupp påverkar effektivitet och kostnader. Inkludera tabeller och de tabeller de är beroende av, i en synkroniseringsgrupp endast om affärsbehov kräver det.

#### <a name="primary-keys"></a>Primärnycklar

Varje tabell i en synkroniseringsgrupp måste ha en primärnyckel. Sql Data Sync-tjänsten kan inte synkronisera en tabell som inte har en primärnyckel.

Innan du använder SQL Data Sync i produktion, testa initiala och pågående synkroniseringsprestanda.

#### <a name="empty-tables-provide-the-best-performance"></a>Tomma tabeller ger bästa prestanda

Tomma tabeller ger bästa prestanda vid initieringstiden. Om måltabellen är tom använder Data Sync bulkinfogning för att läsa in data. Annars gör Data Sync en rad för rad jämförelse och insättning för att söka efter konflikter. Om prestanda inte är ett problem kan du dock ställa in synkronisering mellan tabeller som redan innehåller data.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a>Etablera måldatabaser

SQL Data Sync ger grundläggande databas automatisk etablering.

I det här avsnittet beskrivs begränsningarna för etablering i SQL Data Sync.

#### <a name="autoprovisioning-limitations"></a>Begränsningar för automatisk etablering

SQL Data Sync har följande begränsningar för automatisk etablering:

-   Markera bara de kolumner som skapas i måltabellen. Alla kolumner som inte ingår i synkroniseringsgruppen etableras inte i måltabellerna.
-   Index skapas endast för valda kolumner. Om källtabellindexet har kolumner som inte ingår i synkroniseringsgruppen etableras inte dessa index i måltabellerna.  
-   Index på XML-typkolumner etableras inte.  
-   CHECK-begränsningar har inte etablerats.  
-   Befintliga utlösare i källtabellerna är inte etablerade.  
-   Vyer och lagrade procedurer skapas inte i måldatabasen.
-   ÅTGÄRDER FÖR ATT UPPDATERA KASKAD och TA BORT KASKAD på sekundärnyckelbegränsningar återskapas inte i måltabellerna.
-   Om du har decimal- eller numeriska kolumner med en precision som är större än 28 kan SQL Data Sync uppstå ett problem med konverteringsspill under synkroniseringen. Vi rekommenderar att du begränsar precisionen för decimal- eller numeriska kolumner till högst 28 eller mindre.

#### <a name="recommendations"></a>Rekommendationer

-   Använd funktionen automatisk etablering av SQL Data Sync endast när du provar tjänsten.  
-   Etablera databasschemat för produktion.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a>Var du hittar navdatabasen

#### <a name="enterprise-to-cloud-scenario"></a>Scenario för företag till moln

Om du vill minimera svarstiden håller du navdatabasen nära den största koncentrationen av synkroniseringsgruppens databastrafik.

#### <a name="cloud-to-cloud-scenario"></a>Scenario mellan moln och moln

-   När alla databaser i en synkroniseringsgrupp finns i ett datacenter ska navet finnas i samma datacenter. Den här konfigurationen minskar svarstiden och kostnaden för dataöverföring mellan datacenter.
-   När databaserna i en synkroniseringsgrupp finns i flera datacenter bör navet finnas i samma datacenter som majoriteten av databaserna och databastrafiken.

#### <a name="mixed-scenarios"></a>Blandade scenarier

Tillämpa föregående riktlinjer på komplexa synkroniseringsgruppkonfigurationer, till exempel de som är en blandning av scenarier från företag till moln och från molnet till molnet.

## <a name="sync"></a>Sync

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a>Undvik långsam och kostsam inledande synkronisering

I det här avsnittet diskuterar vi den första synkroniseringen av en synkroniseringsgrupp. Lär dig hur du förhindrar att en första synkronisering tar längre tid och blir dyrare än nödvändigt.

#### <a name="how-initial-sync-works"></a>Så här fungerar inledande synkronisering

När du skapar en synkroniseringsgrupp börjar du med data i endast en databas. Om du har data i flera databaser behandlar SQL Data Sync varje rad som en konflikt som måste lösas. Den här konfliktlösningen gör att den första synkroniseringen går långsamt. Om du har data i flera databaser kan den första synkroniseringen ta mellan flera dagar och flera månader, beroende på databasens storlek.

Om databaserna finns i olika datacenter måste varje rad färdas mellan de olika datacenter. Detta ökar kostnaden för en första synkronisering.

#### <a name="recommendation"></a>Rekommendation

Om möjligt börjar du med data i endast en av synkroniseringsgruppens databaser.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a>Design för att undvika synkroniseringsloopar

En synkroniseringsloop uppstår när det finns cirkelreferenser inom en synkroniseringsgrupp. I det scenariot replikeras varje ändring i en databas i oändlighet och cirkulärt via databaserna i synkroniseringsgruppen.   

Se till att du undviker synkroniseringsloopar, eftersom de orsakar prestandaförsämring och kan avsevärt öka kostnaderna.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a>Ändringar som inte sprids

#### <a name="reasons-that-changes-fail-to-propagate"></a>Orsaker till att ändringarna inte sprids

Ändringar kan misslyckas med att spridas av något av följande skäl:

-   Schema/datatyp inkompatibilitet.
-   Infoga null i kolumner som inte kan null.
-   Bryter mot begränsningar för främmande nyckel.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Vad händer när ändringarna inte sprids?

-   Synkroniseringsgruppen visar att den **Warning** är i ett varningstillstånd.
-   Information visas i portalgränssnittsloggvisaren.
-   Om problemet inte är löst på 45 dagar blir databasen inrutnuell.

> [!NOTE]
> Dessa ändringar sprids aldrig. Det enda sättet att återställa i det här scenariot är att återskapa synkroniseringsgruppen.

#### <a name="recommendation"></a>Rekommendation

Övervaka synkroniseringsgruppens och databashälsan regelbundet via portal- och logggränssnittet.


## <a name="maintenance"></a>Underhåll

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a>Undvik inaktuella databaser och synkroniseringsgrupper

En synkroniseringsgrupp eller en databas i en synkroniseringsgrupp kan bli inaktuella. När en synkroniseringsgrupps status är **inaktuella**slutar den fungera. När en databass status är **inaktuella**kan data gå förlorade. Det är bäst att undvika det här scenariot istället för att försöka återställa från det.

#### <a name="avoid-out-of-date-databases"></a>Undvik inaktuella databaser

En databass status är **instrår när** den har varit offline i 45 dagar eller mer. Om du vill undvika en **instränt** status i en databas kontrollerar du att ingen av databaserna är offline på 45 dagar eller mer.

#### <a name="avoid-out-of-date-sync-groups"></a>Undvik inaktuella synkroniseringsgrupper

Statusen för en synkroniseringsgrupp är **instränt när** en ändring i synkroniseringsgruppen inte sprids till resten av synkroniseringsgruppen i 45 dagar eller mer. Om du vill undvika en **instränt** status för en synkroniseringsgrupp kontrollerar du regelbundet synkroniseringsgruppens historiklogg. Kontrollera att alla konflikter är lösta och att ändringarna sprids i synkroniseringsgruppdatabaserna.

En synkroniseringsgrupp kan misslyckas med att tillämpa en ändring av något av följande skäl:

-   Schemainkompatibilitet mellan tabeller.
-   Datainkompatibilitet mellan tabeller.
-   Om du infogar en rad med ett null-värde i en kolumn som inte tillåter null-värden.
-   Uppdatera en rad med ett värde som bryter mot en sekundärnyckelbegränsning.

Så här förhindrar du inaktuella synkroniseringsgrupper:

-   Uppdatera schemat så att de värden som finns i de misslyckade raderna tillåts.
-   Uppdatera värdena för sekundärnyckeln så att de innehåller värden som finns i de misslyckade raderna.
-   Uppdatera datavärdena på den misslyckade raden så att de är kompatibla med schemat eller externa nycklar i måldatabasen.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a>Undvik avetableringsproblem

I vissa fall kan avregistrering av en databas med en klientagent orsaka att synkroniseringen misslyckas.

#### <a name="scenario"></a>Scenario

1. Synkroniseringsgrupp A skapades med hjälp av en SQL Database-instans och en lokal SQL Server-databas, som är associerad med lokal agent 1.
2. Samma lokala databas är registrerad hos lokal agent 2 (den här agenten är inte associerad med någon synkroniseringsgrupp).
3. Om du avregistrerar den lokala databasen från lokal agent 2 tas spårnings- och metadatatabellerna bort för synkroniseringsgrupp A för den lokala databasen.
4. Synkroniseringsgrupp A-åtgärder misslyckas med det här felet: "Den aktuella åtgärden kunde inte slutföras eftersom databasen inte har etablerats för synkronisering eller om du inte har behörighet till synkroniseringskonfigurationstabellerna."

#### <a name="solution"></a>Lösning

Undvik det här scenariot genom att inte registrera en databas med mer än en agent.

Så här återställer du från det här scenariot:

1. Ta bort databasen från varje synkroniseringsgrupp som den tillhör.  
2. Lägg till databasen i varje synkroniseringsgrupp som du tog bort den från.  
3. Distribuera varje berörd synkroniseringsgrupp (den här åtgärden etablerar databasen).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a>Ändra en synkroniseringsgrupp

Försök inte ta bort en databas från en synkroniseringsgrupp och redigera sedan synkroniseringsgruppen utan att först distribuera någon av ändringarna.

Ta i stället först bort en databas från en synkroniseringsgrupp. Distribuera sedan ändringen och vänta på att avetablering ska slutföras. När avetablering är klart kan du redigera synkroniseringsgruppen och distribuera ändringarna.

Om du försöker ta bort en databas och sedan redigera en synkroniseringsgrupp utan att först distribuera någon av ändringarna misslyckas den ena eller den andra åtgärden. Portalgränssnittet kan bli inkonsekvent. Om detta inträffar uppdaterar du sidan för att återställa rätt tillstånd.

### <a name="avoid-schema-refresh-timeout"></a>Undvik tidsgränsen för schemauppdatering

Om du har ett komplext schema att synkronisera kan du stöta på en "operation timeout" under en schemauppdatering om synkroniseringsmetadatadatabasen har en lägre SKU (exempel: basic). 

#### <a name="solution"></a>Lösning

Om du vill minska problemet skalar du upp databasen för synkroniseringsmetadata så att den har en högre SKU, till exempel S3. 

## <a name="next-steps"></a>Nästa steg
Mer information om SQL Data Sync finns i:

-   Översikt – [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
-   Konfigurera Data Sync
    - I portalen – [Självstudie: Konfigurera SQL Data Sync att synkronisera data mellan Azure SQL Database och SQL Server lokalt](sql-database-get-started-sql-data-sync.md)
    - Med PowerShell
        -  [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Använd PowerShell för att synkronisera mellan en Azure SQL-databas och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Övervaka - [Övervaka SQL Data Sync med Azure Monitor-loggar](sql-database-sync-monitor-oms.md)
-   Felsökning – [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
-   Uppdatera synkroniseringsschemat
    -   Med Transact-SQL – [Automatisera replikeringen av schemaändringar i Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Med PowerShell – [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)

Mer information om SQL Database finns i:

-   [Översikt över SQL-databas](sql-database-technical-overview.md)
-   [Hantering av databasens livscykel](https://msdn.microsoft.com/library/jj907294.aspx)
