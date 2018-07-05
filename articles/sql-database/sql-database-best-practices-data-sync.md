---
title: Metodtips för Azure SQL Data Sync | Microsoft Docs
description: Läs mer om bästa praxis för att konfigurera och köra Azure SQL Data Sync.
services: sql-database
ms.date: 07/03/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
manager: craigg
ms.openlocfilehash: c8b8455dac9aa1a9f7747cada4ce85644162e331
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445168"
---
# <a name="best-practices-for-sql-data-sync"></a>Regelverk för SQL Data Sync 

Den här artikeln beskriver Metodtips för Azure SQL Data Sync.

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md).

## <a name="security-and-reliability"></a> Säkerhet och tillförlitlighet

### <a name="client-agent"></a>Klientagenten

-   Installera klientagenten med lägsta möjliga användarkonto som har tjänsten för nätverksåtkomst.  
-   Installera klientagenten på en dator som inte är en lokal SQL Server-datorn.  
-   Inte registrera en lokal databas med mer än en agent.    
    -   Undvik detta även om du synkroniserar olika tabeller för olika synkroniseringsgrupper.  
    -   Registrera en lokal databas med flera klienten agenter utgör utmaningar när du tar bort en av synkroniseringsgrupper.

### <a name="database-accounts-with-least-required-privileges"></a>Databaskonton med lägsta behörighet

-   **För synkroniseringsinställningen**. Skapa/ändra tabellen. ALTER databas. Skapa proceduren; Välj / Alter Schema; Skapa användardefinierade typer.

-   **För pågående synkronisering**. Välj / Infoga / Uppdatera / ta bort för tabeller som har valts för synkronisering och på synka metadata och spåra tabeller. Körbehörighet om lagrade procedurer som skapats av tjänsten. Körbehörighet på användardefinierade tabelltyper.

-   **För avetableringar**. Ändra på tabeller som en del av synkronisering; Välj / Ta bort på Synkronisera metadata-tabeller. Styra vid synkronisering spåra tabeller, lagrade procedurer och användardefinierade typer.

Azure SQL Database stöder bara en enda uppsättning autentiseringsuppgifter. Överväg följande alternativ för att utföra dessa uppgifter i den här begränsningen:

-   Ändra autentiseringsuppgifterna för olika faser (till exempel *credentials1* för installation och *credentials2* för pågående).  
-   Ändra behörighet av autentiseringsuppgifter (dvs, ändra behörighet när synkronisering har konfigurerats).

## <a name="setup"></a>Konfiguration

### <a name="database-considerations-and-constraints"></a> Databasen överväganden och begränsningar

#### <a name="sql-database-instance-size"></a>SQL Database-instansstorlek

När du skapar en ny SQL Database-instans, ange den maximala storleken så att den alltid är större än databasen som du distribuerar. Om du inte anger den maximala storleken till större än den distribuerade databasen, misslyckas synkronisering. Även om automatisk ökning inte omfattas av SQL Data Sync, kan du köra den `ALTER DATABASE` kommando för att öka storleken på databasen när den har skapats. Se till att du håller dig inom storleksgränser för SQL Database-instans.

> [!IMPORTANT]
> SQL Data Sync lagrar ytterligare metadata med varje databas. Se till att du ta hänsyn till dessa metadata när du beräknar utrymmet som krävs. Mängden ytterligare kostnader som är relaterad till bredden på tabellerna (till exempel smal tabeller kräver mer resurser) och mängden trafik.

### <a name="table-considerations-and-constraints"></a> Tabellen överväganden och begränsningar

#### <a name="selecting-tables"></a>Om du väljer register

Du behöver att inkludera alla tabeller som finns i en databas i en synkroniseringsgrupp. Tabeller som inkluderas i en synkroniseringsgrupp påverkar effektiviteten och kostnader. Innehålla tabeller och de tabeller som de är beroende av, i en synkroniseringsgrupp endast om affärsbehov kräver detta.

#### <a name="primary-keys"></a>Primärnycklar

Varje tabell i en synkroniseringsgrupp måste ha en primärnyckel. SQL Data Sync-tjänsten kan inte synkronisera en tabell som inte har en primärnyckel.

Innan du använder SQL Data Sync i produktion, prestandatestning inledande och pågående synkronisering.

### <a name="provisioning-destination-databases"></a> Etablering av mål-databaser

SQL Data Sync ger autoprovisioning basic-databas.

Det här avsnittet beskriver begränsningar för etablering i SQL Data Sync.

#### <a name="autoprovisioning-limitations"></a>Autoprovisioning begränsningar

SQL Data Sync har följande begränsningar på autoprovisioning:

