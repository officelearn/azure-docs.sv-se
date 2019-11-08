---
title: Metodtips för Data Sync
description: Lär dig mer om metod tips för att konfigurera och köra Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 75fe07dc9847ae32248688bc20fac01e74c7b26a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821856"
---
# <a name="best-practices-for-sql-data-sync"></a>Regelverk för SQL Data Sync 

Den här artikeln beskriver metod tips för Azure SQL Data Sync.

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync har **inte** stöd för Azure SQL Database Managed Instance just nu.

## <a name="security-and-reliability"></a>Säkerhet och tillförlitlighet

### <a name="client-agent"></a>Klient agent

-   Installera klient agenten genom att använda det lägsta privilegierade användar kontot som har åtkomst till nätverks tjänsten.  
-   Installera klient agenten på en dator som inte är den lokala SQL Server datorn.  
-   Registrera inte en lokal databas med fler än en agent.    
    -   Undvik detta även om du synkroniserar olika tabeller för olika Sync-grupper.  
    -   Registrering av en lokal databas med flera klient agenter utgör utmaningarna när du tar bort en av Sync-grupperna.

### <a name="database-accounts-with-least-required-privileges"></a>Databas konton med minst behörighet som krävs

-   **För Sync-installation**. Skapa/ändra tabell; Ändra databas; Skapa procedur; Välj/Ändra schema; Skapa användardefinierad typ.

-   **För pågående synkronisering**. Välj/Infoga/uppdatera/ta bort i tabeller som väljs för synkronisering och vid synkronisering av metadata och spårning av tabeller; Kör behörighet för lagrade procedurer som skapats av tjänsten. Kör behörighet för användardefinierade tabell typer.

-   **För avetablering**. Ändra i tabeller del av synkronisering; Välj/Ta bort i metadata tabeller för synkronisering; Kontroll över synkronisering av spårnings tabeller, lagrade procedurer och användardefinierade typer.

Azure SQL Database stöder endast en uppsättning autentiseringsuppgifter. Överväg följande alternativ för att utföra dessa uppgifter i den här begränsningen:

-   Ändra autentiseringsuppgifterna för olika faser (till exempel *credentials1* för installation och *credentials2* för pågående).  
-   Ändra behörigheten för autentiseringsuppgifterna (det vill säga ändra behörighet när synkroniseringen har kon figurer ATS).

## <a name="setup"></a>Konfiguration

### <a name="database-considerations-and-constraints"></a>Överväganden och begränsningar för databasen

#### <a name="sql-database-instance-size"></a>SQL Database instans storlek

När du skapar en ny SQL Database-instans anger du den maximala storleken så att den alltid är större än den databas som du distribuerar. Om du inte anger den maximala storleken till större än den distribuerade databasen, Miss lyckas synkroniseringen. Även om SQL Data Sync inte erbjuder automatisk tillväxt, kan du köra kommandot `ALTER DATABASE` för att öka storleken på databasen när den har skapats. Se till att du håller dig inom gränsen för SQL Database instans storlek.

> [!IMPORTANT]
> SQL Data Sync lagrar ytterligare metadata för varje databas. Se till att du tar hänsyn till dessa metadata när du beräknar utrymmet som behövs. Mängden extra kostnader är relaterad till bredden på tabellerna (till exempel smala tabeller kräver mer kostnader) och mängden trafik.

### <a name="table-considerations-and-constraints"></a>Tabell överväganden och begränsningar

#### <a name="selecting-tables"></a>Välja tabeller

Du behöver inte inkludera alla tabeller som finns i en databas i en Sync-grupp. De tabeller som du lägger till i en Sync-grupp påverkar effektiviteten och kostnaderna. Inkludera tabeller och de tabeller som de är beroende av, i en Sync-grupp endast om affärs behoven kräver det.

#### <a name="primary-keys"></a>Primära nycklar

Varje tabell i en Sync-grupp måste ha en primär nyckel. Tjänsten SQL Data Sync kan inte synkronisera en tabell som inte har en primär nyckel.

Innan du använder SQL Data Sync i produktion testar du inledande och pågående synkroniseringsinställningar.

#### <a name="empty-tables-provide-the-best-performance"></a>Tomma tabeller ger bästa möjliga prestanda

Tomma tabeller ger bästa prestanda vid initierings tiden. Om mål tabellen är tom använder datasynkronisering Mass infogning för att läsa in data. Annars utför datasynkroniseringen rad-för-rad-jämförelse och infogning för att söka efter konflikter. Om prestanda inte är ett problem kan du dock konfigurera synkronisering mellan tabeller som redan innehåller data.

### <a name="provisioning-destination-databases"></a>Etablering av mål databaser

SQL Data Sync tillhandahåller grundläggande autoetablering av databaser.

I det här avsnittet beskrivs begränsningar för etablering i SQL Data Sync.

#### <a name="autoprovisioning-limitations"></a>Begränsningar för autoetablering

