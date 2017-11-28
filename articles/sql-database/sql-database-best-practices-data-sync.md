---
title: "Metodtips för datasynkronisering för Azure SQL (förhandsversion) | Microsoft Docs"
description: "Lär dig mer om metodtips för att konfigurera och köra Azure SQL Data Sync (förhandsversion)."
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 1c8ad4b318d52b5cb6af284b3304cfa7ad35522b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="best-practices-for-sql-data-sync-preview"></a>Metodtips för SQL-datasynkronisering (förhandsgranskning) 

Den här artikeln beskrivs bästa praxis för datasynkronisering för Azure SQL (förhandsversion).

En översikt över SQL datasynkronisering (förhandsgranskning), se [synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering (förhandsgranskning)](sql-database-sync-data.md).

## <a name="security-and-reliability"></a>Säkerhet och tillförlitlighet

### <a name="client-agent"></a>Klientagenten

-   Installera klientagenten med lägsta möjliga användarkonto som har tjänsten nätverksåtkomst.  
-   Installera klientagenten på en dator som inte är lokal SQL Server-datorn.  
-   Inte registrera en lokal databas med mer än en agent.    
    -   Undvik detta även om du synkroniserar olika tabeller för olika synkroniseringsgrupper.  
    -   Registrerar en lokal databas med flera klienten agenter utgör utmaningar när du tar bort någon av grupperna synkronisering.

### <a name="database-accounts-with-least-required-privileges"></a>Databasen konton med lägsta behörighet som krävs

-   **För Konfigurera synkronisering**. Skapa/ändra tabellen; ALTER Database; Skapa proceduren; Välj / Alter Schema. Skapa User-Defined Type.

-   **För en pågående synkronisering**. Välj / Infoga / Uppdatera / ta bort för tabeller som har valts för synkronisering och på synka metadata och spåra tabeller. Körbehörighet på lagrade procedurer som skapats av tjänsten. Körningsbehörighet användardefinierade tabelltyper.

-   **För avetablering**. Ändra på tabeller som en del av sync; Välj / Ta bort på Synkronisera metadatatabeller. Kontrollera på synkronisering spårning tabeller, lagrade procedurer och användardefinierade typer.

Azure SQL Database stöder bara en enda uppsättning autentiseringsuppgifter. Överväg följande alternativ för att utföra dessa uppgifter i den här begränsningen:

-   Ändra autentiseringsuppgifterna för olika faser (till exempel *credentials1* för installation och *credentials2* för pågående).  
-   Ändra behörigheter för autentiseringsuppgifterna (d.v.s. Ändra behörighet när synkronisering har konfigurerats).

## <a name="setup"></a>Konfiguration

### <a name="database-considerations-and-constraints"></a>Överväganden för databasen och begränsningar

#### <a name="sql-database-instance-size"></a>Storlek på SQL-databas

När du skapar en ny instans av SQL-databas, ange den maximala storleken så att den alltid är större än databasen som du distribuerar. Om du inte anger den maximala storleken till större än den distribuerade databasen, misslyckas synkronisering. Även om SQL-datasynkronisering (förhandsgranskning) inte kan automatisk ökning, kan du köra den `ALTER DATABASE` kommando för att öka storleken på databasen när den har skapats. Se till att du hålla storleksbegränsningarna SQL Database-instans.

> [!IMPORTANT]
> SQL-datasynkronisering (förhandsgranskning) lagrar ytterligare metadata med varje databas. Se till att du ta hänsyn till dessa metadata när du beräknar utrymmet som krävs. Mängden ytterligare kostnader som är relaterad till bredd tabeller (till exempel smala tabeller kräver mer resurser) och mängden trafik.

### <a name="table-considerations-and-constraints"></a>Tabell överväganden och begränsningar

#### <a name="selecting-tables"></a>Om du väljer register

Du behöver ta med alla tabeller i en databas i en grupp för synkronisering. Tabellerna som inkluderas i en grupp för synkronisering påverka effektiviteten och kostnader. Inkludera tabeller och tabeller som de är beroende av, i en grupp för synkronisering endast om affärsbehov kräver detta.

#### <a name="primary-keys"></a>Primära nycklar

Varje tabell i en grupp för synkronisering måste ha en primärnyckel. Tjänsten SQL-datasynkronisering (förhandsversion) kan inte synkronisera en tabell som inte har en primärnyckel.

Testa prestanda för inledande och pågående synkronisering innan du använder SQL-datasynkronisering (förhandsgranskning) i produktion.

### <a name="provisioning-destination-databases"></a>Etablerar mål databaser

Förhandsgranska SQL datasynkronisering (förhandsversion) ger autoprovisioning basic-databas.

Det här avsnittet beskrivs begränsningar för etablering i SQL-datasynkronisering (förhandsversion).

