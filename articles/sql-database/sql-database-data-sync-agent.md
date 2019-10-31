---
title: Data Sync-agent för Azure SQL Data Sync | Microsoft Docs
description: Lär dig hur du installerar och kör Data Sync-agenten för Azure SQL Data Sync för att synkronisera data med lokala SQL Server-databaser
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
ms.openlocfilehash: 13a59a9b536a25897d7c545b6fb466c1192cb545
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177712"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Data Sync-agent för Azure SQL Data Sync

Synkronisera data med lokala SQL Server-databaser genom att installera och konfigurera Data Sync-agenten för Azure SQL Data Sync. Mer information om SQL Data Sync finns i [synkronisera data i flera moln och lokala databaser med SQL Data Sync](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync har **inte** stöd för Azure SQL Database Managed Instance just nu.

## <a name="download-and-install"></a>Hämta och installera

Om du vill ladda ned agenten för data synkronisering går du till [SQL Azure DataSync-agent](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Installera tyst

Om du vill installera Data Sync-agenten tyst från kommando tolken anger du ett kommando som liknar följande exempel. Kontrol lera fil namnet för den hämtade. msi-filen och ange dina egna värden för argumenten **TARGETDIR** och **SERVICEACCOUNT** .

- Om du inte anger något värde för **TARGETDIR**är standardvärdet `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`.

- Om du anger `LocalSystem` som värde för **SERVICEACCOUNT**använder du SQL Server autentisering när du konfigurerar agenten för att ansluta till den lokala SQL Server.

- Om du anger ett domän användar konto eller ett lokalt användar konto som värde för **SERVICEACCOUNT**måste du också ange lösen ordet med argumentet **SERVICEPASSWORD** . Till exempel `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Synkronisera data med SQL Server lokalt

Om du vill konfigurera Data Sync-agenten så att du kan synkronisera data med en eller flera lokala SQL Server-databaser, se [lägga till en lokal SQL Server-databas](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="agent-faq"></a>Vanliga frågor och svar om Data Sync-agent

### <a name="why-do-i-need-a-client-agent"></a>Varför behöver jag en klient agent

SQL Data Syncs tjänsten kommunicerar med SQL Server-databaser via klient agenten. Den här säkerhetsfunktionen förhindrar direkt kommunikation med databaser bakom en brand vägg. När SQL Data Syncs tjänsten kommunicerar med agenten används krypterade anslutningar och en unik token eller *agent nyckel*. SQL Server-databaser autentiserar agenten med hjälp av anslutnings strängen och Agent nyckeln. Den här designen ger en hög säkerhets nivå för dina data.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Hur många instanser av det lokala agent gränssnittet kan köras

Endast en instans av användar gränssnittet kan köras.

### <a name="how-can-i-change-my-service-account"></a>Hur kan jag ändra mitt tjänst konto

När du har installerat en klient agent är det enda sättet att ändra tjänst kontot att avinstallera det och installera en ny klient agent med det nya tjänst kontot.

### <a name="how-do-i-change-my-agent-key"></a>Hur gör jag för att ändra min agent nyckel

En agent nyckel kan bara användas en gång av en agent. Den kan inte återanvändas när du tar bort den och sedan installerar om en ny agent, och den kan inte användas av flera agenter. Om du behöver skapa en ny nyckel för en befintlig agent måste du vara säker på att samma nyckel registreras hos klient agenten och med SQL Data Sync tjänsten.

### <a name="how-do-i-retire-a-client-agent"></a>Hur gör jag för att återkalla en klient agent

Om du vill upphäva eller återkalla en agent direkt, återskapar du dess nyckel i portalen, men skickar den inte i agentens användar gränssnitt. Om du återskapar en nyckel inverifierar den tidigare nyckeln oavsett om motsvarande agent är online eller offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Hur gör jag för att flytta en klient agent till en annan dator

Om du vill köra den lokala agenten från en annan dator än den för närvarande, gör du följande:

1. Installera agenten på önskad dator.
2. Logga in på SQL Data Sync Portal och återskapa en agent nyckel för den nya agenten.
3. Använd den nya agentens användar gränssnitt för att skicka den nya Agent nyckeln.
4. Vänta medan klient agenten laddar ned listan över lokala databaser som har registrerats tidigare.
5. Ange autentiseringsuppgifter för databasen för alla databaser som visas som oåtkomliga. Dessa databaser måste kunna kontaktas från den nya dator där agenten är installerad.

## <a name="agent-tshoot"></a>Felsöka problem med Data Sync-agenten

- [Installationen, avinstallationen eller reparationen av klient agenten Miss lyckas](#agent-install)

- [Klient agenten fungerar inte när jag avbryter avinstallationen](#agent-uninstall)

- [Min databas visas inte i agent listan](#agent-list)

- [Klient agenten startar inte (fel 1069)](#agent-start)

- [Jag kan inte skicka Agent nyckeln](#agent-key)

- [Det går inte att ta bort klient agenten från portalen om den associerade lokala databasen inte kan kontaktas](#agent-delete)

- [Den lokala Sync-appen kan inte ansluta till den lokala synkroniseringstjänsten](#agent-connect)

### <a name="agent-install"></a>Installationen, avinstallationen eller reparationen av klient agenten Miss lyckas

- **Orsak**. Många scenarier kan orsaka detta fel. Ta reda på orsaken till felet genom att titta på loggarna.

- **Lösning**. Om du vill hitta den speciella orsaken till felet genererar du och tittar på Windows Installer loggar. Du kan aktivera loggning vid en kommando tolk. Om den nedladdade installations filen exempelvis är `SQLDataSyncAgent-2.0-x86-ENU.msi`, generera och granska loggfiler med hjälp av följande kommando rader:

  - För-installationer: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - För avinstallationer: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Du kan också aktivera loggning för alla installationer som utförs av Windows Installer. I Microsoft Knowledge Base-artikeln [så här aktiverar du Windows Installer loggning](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) ger en lösning med ett klick för att aktivera loggning för Windows Installer. Den innehåller också platsen för loggarna.

### <a name="agent-uninstall"></a>Klient agenten fungerar inte när jag avbryter avinstallationen

Klient agenten fungerar inte, även när du har avbrutit avinstallationen.

- **Orsak**. Detta beror på att den SQL Data Sync klient agenten inte lagrar autentiseringsuppgifter.

- **Lösning**. Du kan prova de här två lösningarna:

    -   Använd Services. msc för att ange autentiseringsuppgifterna för klient agenten igen.
    -   Avinstallera den här klient agenten och installera sedan en ny. Hämta och installera den senaste klient agenten från [Download Center](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="agent-list"></a>Min databas visas inte i agent listan

När du försöker lägga till en befintlig SQL Server-databas i en Sync-grupp visas inte databasen i listan över agenter.

Dessa scenarier kan orsaka det här problemet:

- **Orsak**. Klient agenten och Sync-gruppen finns i olika data Center.

- **Lösning**. Klient agenten och Sync-gruppen måste finnas i samma data Center. Om du vill konfigurera detta har du två alternativ:

    -   Skapa en ny agent i data centret där Sync-gruppen finns. Registrera sedan databasen med den agenten.
    -   Ta bort den aktuella Sync-gruppen. Återskapa sedan synkroniseringsresursen i data centret där agenten finns.

- **Orsak**. Klient agentens lista över databaser är inte aktuell.

- **Lösning**. Stoppa och starta sedan om klient Agent tjänsten.

    Den lokala agenten hämtar bara listan över associerade databaser när Agent nyckeln först skickas. Det går inte att hämta listan över associerade databaser för efterföljande agent nyckels under sändningar. Databaser som registreras under en agent flyttning visas inte i den ursprungliga agent instansen.

### <a name="agent-start"></a>Klient agenten startar inte (fel 1069)

Du upptäcker att agenten inte körs på en dator som är värd för SQL Server. När du försöker starta agenten manuellt visas en dialog ruta som visar meddelandet "fel 1069: tjänsten startades inte på grund av ett inloggnings fel."

![Dialog rutan Data Sync-fel 1069](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Orsak**. En sannolik orsak till det här felet är att lösen ordet på den lokala servern har ändrats sedan du skapade agent-och agent lösen ordet.

- **Lösning**. Uppdatera agentens lösen ord till det aktuella Server lösen ordet:

  1. Leta upp tjänsten SQL Data Sync Client Agent.  
    a. Välj **Starta**.  
    b. I rutan Sök anger du **Services. msc**.  
    c. I Sök resultaten väljer du **tjänster**.  
    d. I fönstret **tjänster** bläddrar du till posten för **SQL Data Sync-agenten**.  
  1. Högerklicka på **SQL Data Sync agent**och välj sedan **stoppa**.
  1. Högerklicka på **SQL Data Sync agent**och välj sedan **Egenskaper**.
  1. Välj fliken **Logga** in på **SQL Data Sync agent egenskaper**.
  1. Ange ditt lösen ord i rutan **lösen ord** .
  1. Ange lösen ordet igen i rutan **Bekräfta lösen ord** .
  1. Tryck på **Tillämpa** och välj sedan **OK**.
  1. I fönstret **tjänster** högerklickar du på tjänsten **SQL Data Sync agent** och klickar sedan på **Starta**.
  1. Stäng fönstret **tjänster** .

### <a name="agent-key"></a>Jag kan inte skicka Agent nyckeln

När du har skapat eller återskapat en nyckel för en agent försöker du skicka nyckeln via SqlAzureDataSyncAgent-programmet. Det gick inte att slutföra överföringen.

![Dialog rutan synkroniseringsfel – det går inte att skicka agent nyckel](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Krav**. Innan du fortsätter kontrollerar du följande krav:

  - SQL Data Sync Windows-tjänsten körs.

  - Tjänst kontot för SQL Data Sync Windows-tjänst har nätverks åtkomst.

  - Den utgående 1433-porten är öppen i den lokala brand Väggs regeln.

  - Den lokala IP-adressen läggs till i Server-eller databas brand Väggs regeln för databasen för synkronisering av metadata.

- **Orsak**. Agent nyckeln identifierar unikt varje lokal agent. Nyckeln måste uppfylla två villkor:

  -   Klient Agent nyckeln på SQL Data Sync-servern och den lokala datorn måste vara identisk.
  -   Klient Agent nyckeln kan bara användas en gång.

- **Lösning**. Om agenten inte fungerar beror det på att ett eller båda villkoren inte uppfylls. Så här får du agenten att fungera igen:

  1. Generera en ny nyckel.
  1. Använd den nya nyckeln till agenten.

  Så här tillämpar du den nya nyckeln på agenten:

  1. I Utforskaren går du till din agent installations katalog. Standard katalogen för installationen är C:\\program files (x86)\\Microsoft SQL Data Sync.
  1. Dubbelklicka på bin-underkatalogen.
  1. Öppna SqlAzureDataSyncAgent-programmet.
  1. Välj **Skicka agent nyckel**.
  1. I det angivna utrymmet klistrar du in nyckeln från Urklipp.
  1. Välj **OK**.
  1. Stäng programmet.

### <a name="agent-delete"></a>Det går inte att ta bort klient agenten från portalen om den associerade lokala databasen inte kan kontaktas

Om en lokal slut punkt (dvs. en databas) som har registrerats med en SQL Data Sync klient agent blir otillgänglig kan klient agenten inte tas bort.

- **Orsak**. Det går inte att ta bort den lokala agenten eftersom den otillgängliga databasen fortfarande är registrerad hos agenten. När du försöker ta bort agenten försöker borttagnings processen att komma åt databasen, vilket Miss lyckas.

- **Lösning**. Använd "framtvinga borttagning" för att ta bort den oåtkomliga databasen.

> [!NOTE]
> Om Sync metadata-tabeller är kvar efter en "tvångs borttagning", kan du använda `deprovisioningutil.exe` för att rensa dem.

### <a name="agent-connect"></a>Den lokala Sync-appen kan inte ansluta till den lokala synkroniseringstjänsten

- **Lösning**. Prova följande steg:

  1. Avsluta appen.  
  1. Öppna panelen komponent tjänster.  
    a. Skriv **Services. msc**i rutan Sök i verktygsfältet.  
    b. I Sök resultaten, dubbelklicka på **tjänster**.  
  1. Stoppa tjänsten **SQL Data Sync** .
  1. Starta om tjänsten **SQL Data Sync** .  
  1. Öppna appen igen.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Köra agenten för data synkronisering från kommando tolken

Du kan köra följande kommandon för Sync-agenten från kommando tolken:

### <a name="ping-the-service"></a>Pinga tjänsten

#### <a name="usage"></a>Användning

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Exempel

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Visa registrerade databaser

#### <a name="usage"></a>Användning

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Exempel

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Skicka in Agent nyckeln

#### <a name="usage"></a>Användning

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Exempel

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Registrera en databas

#### <a name="usage"></a>Användning

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Exempel

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Avregistrera en databas

När du använder det här kommandot för att avregistrera en databas avetablerar du databasen helt och hållet. Om databasen ingår i andra Sync-grupper, delar den här åtgärden de andra Sync-grupperna.

#### <a name="usage"></a>Användning

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Exempel

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>Uppdatera autentiseringsuppgifter

#### <a name="usage"></a>Användning

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Exempel

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>Nästa steg

Mer information om SQL Data Sync finns i följande artiklar:

-   Översikt – [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
-   Konfigurera Data Sync
    - I portalen – [Självstudie: Konfigurera SQL Data Sync att synkronisera data mellan Azure SQL Database och SQL Server lokalt](sql-database-get-started-sql-data-sync.md)
    - Med PowerShell
        -  [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Använd PowerShell för att synkronisera mellan en Azure SQL-databas och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Metodtips – [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Övervaka [SQL Data Sync med Azure Monitor loggar](sql-database-sync-monitor-oms.md)
-   Felsökning – [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
-   Uppdatera synkroniseringsschemat
    -   Med Transact-SQL – [Automatisera replikeringen av schemaändringar i Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Med PowerShell – [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)