SQL Data Sync har följande begränsningar för autoetablering:

-   Välj bara de kolumner som skapas i mål tabellen. Alla kolumner som inte ingår i Sync-gruppen är inte etablerade i mål tabellerna.
-   Index skapas endast för de markerade kolumnerna. Om käll tabellens index innehåller kolumner som inte ingår i Sync-gruppen, är dessa index inte etablerade i mål tabellerna.  
-   Index för kolumner med XML-typ har inte tillhandahållits.  
-   KONTROLL begränsningar har inte tillhandahållits.  
-   Befintliga utlösare i käll tabellerna har inte tillhandahållits.  
-   Vyer och lagrade procedurer skapas inte i mål databasen.
-   VID uppdatering av kaskad och BORTTAGNINGs åtgärder på sekundär nyckel, återskapas inte i mål tabellerna.
-   Om du har decimaler eller numeriska kolumner med en precision som är större än 28 kan SQL Data Sync stöta på ett problem med konverterings spill under synkroniseringen. Vi rekommenderar att du begränsar precisionen för decimaler eller numeriska kolumner till 28 eller mindre.

#### <a name="recommendations"></a>Rekommendationer

-   Använd SQL Data Sync autoetablerings funktion endast när du testar tjänsten.  
-   Etablera databas schema för produktion.

### <a name="locate-hub"></a>Var du hittar Hub-databasen

#### <a name="enterprise-to-cloud-scenario"></a>Scenario för företag till moln

För att minimera fördröjningen bör du hålla Hub-databasen nära den största koncentrationen av databas trafiken i Sync-gruppen.

#### <a name="cloud-to-cloud-scenario"></a>Moln-till-moln-scenario

-   När alla databaser i en Sync-grupp finns i ett Data Center, ska hubben finnas i samma data Center. Den här konfigurationen minskar svars tiden och kostnaden för data överföring mellan data Center.
-   När databaserna i en Sync-grupp finns i flera data Center, ska hubben finnas i samma data Center som de flesta databaserna och databas trafiken.

#### <a name="mixed-scenarios"></a>Blandade scenarier

Tillämpa de föregående rikt linjerna för komplexa inställningar för synkronisering av grupper, t. ex. sådana som är en blandning av företags-till-moln-och moln-till-moln-scenarier.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a>Undvik långsam och kostsam inledande synkronisering

I det här avsnittet diskuterar vi den första synkroniseringen av en Sync-grupp. Lär dig hur du kan förhindra en inledande synkronisering från att ta längre tid och bli dyrare än nödvändigt.

#### <a name="how-initial-sync-works"></a>Så här fungerar den inledande synkroniseringen

När du skapar en Sync-grupp börjar du med data i en databas. Om du har data i flera databaser behandlar SQL Data Sync varje rad som en konflikt som behöver lösas. Den här konflikt lösningen gör att den inledande synkroniseringen går långsamt. Om du har data i flera databaser kan den första synkroniseringen ta mellan flera dagar och flera månader, beroende på databasens storlek.

Om databaserna finns i olika data Center måste varje rad transporteras mellan olika data Center. Detta ökar kostnaden för en inledande synkronisering.

#### <a name="recommendation"></a>Rekommendation

Börja om möjligt med data i endast en av databaserna för Sync-gruppen.

### <a name="design-to-avoid-synchronization-loops"></a>Design för att undvika Sync-slingor

En synkroniseringskonflikt inträffar när det finns cirkel referenser i en Sync-grupp. I det scenariot är varje ändring i en databas oändlig och cirkulär replikerad genom databaserna i Sync-gruppen.   

Se till att du undviker synkroniseringsfel, eftersom de orsakar prestanda försämring och kan öka kostnaderna avsevärt.

### <a name="handling-changes-that-fail-to-propagate"></a>Ändringar som inte sprids

#### <a name="reasons-that-changes-fail-to-propagate"></a>Orsaker till att ändringar inte sprids

Ändringar kan inte spridas av någon av följande orsaker:

-   Inkompatibilitet för schema/datatype.
-   Infogar null i kolumner som inte kan ha värdet null.
-   Brott mot sekundär nyckel begränsningar.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Vad händer om det inte går att sprida ändringar?

-   Sync-gruppen visar att den är i ett **varnings** tillstånd.
-   Information visas i portalens GRÄNSSNITTs logg visare.
-   Om problemet inte är löst i 45 dagar blir databasen inaktuell.

> [!NOTE]
> Ändringarna sprids aldrig. Det enda sättet att återställa i det här scenariot är att skapa Sync-gruppen igen.

#### <a name="recommendation"></a>Rekommendation

Övervaka Sync-gruppen och databas hälsan regelbundet via portalen och logg gränssnittet.


## <a name="maintenance"></a>Underhåll

### <a name="avoid-out-of-date-databases-and-sync-groups"></a>Undvik inaktuella databaser och synkronisera grupper