#### <a name="autoprovisioning-limitations"></a>Autoprovisioning begränsningar

SQL-datasynkronisering (förhandsversion) har följande begränsningar på autoprovisioning:

-   Markera de kolumner som har skapats i måltabellen.  
    Alla kolumner som inte tillhör gruppen synkronisering etablerats inte i mål-tabeller.
-   Index skapas endast för de markerade kolumnerna.  
    Om käll-Tabellindex har kolumner som inte ingår i gruppen synkronisering, etableras inte dessa index i mål-tabeller.  
-   Index för kolumner av typen XML etableras inte.  
-   Kontrollbegränsningar etableras inte.  
-   Befintliga utlösare på källtabellerna etableras inte.  
-   Vyer och lagrade procedurer skapas inte till måldatabasen.

#### <a name="recommendations"></a>Rekommendationer

-   Använda SQL-datasynkronisering (förhandsgranskning) autoprovisioning funktionen endast när du testar tjänsten.  
-   Etablera databasschemat för produktion.

### <a name="locate-hub"></a>Var ska NAV-databas

#### <a name="enterprise-to-cloud-scenario"></a>Enterprise-to-cloud-scenario

Låt hubb databasen nära största koncentrationen av gruppen synkronisera databastrafik för att minimera svarstider.

#### <a name="cloud-to-cloud-scenario"></a>Scenariot molnet till molnet

-   När alla databaser i en grupp för synkronisering är i ett datacenter, måste navet finnas i samma datacenter. Den här konfigurationen minskar latens och kostnaden för att överföra data mellan datacenter.
-   När databaser i en grupp för synkronisering har flera Datacenter kan måste navet finnas i samma datacenter som flesta databaser och databastrafik.

#### <a name="mixed-scenarios"></a>Blandat scenarier

Riktlinjerna ovan gäller komplexa sync konfigurationer, till exempel de som är en blandning av enterprise-to-cloud och moln-to-cloud-scenarier.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a>Undvika långsamt och kostsamt inledande synkronisering

I det här avsnittet diskuterar vi den första synkroniseringen av en grupp för synkronisering. Lär dig hur du vill förhindra att en inledande synkronisering från tar längre tid och som kostar mer än nödvändigt.

#### <a name="how-initial-sync-works"></a>Hur den första synkroniseringen fungerar

När du skapar en grupp för synkronisering, börja med data i en enda databas. Om du har data i flera databaser behandlar SQL datasynkronisering (förhandsgranskning) varje rad som en konflikt som måste lösas. Den här konfliktlösning gör den första synkroniseringen gå långsamt. Om du har data i flera databaser kan inledande synkronisering ta mellan flera dagar och flera månader, beroende på databasstorleken.

Om databaserna är i olika datacenter, måste varje rad färdas mellan olika datacenter. Detta ökar kostnaden för en inledande synkronisering.

#### <a name="recommendation"></a>Rekommendation

Starta om möjligt med data i endast en av gruppen synkronisera databaser.

### <a name="design-to-avoid-synchronization-loops"></a>Design så att loopar undviks synkronisering

En loop sync uppstår vid cirkelreferenser i en grupp för synkronisering. I det scenariot replikeras varje ändring i en databas oändlighet och cirkulärt via databaserna i gruppen synkronisering.   

Se till att du inte synkronisera slingor, eftersom de prestanda försämras och kan avsevärt öka kostnaderna.

### <a name="handling-changes-that-fail-to-propagate"></a>Ändringar som inte kan sprida

#### <a name="reasons-that-changes-fail-to-propagate"></a>Det gick inte att sprida skäl som ändras

Ändringar kanske inte kan sprida av något av följande skäl:

-   Schema-datatyp inkompatibilitet.
-   Lägga till null i icke-nullbara kolumner.
-   Brott mot sekundärnyckelbegränsningar.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Vad händer när det gick inte att sprida ändringarna?

-   Synkronisera gruppen visar att det är i ett **varning** tillstånd.
-   Information visas i portalen UI-Loggvisaren.
-   Om problemet kvarstår för 45 dagar databasen börjar bli inaktuell.

> [!NOTE]
> De här ändringarna sprida aldrig. Det enda sättet att återställa i det här scenariot är att skapa gruppen synkronisera igen.

#### <a name="recommendation"></a>Rekommendation

Övervaka hälsan för synkronisering grupp och databasen regelbundet via Företagsportalen och logga gränssnitt.


## <a name="maintenance"></a>Underhåll

### <a name="avoid-out-of-date-databases-and-sync-groups"></a>Undvika inaktuella databaser och synkronisera grupper

En sync-grupp eller en databas i en grupp för synkronisering kan bli inaktuell. När en synkronisering Gruppstatus är **inaktuella**, slutar att fungera. När en databas status är **inaktuella**, data kan gå förlorade. Det är bäst att undvika det här scenariot istället för att försöka återställa från den.