-   Markera de kolumner som skapas i tabellen.  
    Alla kolumner som inte ingår i synkroniseringsgruppen är inte etablerad i måltabeller.
-   Index skapas endast för de markerade kolumnerna.  
    Om Tabellindex källa har kolumner som inte ingår i synkroniseringsgruppen, är inte dessa index etablerad i måltabeller.  
-   Index för kolumner av typen XML är inte etablerat.  
-   Kontrollbegränsningar etablerats inte.  
-   Befintliga utlösare källtabellerna etablerats inte.  
-   Vyer och lagrade procedurer skapas inte till måldatabasen.
-   PÅ UPDATE CASCADE och ON DELETE CASCADE återskapas inte åtgärder på sekundärnyckelrestriktioner i måltabellerna.

#### <a name="recommendations"></a>Rekommendationer

-   Använda SQL Data Sync autoprovisioning funktionen endast när du provar på att använda tjänsten.  
-   Etablera databasschemat för produktion.

### <a name="locate-hub"></a> Var du vill placera hubb-databasen

#### <a name="enterprise-to-cloud-scenario"></a>Scenario för företag till molnet

Behåll hubbdatabasen nära största koncentrationen av synkroniseringsgruppen databastrafik minska svarstiden.

#### <a name="cloud-to-cloud-scenario"></a>Moln-till-moln-scenario

-   När alla databaser i en synkroniseringsgrupp är i ett datacenter, måste hubben finnas i samma datacenter. Den här konfigurationen minskar svarstiden och kostnaden för dataöverföring mellan datacenter.
-   När databaser i en synkroniseringsgrupp finns i flera datacenter, måste hubben finnas i samma datacenter som flesta av de databaser och databastrafik.

#### <a name="mixed-scenarios"></a>Blandade scenarier

Gäller ovanstående riktlinjer komplexa synkronisering gruppkonfigurationer, till exempel de som är en blandning av enterprise-till-moln-och molnet till molnet.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> Undvika långsamma och kostsamma inledande synkronisering

I det här avsnittet diskuterar vi hur den första synkroniseringen av en synkroniseringsgrupp. Lär dig hur du kan förhindra en initial synkronisering från tar längre tid och är dyrare än nödvändigt.

#### <a name="how-initial-sync-works"></a>Hur den första synkroniseringen fungerar

När du skapar en synkroniseringsgrupp kan du börja med data i endast en databas. Om du har data i flera databaser, behandlar varje rad i SQL Data Sync som en konflikt som måste lösas. Den här konfliktlösning gör den inledande synkroniseringen att gå långsamt. Om du har data i flera databaser, ta den inledande synkroniseringen mellan flera dagar och flera månader, beroende på databasens storlek.

Om databaserna är i olika datacenter, måste varje rad åka mellan olika datacenter. Detta ökar kostnaden för en inledande synkronisering.

#### <a name="recommendation"></a>Rekommendation

Starta om det är möjligt med data i endast en av synkroniseringsgruppen databaser.

### <a name="design-to-avoid-synchronization-loops"></a> Design för att undvika slingor för synkronisering

En synkronisering loop uppstår vid cirkelreferenser inom en synkroniseringsgrupp. I det här scenariot kan replikeras varje ändring i en databas oändlighet och cirkulärt via databaserna i synkroniseringsgruppen.   

Se till att du undviker att synkronisera slingor, eftersom de orsakar försämrade prestanda och kan ta avsevärt längre kostnader.

### <a name="handling-changes-that-fail-to-propagate"></a> Ändringar som inte spridits

#### <a name="reasons-that-changes-fail-to-propagate"></a>Det gick inte att sprida orsaker som ändras

Ändringar kan misslyckas att sprida för någon av följande orsaker:

-   Schemat/datatype inkompatibilitet.
-   Infoga null i icke-nullbara kolumner.
-   Brott mot sekundärnyckelbegränsningar.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Vad händer när det gick inte att sprida ändringarna?

-   Synkronisera gruppen visar att den är i en **varning** tillstånd.
-   Information finns i Loggvisaren för portalens användargränssnitt.
-   Om problemet kvarstår 45 dagar blir databasen inaktuell.

> [!NOTE]
> Dessa ändringar att aldrig spridas. Det enda sättet att återställa i det här scenariot är att skapa synkroniseringsgruppen.

#### <a name="recommendation"></a>Rekommendation

Övervaka hälsan för synkronisering grupp och databasen regelbundet via gränssnittet och logga.


## <a name="maintenance"></a>Underhåll

### <a name="avoid-out-of-date-databases-and-sync-groups"></a> Undvik att gamla databaser och synkronisera grupper

