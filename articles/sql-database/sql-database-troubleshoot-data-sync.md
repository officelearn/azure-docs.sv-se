---
title: Felsöka SQL Data Sync
description: Lär dig hur du felsöker vanliga problem med Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: d6ea604446cb9d56bb699685d24c81992bcac3a2
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382898"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Felsöka problem med SQL Data Sync

I den här artikeln beskrivs felsÃ¶kning av kända problem med Azure SQL Data Sync. Om det finns en lösning på ett problem, det finns här.

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync har **inte** stöd för Azure SQL Database Managed Instance just nu.

## <a name="sync-issues"></a>Synkronisera problem

- [Synkroniseringen misslyckas i portalgränssnittet för lokala databaser som är associerade med klientagenten](#sync-fails)

- [Min synkroniseringsgrupp har fastnat i bearbetningstillståndet](#sync-stuck)

- [Jag ser felaktiga data i tabellerna](#sync-baddata)

- [Jag ser inkonsekventa primärnyckeldata efter en lyckad synkronisering](#sync-pkdata)

- [Jag ser en betydande försämring av prestanda](#sync-perf)

- [Jag ser det här meddelandet: "Det \<går inte att infoga värdet NULL i kolumnkolumnen>. Kolumnen tillåter inte nulls." Vad betyder detta, och hur kan jag fixa det?](#sync-nulls)

- [Hur hanterar Data Sync cirkelreferenser? Det vill säga när samma data synkroniseras i flera synkroniseringsgrupper och fortsätter att förändras som ett resultat?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a>Synkroniseringen misslyckas i portalgränssnittet för lokala databaser som är associerade med klientagenten

Synkroniseringen misslyckas i SQL Data Sync-portalens användargränssnitt för lokala databaser som är associerade med klientagenten. På den lokala datorn som kör agenten visas System.IO.IOException-fel i händelseloggen. Felen säger att disken har otillräckligt utrymme.

- **Orsak**. Enheten har inte tillräckligt med utrymme.

- **Upplösning**. Skapa mer utrymme på enheten där katalogen %TEMP% finns.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a>Min synkroniseringsgrupp har fastnat i bearbetningstillståndet

En synkroniseringsgrupp i SQL Data Sync har varit i bearbetningstillstånd under lång tid. Det svarar inte på **stoppkommandot** och loggarna visar inga nya poster.

Något av följande villkor kan leda till att en synkroniseringsgrupp fastnar i bearbetningstillståndet:

- **Orsak**. Klientagenten är offline

- **Upplösning**. Se till att klientagenten är online och försök sedan igen.

- **Orsak**. Klientagenten är avinstallerad eller saknas.

- **Upplösning**. Om klientagenten är avinstallerad eller saknas:

    1. Ta bort agentens XML-fil från installationsmappen för SQL Data Sync, om filen finns.
    1. Installera agenten på en lokal dator (det kan vara samma eller en annan dator). Skicka agentnyckeln som genereras i portalen för den agent som visas som offline.

- **Orsak**. SQL Data Sync-tjänsten har stoppats.

- **Upplösning**. Starta om SQL Data Sync-tjänsten.

    1. Sök efter **Tjänster**på **Start-menyn** .
    1. Välj **Tjänster**i sökresultaten .
    1. Leta reda på sql **data sync-tjänsten.**
    1. Om tjänststatusen är **Stoppad**högerklickar du på tjänstnamnet och väljer sedan **Start**.

> [!NOTE]
> Om den föregående informationen inte flyttar synkroniseringsgruppen från bearbetningstillståndet kan Microsoft Support återställa synkroniseringsgruppens status. Om du vill att din synkroniseringsgrupp status återställas, i [Azure SQL Database forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), skapa ett inlägg. I inlägget inkludera du ditt prenumerations-ID och synkroniseringsgrupp-ID för den grupp som behöver återställas. En Microsoft Support-tekniker svarar på ditt inlägg och meddelar dig när statusen har återställts.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a>Jag ser felaktiga data i tabellerna

Om tabeller som har samma namn men som kommer från olika databasscheman inkluderas i en synkronisering visas felaktiga data i tabellerna efter synkroniseringen.

- **Orsak**. Etableringsprocessen för SQL Data Sync använder samma spårningstabeller för tabeller som har samma namn men som finns i olika scheman. På grund av detta återspeglas ändringar från båda tabellerna i samma spårningstabell. Detta medför felaktiga dataändringar under synkroniseringen.

- **Upplösning**. Kontrollera att namnen på tabeller som är involverade i en synkronisering är olika, även om tabellerna tillhör olika scheman i en databas.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a>Jag ser inkonsekventa primärnyckeldata efter en lyckad synkronisering

En synkronisering rapporteras som lyckad och loggen visar inga misslyckade eller överhoppade rader, men du observerar att primärnyckeldata är inkonsekventa mellan databaserna i synkroniseringsgruppen.

- **Orsak**. Detta resultat är avsiktligt. Ändringar i en primärnyckelkolumn resulterar i inkonsekventa data på raderna där primärnyckeln ändrades.

- **Upplösning**. För att förhindra det här problemet, se till att inga data i en primärnyckelkolumn ändras. Om du vill åtgärda problemet när det har inträffat tar du bort raden som innehåller inkonsekventa data från alla slutpunkter i synkroniseringsgruppen. Sätt sedan tillbaka raden.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a>Jag ser en betydande försämring av prestanda

Din prestanda försämras avsevärt, eventuellt till den punkt där du inte ens kan öppna datasynkroniseringsgränssnittet.

- **Orsak**. Den troligaste orsaken är en synkroniseringsloop. En synkroniseringsloop uppstår när en synkronisering av synkroniseringsgrupp A utlöser en synkronisering efter synkroniseringsgrupp B, som sedan utlöser en synkronisering efter synkroniseringsgrupp A. Den faktiska situationen kan vara mer komplex och det kan omfatta mer än två synkroniseringsgrupper i loopen. Problemet är att det finns en cirkulär utlösande av synkronisering som orsakas av synkroniseringsgrupper överlappande varandra.

- **Upplösning**. Den bästa lösningen är förebyggande. Se till att du inte har cirkelreferenser i synkroniseringsgrupperna. Alla rader som synkroniseras av en synkroniseringsgrupp kan inte synkroniseras av en annan synkroniseringsgrupp.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a>Jag ser det här meddelandet: "Det \<går inte att infoga värdet NULL i kolumnkolumnen>. Kolumnen tillåter inte nulls." Vad betyder detta, och hur kan jag fixa det? 
Det här felmeddelandet anger att ett av följande två problem har uppstått:
-  En tabell har ingen primärnyckel. Lös problemet genom att lägga till en primärnyckel i alla tabeller som du synkroniserar.
-  Det finns en WHERE-sats i create index-satsen. Datasynkronisering hanterar inte det här villkoret. Lös problemet genom att ta bort WHERE-satsen eller manuellt göra ändringarna i alla databaser. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a>Hur hanterar Data Sync cirkelreferenser? Det vill säga när samma data synkroniseras i flera synkroniseringsgrupper och fortsätter att förändras som ett resultat?
Datasynkronisering hanterar inte cirkelreferenser. Var noga med att undvika dem. 

## <a name="client-agent-issues"></a>Problem med klientagenten

Information om felsökning av problem med klientagenten finns [i Felsöka problem med datasynkroniseringsagenten](sql-database-data-sync-agent.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Problem med installation och underhåll

- [Jag får ett meddelande om att det är på utrymme](#setup-space)

- [Jag kan inte ta bort min synkroniseringsgrupp](#setup-delete)

- [Jag kan inte avregistrera en lokal SQL Server-databas](#setup-unreg)

- [Jag har inte tillräckliga privilegier för att starta systemtjänster](#setup-perms)

- [En databas har statusen "Inaktuella"](#setup-date)

- [En synkroniseringsgrupp har statusen "Inaktuella"](#setup-date2)

- [Det går inte att ta bort en synkroniseringsgrupp inom tre minuter efter att agenten avinstallerats eller stoppats](#setup-delete2)

- [Vad händer när jag återställer en förlorad eller skadad databas?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a>Jag får ett meddelande om att det är på utrymme

- **Orsak**. Meddelandet "disk av utrymme" kan visas om överblivna filer måste tas bort. Detta kan bero på antivirusprogram eller så är filer öppna när du försöker ta bort åtgärder.

- **Upplösning**. Ta bort synkroniseringsfilerna manuellt i mappen`del \*sync\* /s`%temp% ( ). Ta sedan bort underkatalogerna i mappen %temp%.

> [!IMPORTANT]
> Ta inte bort några filer medan synkronisering pågår.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a>Jag kan inte ta bort min synkroniseringsgrupp

Det går inte att ta bort en synkroniseringsgrupp. Något av följande scenarier kan leda till att en synkroniseringsgrupp inte tas bort:

- **Orsak**. Klientagenten är offline.

- **Upplösning**. Kontrollera att klientagenten är online och försök sedan igen.

- **Orsak**. Klientagenten är avinstallerad eller saknas.

- **Upplösning**. Om klientagenten är avinstallerad eller saknas:  
    a. Ta bort agentens XML-fil från installationsmappen för SQL Data Sync, om filen finns.  
    b. Installera agenten på en lokal dator (det kan vara samma eller en annan dator). Skicka agentnyckeln som genereras i portalen för den agent som visas som offline.

- **Orsak**. En databas är offline.

- **Upplösning**. Kontrollera att dina SQL-databaser och SQL Server-databaser är online.

- **Orsak**. Synkroniseringsgruppen etablerar eller synkroniserar.

- **Upplösning**. Vänta tills etablerings- eller synkroniseringsprocessen är klar och försök sedan ta bort synkroniseringsgruppen igen.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a><a name="setup-unreg"></a>Jag kan inte avregistrera en lokal SQL Server-databas

- **Orsak**. Troligtvis försöker du avregistrera en databas som redan har tagits bort.

- **Upplösning**. Om du vill avregistrera en lokal SQL Server-databas markerar du databasen och väljer sedan **Tvinga bort**.

  Om den här åtgärden inte tar bort databasen från synkroniseringsgruppen:

  1. Stoppa och starta om klientagentens värdtjänst:  
    a. Välj **Start-menyn.**  
    b. Ange **services.msc**i sökrutan.  
    c. Dubbelklicka på **Tjänster**i avsnittet **Program** i sökresultatfönstret .  
    d. Högerklicka på **SQL Data** Sync-tjänsten.  
    e. Om tjänsten körs, stoppa den.  
    f. Högerklicka på tjänsten och välj sedan **Start**.  
    g. Kontrollera om databasen fortfarande är registrerad. Om det inte längre är registrerat, är du klar. Annars fortsätter du med nästa steg.
  1. Öppna klientagentappen (SqlAzureDataSyncAgent).
  1. Välj **Redigera autentiseringsuppgifter**och ange sedan autentiseringsuppgifterna för databasen.
  1. Fortsätt med unregistration.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a>Jag har inte tillräckliga privilegier för att starta systemtjänster

- **Orsak**. Det här felet uppstår i två situationer:
  -   Användarnamnet och/eller lösenordet är felaktiga.
  -   Det angivna användarkontot har inte tillräcklig behörighet för att logga in som en tjänst.

- **Upplösning**. Bevilja inloggningsuppgifter för inloggning som en tjänst till användarkontot:

  1. Gå till **Starta** > **administrationsverktyg** > **på Kontrollpanelen** > **Lokal säkerhetsprincip** > **Lokal princip** > **Hantering av användarrättigheter**.
  1. Välj **Logga in som en tjänst**.
  1. Lägg till användarkontot i dialogrutan **Egenskaper.**
  1. Tryck på **Tillämpa** och välj sedan **OK**.
  1. Stäng alla fönster.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a>En databas har statusen "Inaktuella"

- **Orsak**. SQL Data Sync tar bort databaser som har varit offline från tjänsten i 45 dagar eller mer (räknat från den tidpunkt då databasen gick offline). Om en databas är offline i 45 dagar eller mer och sedan kommer tillbaka online är dess status **inaktuella**.

- **Upplösning**. Du kan undvika en **insåld** status genom att se till att ingen av databaserna är offline i 45 dagar eller mer.

  Om en databass status är **inaktuella:**

  1. Ta bort databasen som har en **instränlig** status från synkroniseringsgruppen.
  1. Lägg till databasen i synkroniseringsgruppen igen.

  > [!WARNING]
  > Du förlorar alla ändringar som gjorts i den här databasen när den var offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a>En synkroniseringsgrupp har statusen "Inaktuella"

- **Orsak**. Om en eller flera ändringar inte kan gälla för hela kvarhållningsperioden på 45 dagar kan en synkroniseringsgrupp bli inaktuell.

- **Upplösning**. Om du vill undvika en **instränt** status för en synkroniseringsgrupp undersöker du regelbundet resultatet av synkroniseringsjobben i historikvisaren. Undersök och lös alla ändringar som inte kan tillämpas.

  Om statusen för en synkroniseringsgrupp är **instrårsprrättning**tar du bort synkroniseringsgruppen och återskapar den.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a>Det går inte att ta bort en synkroniseringsgrupp inom tre minuter efter att agenten avinstallerats eller stoppats

Du kan inte ta bort en synkroniseringsgrupp inom tre minuter efter att du avinstallerat eller stoppat den associerade SQL Data Sync-klientagenten.

- **Upplösning**.

  1. Ta bort en synkroniseringsgrupp medan de associerade synkroniseringsagenterna är online (rekommenderas).
  1. Om agenten är offline men är installerad tar du med den online på den lokala datorn. Vänta tills agentens status visas som **Online** i SQL Data Sync-portalen. Ta sedan bort synkroniseringsgruppen.
  1. Om agenten är offline eftersom den avinstallerades:  
    a.  Ta bort agentens XML-fil från installationsmappen för SQL Data Sync, om filen finns.  
    b.  Installera agenten på en lokal dator (det kan vara samma eller en annan dator). Skicka agentnyckeln som genereras i portalen för den agent som visas som offline.  
    c. Försök att ta bort synkroniseringsgruppen.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a>Vad händer när jag återställer en förlorad eller skadad databas?

Om du återställer en förlorad eller skadad databas från en säkerhetskopia kan det finnas en icke-konvergens av data i de synkroniseringsgrupper som databasen tillhör.

## <a name="next-steps"></a>Nästa steg
Mer information om SQL Data Sync finns i:

-   Översikt – [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
-   Konfigurera Data Sync
    - I portalen – [Självstudie: Konfigurera SQL Data Sync att synkronisera data mellan Azure SQL Database och SQL Server lokalt](sql-database-get-started-sql-data-sync.md)
    - Med PowerShell
        -  [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Använd PowerShell för att synkronisera mellan en Azure SQL-databas och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Metodtips – [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Övervaka - [Övervaka SQL Data Sync med Azure Monitor-loggar](sql-database-sync-monitor-oms.md)
-   Uppdatera synkroniseringsschemat
    -   Med Transact-SQL – [Automatisera replikeringen av schemaändringar i Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Med PowerShell – [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)

Mer information om SQL Database finns i:

-   [Översikt över SQL Database](sql-database-technical-overview.md)
-   [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