#### <a name="avoid-out-of-date-databases"></a>Undvika inaktuella databaser

En databas har statusen **inaktuella** när det har varit offline i 45 dagar eller mer. Att undvika ett **inaktuella** status på en databas, kontrollera att ingen av databaserna är offline i 45 dagar eller mer.

#### <a name="avoid-out-of-date-sync-groups"></a>Undvika inaktuella synkroniseringsgrupper

En sync-grupp har statusen **inaktuella** när ändringar i gruppen synkronisering inte spridas till resten av gruppen synkronisering i 45 dagar eller mer. Att undvika ett **inaktuella** status på en grupp för synkronisering, regelbundet kontrollera gruppen sync-händelseloggen. Se till att alla konflikter löses och att har ändringar i hela synkronisera grupp databaser.

En grupp för synkronisering kanske inte kan tillämpa en ändring för något av följande skäl:

-   Schemainkompatibilitet mellan tabeller.
-   Data inkompatibilitet mellan tabeller.
-   Infoga en rad med ett null-värde i en kolumn som inte tillåter null-värden.
-   Uppdatera en rad med ett värde som bryter mot villkoret för en sekundärnyckel.

För att förhindra inaktuella synkroniseringsgrupper:

-   Uppdatera schemat så att de värden som finns i de felaktiga raderna.
-   Uppdatera sekundärnyckelvärden för att inkludera de värden som finns i de felaktiga raderna.
-   Uppdatera datavärdena i raden misslyckade så att de är kompatibla med det schema eller den externa nycklar i måldatabasen.

### <a name="avoid-deprovisioning-issues"></a>Undvika avetablering problem

I vissa fall kan det orsaka synkronisering misslyckas att avregistrera en databas med en klientagent.

#### <a name="scenario"></a>Scenario

1. Synkronisera grupp A har skapats med hjälp av en instans av SQL-databas och en lokal SQL Server-databas som är kopplat till lokal agent 1.
2. Samma lokala databas har registrerats med lokal agent 2 (den här agenten inte är associerad med någon synkronisering grupp).
3. Avregistrerar den lokala databasen från lokal agent 2 tar bort spårning och meta tabeller för synkronisera grupp A för den lokala databasen.
4. Synkronisera gruppera en operations-misslyckas med felet: ”den aktuella åtgärden kunde inte slutföras eftersom databasen inte har etablerats för synkronisering eller du har inte behörighet att synkronisera configuration tabeller”.

#### <a name="solution"></a>Lösning

För att undvika det här scenariot kan inte registrera en databas med mer än en agent.

För att återställa från det här scenariot:

1. Ta bort databasen från varje sync-grupp som den tillhör.  
2. Lägga till databasen i varje sync-grupp som du har tagit bort den från igen.  
3. Distribuera varje berörda sync-grupp (den här åtgärden etablerar databasen).  

### <a name="modifying-your-sync-group"></a>Ändra en grupp för synkronisering

Försök inte att ta bort en databas från en grupp för synkronisering och sedan redigera gruppen sync utan att först distribuera en av ändringarna.

I stället först ta bort en databas från en grupp för synkronisering. Sedan distribuerar ändringen och vänta tills borttagning för att avsluta. När avetablering är klar kan du redigera gruppen synkronisera och distribuera ändringarna.

Om du försöker ta bort en databas och sedan redigera en sync-grupp utan att först distribuera en ändringar misslyckas en eller den andra åtgärden. Gränssnittet portal kan bli inkonsekvent. Om det händer, uppdatera sidan om du vill återställa rätt tillstånd.

## <a name="next-steps"></a>Nästa steg
Mer information om SQL-datasynkronisering (förhandsgranskning) finns i:

-   [Synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering (förhandsgranskning)](sql-database-sync-data.md)
-   [Konfigurera Azure SQL datasynkronisering (förhandsgranskning)](sql-database-get-started-sql-data-sync.md)
-   [Övervaka Azure SQL-datasynkronisering (förhandsversion) med OMS logganalys](sql-database-sync-monitor-oms.md)
-   [Felsökning av problem med Azure SQL-datasynkronisering (förhandsgranskning)](sql-database-troubleshoot-data-sync.md)  
-   Slutför PowerShell-exempel som visar hur du konfigurerar SQL-datasynkronisering (förhandsversion):  
    -   [Använd PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Hämta SQL datasynkronisering (förhandsgranskning) REST API-dokumentation](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)  

Mer information om SQL-databas finns i:

-   [Översikt över SQL-databas](sql-database-technical-overview.md)
-   [Livscykelhantering för databasen](https://msdn.microsoft.com/library/jj907294.aspx)