En synkroniseringsgrupp eller en databas i en synkroniseringsgrupp kan bli inaktuell. När en synkroniseringsgrupp statusen är **inaktuella**, den slutar fungera. När statusen för en databas är **inaktuella**, data kan gå förlorade. Det är bäst att undvika det här scenariot istället för att försöka att återställa från kontot.

#### <a name="avoid-out-of-date-databases"></a>Undvik att gamla databaser

En databas har statusen **inaktuella** när det har varit offline under 45 dagar eller mer. Att undvika ett **inaktuella** status på en databas, se till att ingen av databaserna är offline i 45 dagar eller mer.

#### <a name="avoid-out-of-date-sync-groups"></a>Undvik att gamla synkroniseringsgrupper

En synkroniseringsgrupp status anges **inaktuella** när ändringar i synkroniseringsgruppen inte gick att spridas till resten av synkroniseringsgruppen i 45 dagar eller mer. Att undvika ett **inaktuella** status på en synkroniseringsgrupp regelbundet kontrollera händelseloggen för synkroniseringsgruppen. Kontrollera att alla konflikter löses, och att ändringarna har spridits i hela synkronisera grupp databaser.

En synkroniseringsgrupp misslyckas med att tillämpa en ändring för något av följande skäl:

-   Schemainkompatibilitet mellan tabeller.
-   Data inkompatibilitet mellan tabeller.
-   Infoga en rad med ett null-värde i en kolumn som inte tillåter null-värden.
-   Uppdaterar en rad med ett värde som bryter mot en sekundärnyckelbegränsning.

Att förhindra att inaktuella synkroniseringsgrupper:

-   Uppdatera schemat så att de värden som finns i de misslyckade raderna.
-   Uppdatera sekundärnyckelvärden för att inkludera de värden som finns i de misslyckade raderna.
-   Uppdatera datavärdena i den misslyckade raden så att de är kompatibla med schema eller sekundärnycklar i måldatabasen.

### <a name="avoid-deprovisioning-issues"></a> Undvika avetablering problem

I vissa fall kan det orsaka synkronisering misslyckas att avregistrera en databas med en klientagent.

#### <a name="scenario"></a>Scenario

1. Synkroniseringsgruppen A har skapats med hjälp av en SQL-databasinstans och en lokal SQL Server-databas som är associerad med lokal agent 1.
2. Samma lokala databas har registrerats med lokal agent 2 (den här agenten inte är kopplad till någon synkroniseringsgruppen).
3. Avregistrera en lokal databas från lokal agent 2 tar bort spårning och meta tabeller för synkronisera grupp A för den lokala databasen.
4. Synkronisera gruppera en operations-misslyckas med felet: ”den aktuella åtgärden kunde inte slutföras eftersom databasen inte har etablerats för synkronisering eller du har inte behörighet att synkronisera configuration tabeller”.

#### <a name="solution"></a>Lösning

För att undvika det här scenariot kan inte registrera en databas med mer än en agent.

Återställa från det här scenariot:

1. Ta bort databasen från varje synkroniseringsgrupp som den tillhör.  
2. Lägga till databasen i varje synkroniseringsgrupp som du har tagit bort den från igen.  
3. Distribuera varje berörda synkroniseringsgruppen (den här åtgärden etableras databasen).  

### <a name="modifying-your-sync-group"></a> Ändra en synkroniseringsgrupp

Försök inte att ta bort en databas från en synkroniseringsgrupp och sedan redigera synkroniseringsgruppen utan att distribuera förstnämnda ändringar.

I stället först ta bort en databas från en synkroniseringsgrupp. Sedan distribuerar ändringen och vänta tills avetablering för att slutföra. När avetablering är klar kan du redigera synkroniseringsgruppen och distribuera ändringarna.

Om du försöker ta bort en databas och sedan redigera en synkroniseringsgrupp utan att distribuera förstnämnda ändringar, misslyckas en eller den andra åtgärden. Portalgränssnittet kan bli inkonsekvent. Om det händer kan du uppdatera sidan om du vill återställa rätt tillstånd.

## <a name="next-steps"></a>Nästa steg
Mer information om SQL Data Sync finns:

-   [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
-   [Konfigurera Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)
-   [Övervaka Azure SQL Data Sync med Log Analytics](sql-database-sync-monitor-oms.md)
-   [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)  
-   Slutför PowerShell-exempel som visar hur du konfigurerar SQL Data Sync:  
    -   [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Ladda ned REST API-dokumentation för SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)  

Mer information om SQL-databas finns:

-   [Översikt över SQL Database](sql-database-technical-overview.md)
-   [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
