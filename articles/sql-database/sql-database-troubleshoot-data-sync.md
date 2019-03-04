---
title: Felsöka Azure SQL Data Sync | Microsoft Docs
description: Lär dig hur du felsöker vanliga problem med Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 4ab26891a98418fae75c3be18f17f1d8e02d8b36
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216721"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Felsöka problem med SQL Data Sync

Den här artikeln beskriver hur du felsöker kända problem med Azure SQL Data Sync. Om det finns en lösning på problemet, finns den här.

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync har **inte** stöd för Azure SQL Database Managed Instance just nu.

## <a name="sync-issues"></a>Synkroniseringsproblem

- [Synkroniseringen misslyckas i portalens användargränssnitt för lokala databaser som är associerade med klientens agent](#sync-fails)

- [Min synkroniseringsgruppen fastnar i tillståndet för bearbetning](#sync-stuck)

- [Kan jag se felaktiga data i Mina tabeller](#sync-baddata)

- [Inkonsekventa data av den primära nyckeln visas efter en lyckad synkronisering](#sync-pkdata)

- [Jag ser betydande försämrade prestanda](#sync-perf)

- [Jag ser följande meddelande: ”Det går inte att infoga värdet NULL i kolumnen <column>. Kolumnen tillåter inte null-värden ”. Vad betyder detta och hur kan jag åtgärda det?](#sync-nulls)

- [Hur hanterar datasynkronisering cirkelreferenser? Det vill säga när samma data synkroniseras i flera synkroniseringsgrupper och ändras som ett resultat?](#sync-circ)

### <a name="sync-fails"></a> Synkroniseringen misslyckas i portalens användargränssnitt för lokala databaser som är associerade med klientens agent

Det går inte att synkronisera i SQL Data Sync-portalens användargränssnitt för lokala databaser som är associerade med klientens agent. På den lokala datorn som kör agenten, visas System.IO.IOException fel i händelseloggen. Fel som säger att det finns inte tillräckligt med utrymme på disken.

- **Orsak**. Enheten har inte tillräckligt med utrymme.

- **Lösning**. Skapa mer utrymme på enheten där katalogen % TEMP % finns.

### <a name="sync-stuck"></a> Min synkroniseringsgruppen fastnar i tillståndet för bearbetning

En synkroniseringsgrupp i SQL Data Sync har varit i tillståndet bearbetning under en längre tid. De inte svara på den **stoppa** kommandot och loggarna visar inga nya poster.

Något av följande villkor kan resultera i en synkroniseringsgrupp som fastnat i bearbetning tillstånd:

- **Orsak**. Klientagenten är offline

- **Lösning**. Se till att klientagenten är online och försök sedan igen.

- **Orsak**. Klientagenten är avinstallerad eller saknas.

- **Lösning**. Om klientagenten är avinstallerad eller saknas:

    1. Ta bort agentens XML-fil från installationsmappen för SQL Data Sync, om filen finns.
    1. Installera agenten på en lokal dator (det kan vara samma eller en annan dator). Skicka agentnyckeln som genereras i portalen för den agent som visas som offline.

- **Orsak**. SQL Data Sync-tjänsten har stoppats.

- **Lösning**. Starta om SQL Data Sync-tjänsten.

    1. I den **starta** -menyn, Sök efter **Services**.
    1. I sökresultaten väljer **Services**.
    1. Hitta den **SQL Data Sync** service.
    1. Om status för tjänsten är **stoppad**, högerklicka på tjänstenamnet och välj sedan **starta**.

> [!NOTE]
> Om informationen inte flytta din synkroniseringsgruppen ur tillståndet för bearbetning, kan Microsoft Support återställa statusen för din synkroniseringsgrupp. Ha din grupp synkroniseringsstatus återställa, i den [Azure SQL Database-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), skapa ett inlägg. I inlägget, inkludera ditt prenumerations-ID och synkronisera grupp-ID för gruppen som ska återställas. En Microsoft Support-tekniker svarar på ditt inlägg och meddelar dig när status har återställts.

### <a name="sync-baddata"></a> Kan jag se felaktiga data i Mina tabeller

Om tabeller som har samma namn men som kommer från olika databasscheman ingår i en synkronisering, visas felaktiga data i tabeller efter synkroniseringen.

- **Orsak**. SQL-datasynkronisering etableringsprocessen använder samma spårningstabeller för tabeller som har samma namn men befinner sig i olika scheman. Därför återspeglas ändringar från båda tabellerna i tabellen samma uppföljning. Detta leder till felaktig data ändras under synkroniseringen.

- **Lösning**. Kontrollera att namnen på tabellerna som ingår i en synkronisering är olika, även om tabellerna som tillhör olika scheman i en databas.

### <a name="sync-pkdata"></a> Inkonsekventa data av den primära nyckeln visas efter en lyckad synkronisering

En synkronisering har rapporterats som lyckas och loggen visar inga misslyckade eller hoppades över rader, men du märka att primära viktiga data är inkonsekvent mellan databaser i synkroniseringsgruppen.

- **Orsak**. Resultatet är avsiktligt. Ändringar i alla primärnyckelkolumnen leda till inkonsekventa data i rader där den primära nyckeln har ändrats.

- **Lösning**. Se till att inga data i en primärnyckelkolumn ändras för att förhindra det här problemet. Ta bort den rad som har inkonsekventa data från alla slutpunkter i synkroniseringsgruppen för att åtgärda problemet när det har uppstått. Sedan kan återinföra raden.

### <a name="sync-perf"></a> Jag ser betydande försämrade prestanda

Prestanda försämras avsevärt, eventuellt till den punkt där du även inte kan öppna Användargränssnittet för Data Sync.

- **Orsak**. Den mest troliga orsaken är en sync-loop. En loop för synkronisering sker när en synkronisering av synkroniseringsgrupp A utlöser en synkronisering av synkroniseringsgrupp B, som utlöser en synkronisering av synkroniseringsgruppen A. I verkligheten kan vara mer komplexa och det kan handla om fler än två synkroniseringsgrupper loopen. Problemet är att det finns en cirkulär utlösningen av synkronisering som orsakas av synkroniseringsgrupper överlappar varandra.

- **Lösning**. Bästa korrigeringen är skydd. Se till att du inte har cirkelreferenser i din synkroniseringsgrupper. Alla rader som har synkroniserats med en synkroniseringsgrupp kan inte synkroniseras med en annan synkroniseringsgrupp.

### <a name="sync-nulls"></a> Jag ser följande meddelande: ”Det går inte att infoga värdet NULL i kolumnen <column>. Kolumnen tillåter inte null-värden ”. Vad betyder detta och hur kan jag åtgärda det? 
Det här felmeddelandet indikerar att en av de två följande problem har uppstått:
-  En tabell har inte en primärnyckel. Om du vill åtgärda problemet genom att lägga till en primär nyckel till alla tabeller som du synkroniserar.
-  Det finns en WHERE-sats i CREATE INDEX-instruktionen. Datasynkronisering kan inte hantera det här tillståndet. Om du vill åtgärda problemet genom att ta bort WHERE-satsen eller manuellt gör ändringar i alla databaser. 
 
### <a name="sync-circ"></a> Hur hanterar datasynkronisering cirkelreferenser? Det vill säga när samma data synkroniseras i flera synkroniseringsgrupper och ändras som ett resultat?
Datasynkronisering kan inte hantera cirkelreferenser. Tänk på att undvika dem. 

## <a name="client-agent-issues"></a>Agenten klientproblem

Felsökning av problem med klientens agent beskrivs [felsöka Data Sync-agenten skickar](sql-database-data-sync-agent.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Problem med installation och underhåll

- [Jag får ett meddelande om ”disk slut på utrymme”](#setup-space)

- [Jag kan inte radera min synkroniseringsgrupp](#setup-delete)

- [Jag kan inte avregistrera en lokal SQL Server-databas](#setup-unreg)

- [Jag har inte tillräcklig behörighet för att starta systemtjänster](#setup-perms)

- [En databas med statusen ”inaktuell”](#setup-date)

- [En synkroniseringsgrupp med statusen ”inaktuell”](#setup-date2)

- [En synkroniseringsgrupp kan inte tas bort inom tre minuter efter att avinstallera eller stoppa agenten](#setup-delete2)

- [Vad händer när jag återställer en förlorad eller skadad databas?](#setup-restore)

### <a name="setup-space"></a> Jag får ett meddelande om ”disk slut på utrymme”

- **Orsak**. Meddelandet ”disk slut på utrymme” kanske visas om överblivna filer måste tas bort. Detta kan orsakas av ett antivirusprogram eller filer är öppna när ta bort åtgärder kan utföras.

- **Lösning**. Ta bort sync-filer som finns i % temp %-mappen manuellt (`del \*sync\* /s`). Ta sedan bort underkataloger i % temp %-mappen.

> [!IMPORTANT]
> Ta inte bort några filer när synkronisering pågår.

### <a name="setup-delete"></a> Jag kan inte radera min synkroniseringsgrupp

Det går inte att ditt försök att ta bort en synkroniseringsgrupp. Något av följande scenarier kan resultera i misslyckande med att ta bort en synkroniseringsgrupp:

- **Orsak**. Klientagenten är offline.

- **Lösning**. Kontrollera att klientagenten är online och försök sedan igen.

- **Orsak**. Klientagenten är avinstallerad eller saknas.

- **Lösning**. Om klientagenten är avinstallerad eller saknas:  
    a. Ta bort agentens XML-fil från installationsmappen för SQL Data Sync, om filen finns.  
    b. Installera agenten på en lokal dator (det kan vara samma eller en annan dator). Skicka agentnyckeln som genereras i portalen för den agent som visas som offline.

- **Orsak**. En databas är offline.

- **Lösning**. Se till att SQL-databaser och SQL Server-databaser är anslutna.

- **Orsak**. Synkroniseringsgruppen etablering eller synkronisering.

- **Lösning**. Vänta tills processen för etablering eller synkronisering är klar och försök sedan ta bort synkroniseringsgruppen.

### <a name="setup-unreg"></a> Jag kan inte avregistrera en lokal SQL Server-databas

- **Orsak**. Du försöker mest sannolikt att avregistrera en databas som redan har tagits bort.

- **Lösning**. Om du vill avregistrera en lokal SQL Server-databas, väljer du databasen och välj sedan **Framtvinga borttagning av**.

  Om den här åtgärden inte kan ta bort databasen från synkroniseringsgruppen:

  1. Stoppa och starta sedan om klient-Agenttjänsten för värden:  
    a. Välj den **starta** menyn.  
    b. I sökrutan anger **services.msc**.  
    c. I den **program** avsnittet sökning resulterar fönstret dubbelklickar du på **Services**.  
    d. Högerklicka på den **SQL Data Sync** service.  
    e. Om tjänsten körs stoppa den.  
    f. Högerklicka på tjänsten och välj sedan **starta**.  
    g. Kontrollera om databasen fortfarande är registrerad. Om den inte längre är registrerat, är du klar. Annars fortsätter du med nästa steg.
  1. Öppna agenten klientappen (SqlAzureDataSyncAgent).
  1. Välj **redigera autentiseringsuppgifter**, och sedan ange autentiseringsuppgifterna för databasen.
  1. Fortsätt med avregistrering.

### <a name="setup-perms"></a> Jag har inte tillräcklig behörighet för att starta systemtjänster

- **Orsak**. Det här felet uppstår i två situationer:
  -   Användarnamnet eller lösenordet är felaktigt.
  -   Det angivna användarkontot har inte tillräcklig behörighet för att logga in som en tjänst.

- **Lösning**. Bevilja log-på-as-a-service-autentiseringsuppgifter för användarkontot:

  1. Gå till **starta** > **Kontrollpanelen** > **Administrationsverktyg** > **lokal säkerhetsprincip**  >  **Lokal princip** > **användaren Rights Management**.
  1. Välj **logga in som en tjänst**.
  1. I den **egenskaper** dialogrutan lägger du till användarkontot.
  1. Tryck på **Tillämpa** och välj sedan **OK**.
  1. Stäng alla fönster.

### <a name="setup-date"></a> En databas med statusen ”inaktuell”

- **Orsak**. SQL Data Sync tar bort databaser som har varit offline från tjänsten i 45 dagar eller mer (som räknas från det att databasen övergick till offlineläge). Om en databas är offline i 45 dagar eller mer och är online igen, är dess status **inaktuella**.

- **Lösning**. Du kan undvika en **inaktuella** status genom att se till att ingen av dina databaser arbeta offline av 45 dagar eller mer.

  Om status för en databas är **inaktuella**:

  1. Ta bort databasen med en **inaktuella** status från synkroniseringsgruppen.
  1. Lägga till databasen igen i synkroniseringsgruppen.

  > [!WARNING]
  > Du förlorar alla ändringar som gjorts i den här databasen när den var offline.

### <a name="setup-date2"></a> En synkroniseringsgrupp med statusen ”inaktuell”

- **Orsak**. Om en eller flera ändringar inte gäller för hela kvarhållningsperiod på 45 dagar, kan en synkroniseringsgrupp bli inaktuella.

- **Lösning**. Att undvika ett **inaktuella** status för en synkroniseringsgrupp granska resultaten av sync-jobb i visningsprogrammet för historik med jämna mellanrum. Undersöka och lösa eventuella ändringar som inte att användas.

  Om en synkroniseringsgrupp status är **inaktuella**, ta bort synkroniseringsgruppen och skapa den igen.

### <a name="setup-delete2"></a> En synkroniseringsgrupp kan inte tas bort inom tre minuter efter att avinstallera eller stoppa agenten

Du kan inte ta bort en synkroniseringsgrupp inom tre minuter efter att avinstallera eller stoppa den associerade SQL Data Sync-klientagenten.

- **Lösning**.

  1. Ta bort en synkroniseringsgrupp medan de associerade synkroniseringsagenter är online (rekommenderas).
  1. Om agenten är offline men är installerat, kan du ta den online på den lokala datorn. Vänta tills statusen för agenten ska visas som **Online** i SQL Data Sync-portalen. Ta sedan bort synkroniseringsgruppen.
  1. Om agenten är offline eftersom det avinstallerades:  
    a.  Ta bort agentens XML-fil från installationsmappen för SQL Data Sync, om filen finns.  
    b.  Installera agenten på en lokal dator (det kan vara samma eller en annan dator). Skicka agentnyckeln som genereras i portalen för den agent som visas som offline.  
    c. Försök att ta bort synkroniseringsgruppen.

### <a name="setup-restore"></a> Vad händer när jag återställer en förlorad eller skadad databas?

Om du återställer en förlorad eller skadad databas från en säkerhetskopia kan finnas det en bristande konvergens i data i synkroniseringsgrupper som databasen tillhör.

## <a name="next-steps"></a>Nästa steg
Mer information om SQL Data Sync finns:

-   Översikt – [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
-   Konfigurera Data Sync
    - I portalen – [Självstudie: Konfigurera SQL Data Sync för att synkronisera data mellan Azure SQL Database och SQL Server lokalt](sql-database-get-started-sql-data-sync.md)
    - Med PowerShell
        -  [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Metodtips – [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Övervaka – [övervaka SQL Data Sync med Azure Monitor-loggar](sql-database-sync-monitor-oms.md)
-   Uppdatera synkroniseringsschemat
    -   Med Transact-SQL – [Automatisera replikeringen av schemaändringar i Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Med PowerShell – [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)

Mer information om SQL-databas finns:

-   [Översikt över SQL Database](sql-database-technical-overview.md)
-   [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