En Sync-grupp eller en databas i en Sync-grupp kan bli inaktuell. När en synkroniseringsstatus-status är **inaktuell**, slutar den att fungera. När en Databass status är **inaktuell**kan data gå förlorade. Det är bäst att undvika det här scenariot i stället för att försöka återställa från det.

#### <a name="avoid-out-of-date-databases"></a>Undvik inaktuella databaser

En databas status är **inaktuell** när den har varit offline i 45 dagar eller mer. För att undvika **inaktuella** status för en databas, se till att ingen av databaserna är offline i 45 dagar eller mer.

#### <a name="avoid-out-of-date-sync-groups"></a>Undvik inaktuella Sync-grupper

Statusen för en synkroniseringsstatus är **inaktuell** när en ändring i Sync-gruppen inte kan spridas till resten av Sync-gruppen i 45 dagar eller mer. Om du vill undvika en **inaktuell** status för en Sync-grupp kontrollerar du regelbundet den synkroniserade gruppens historik logg. Se till att alla konflikter är lösta och att ändringarna har spridits i grupp databaserna för synkronisering.

En synkroniseringsresurs kanske inte kan tillämpa någon ändring av följande orsaker:

-   Schema inkompatibilitet mellan tabeller.
-   Inkompatibla data mellan tabeller.
-   Infogar en rad med ett null-värde i en kolumn som inte tillåter Null-värden.
-   Uppdatera en rad med ett värde som bryter mot en sekundär nyckel begränsning.

Förhindra inaktuella Sync-grupper:

-   Uppdatera schemat för att tillåta värdena som finns i raderna som misslyckades.
-   Uppdatera sekundär nyckel värden så att de innehåller de värden som finns i raderna som misslyckades.
-   Uppdatera datavärdena i den felaktiga raden så att de är kompatibla med schemat eller sekundär nycklarna i mål databasen.

### <a name="avoid-deprovisioning-issues"></a>Undvik att avetablera problem

I vissa fall kan avregistrering av en databas med en klient agent leda till att synkroniseringen Miss fungerar.

#### <a name="scenario"></a>Scenario

1. Sync Group A har skapats med hjälp av en SQL Database-instans och en lokal SQL Server-databas som är associerad med lokal agent 1.
2. Samma lokala databas har registrerats med lokal Agent 2 (den här agenten är inte kopplad till någon Sync-grupp).
3. Om du avregistrerar den lokala databasen från lokal Agent 2 raderas spårnings-och meta-tabellerna för Sync Group A för den lokala databasen.
4. Det gick inte att synkronisera en åtgärd med det här felet: "den aktuella åtgärden kunde inte slutföras eftersom databasen inte är etablerad för synkronisering eller så har du inte behörighet till konfigurations tabellerna för synkronisering."

#### <a name="solution"></a>Lösning

Undvik det här scenariot genom att inte registrera en databas med fler än en agent.

Så här återställer du från det här scenariot:

1. Ta bort databasen från varje Sync-grupp som den tillhör.  
2. Lägg tillbaka databasen i varje synkroniseringsresurs som du tog bort den från.  
3. Distribuera varje grupp som påverkas (den här åtgärden etablerar databasen).  

### <a name="modifying-your-sync-group"></a>Ändra en Sync-grupp

Försök inte att ta bort en databas från en synkroniseringsresurs och redigera sedan Sync-gruppen utan att först distribuera en av ändringarna.

Ta i stället först bort en databas från en Sync-grupp. Distribuera sedan ändringen och vänta tills avetableringen har slutförts. När avetableringen är färdig kan du redigera synkroniseringsresursen och distribuera ändringarna.

Om du försöker ta bort en databas och sedan redigera en synkroniserad grupp utan att först distribuera en av ändringarna, Miss lyckas en eller andra åtgärden. Portal gränssnittet kan bli inkonsekvent. Om detta inträffar uppdaterar du sidan för att återställa rätt tillstånd.

## <a name="next-steps"></a>Nästa steg
Mer information om SQL Data Sync finns i:

-   Översikt – [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
-   Konfigurera Data Sync
    - I portalen – [Självstudie: Konfigurera SQL Data Sync att synkronisera data mellan Azure SQL Database och SQL Server lokalt](sql-database-get-started-sql-data-sync.md)
    - Med PowerShell
        -  [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Använd PowerShell för att synkronisera mellan en Azure SQL-databas och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Övervaka [SQL Data Sync med Azure Monitor loggar](sql-database-sync-monitor-oms.md)
-   Felsökning – [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
-   Uppdatera synkroniseringsschemat
    -   Med Transact-SQL – [Automatisera replikeringen av schemaändringar i Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Med PowerShell – [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)

Mer information om SQL Database finns i:

-   [Översikt över SQL Database](sql-database-technical-overview.md)
-   [Databas livs cykel hantering](https://msdn.microsoft.com/library/jj907294.aspx)
