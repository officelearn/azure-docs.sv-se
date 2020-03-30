---
title: Datasynkroniseringsagent för SQL Data Sync
description: Lär dig hur du installerar och kör Data Sync Agent för Azure SQL Data Sync för att synkronisera data med lokala SQL Server-databaser
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
ms.openlocfilehash: 6d0a728401ac9f0156cc8fa913ce486bb577c6dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825180"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Datasynkroniseringsagent för Azure SQL Data Sync

Synkronisera data med lokala SQL Server-databaser genom att installera och konfigurera Data Sync Agent för Azure SQL Data Sync. Mer information om SQL Data Sync finns i [Synkronisera data i flera molndatabaser och lokala databaser med SQL Data Sync](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync har **inte** stöd för Azure SQL Database Managed Instance just nu.

## <a name="download-and-install"></a>Hämta och installera

Om du vill hämta Data Sync Agent går du till [SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Installera tyst

Om du vill installera Data Sync Agent tyst från kommandotolken anger du ett kommando som liknar följande exempel. Kontrollera filnamnet på den nedladdade MSI-filen och ange dina egna värden för argumenten **TARGETDIR** och **SERVICEACCOUNT.**

- Om du inte anger något värde för **TARGETDIR**är `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`standardvärdet .

- Om du `LocalSystem` anger som värdet för **SERVICEACCOUNT**använder du SQL Server-autentisering när du konfigurerar agenten för att ansluta till den lokala SQL Server.

- Om du anger ett domänanvändarkonto eller ett lokalt användarkonto som värdet av **SERVICEACCOUNT**måste du också ange lösenordet med **argumentet SERVICEPASSWORD.** Till exempel `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Synkronisera data med SQL Server lokalt

Information om hur du konfigurerar Data Sync Agent så att du kan synkronisera data med en eller flera lokala SQL Server-databaser finns [i Lägga till en lokal SQL Server-databas](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a>Vanliga frågor om datasynkroniseringsagent

### <a name="why-do-i-need-a-client-agent"></a>Varför behöver jag en klientagent

SQL Data Sync-tjänsten kommunicerar med SQL Server-databaser via klientagenten. Den här säkerhetsfunktionen förhindrar direkt kommunikation med databaser bakom en brandvägg. När SQL Data Sync-tjänsten kommunicerar med agenten gör den det med hjälp av krypterade anslutningar och en unik token- eller *agentnyckel*. SQL Server-databaserna autentiserar agenten med hjälp av anslutningssträngen och agentnyckeln. Den här designen ger en hög säkerhetsnivå för dina data.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Hur många instanser av det lokala agentgränssnittet kan köras

Endast en instans av användargränssnittet kan köras.

### <a name="how-can-i-change-my-service-account"></a>Hur ändrar jag mitt tjänstkonto

När du har installerat en klientagent är det enda sättet att ändra tjänstkontot att avinstallera det och installera en ny klientagent med det nya tjänstkontot.

### <a name="how-do-i-change-my-agent-key"></a>Hur ändrar jag min agentnyckel

En agentnyckel kan bara användas en gång av en agent. Det går inte att återanvändas när du tar bort och sedan installerar om en ny agent, och den kan inte heller användas av flera agenter. Om du behöver skapa en ny nyckel för en befintlig agent måste du vara säker på att samma nyckel registreras med klientagenten och med SQL Data Sync-tjänsten.

### <a name="how-do-i-retire-a-client-agent"></a>Hur drar jag tillbaka en klientagent

Om du vill ogiltigförklara eller dra tillbaka en agent återskapar du nyckeln i portalen men skickar den inte i agentgränssnittet. Om du återskapar en nyckel blir den tidigare nyckeln ogiltig oavsett om motsvarande agent är online eller offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Hur flyttar jag en klientagent till en annan dator

Om du vill köra den lokala agenten från en annan dator än den är för närvarande på gör du följande:

1. Installera agenten på önskad dator.
2. Logga in på SQL Data Sync-portalen och återskapa en agentnyckel för den nya agenten.
3. Använd den nya agentens användargränssnitt för att skicka den nya agentnyckeln.
4. Vänta medan klientagenten hämtar listan över lokala databaser som har registrerats tidigare.
5. Ange databasreferenser för alla databaser som inte kan nås. Dessa databaser måste kunna nås från den nya datorn där agenten är installerad.

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a>Felsöka problem med datasynkroniseringsagenten

- [Klientagenten installera, avinstallera eller reparera misslyckas](#agent-install)

- [Klientagenten fungerar inte när jag har avbrutit avinstallationen](#agent-uninstall)

- [Min databas finns inte med i agentlistan](#agent-list)

- [Klientagenten startar inte (fel 1069)](#agent-start)

- [Jag kan inte skicka agentnyckeln](#agent-key)

- [Klientagenten kan inte tas bort från portalen om den associerade lokala databasen inte kan nås](#agent-delete)

- [Appen Lokal synkroniseringsagent kan inte ansluta till den lokala synkroniseringstjänsten](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a>Klientagenten installera, avinstallera eller reparera misslyckas

- **Orsak**. Många scenarier kan orsaka detta fel. Om du vill ta reda på orsaken till det här felet tittar du på loggarna.

- **Upplösning**. Om du vill hitta den specifika orsaken till felet genererar och tittar du på Windows Installer-loggarna. Du kan aktivera loggning vid en kommandotolk. Om den hämtade installationsfilen till `SQLDataSyncAgent-2.0-x86-ENU.msi`exempel är generera och undersöka loggfiler med hjälp av följande kommandorader:

  - För installationer:`msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - För avinstallationer:`msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Du kan också aktivera loggning för alla installationer som utförs av Windows Installer. Microsoft Knowledge Base-artikeln Så här aktiverar du loggning av [Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) innehåller en lösning med ett klick för att aktivera loggning för Windows Installer. Det ger också platsen för loggarna.

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a>Klientagenten fungerar inte när jag har avbrutit avinstallationen

Klientagenten fungerar inte, inte ens efter att du har avbrutit avinstallationen.

- **Orsak**. Detta beror på att SQL Data Sync-klientagenten inte lagrar autentiseringsuppgifter.

- **Upplösning**. Du kan prova följande två lösningar:

    -   Använd services.msc för att ange autentiseringsuppgifterna för klientagenten igen.
    -   Avinstallera den här klientagenten och installera sedan en ny. Ladda ner och installera den senaste klientagenten från [Download Center](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a>Min databas finns inte med i agentlistan

NÃ¤r du fÃ¶rsÃ¶rsÃ¶r att lägga till en befintlig SQL Server-databas i en synkroniseringsgrupp visas inte databasen i listan med ombud.

Dessa scenarier kan orsaka det här problemet:

- **Orsak**. Klientagenten och synkroniseringsgruppen finns i olika datacenter.

- **Upplösning**. Klientagenten och synkroniseringsgruppen måste finnas i samma datacenter. Om du vill ställa in detta har du två alternativ:

    -   Skapa en ny agent i datacentret där synkroniseringsgruppen finns. Registrera sedan databasen hos agenten.
    -   Ta bort den aktuella synkroniseringsgruppen. Återskapa sedan synkroniseringsgruppen i datacentret där agenten finns.

- **Orsak**. Klientagentens lista över databaser är inte aktuell.

- **Upplösning**. Stoppa och starta om klientagenttjänsten.

    Den lokala agenten hämtar listan över associerade databaser endast vid den första inlämningen av agentnyckeln. Listan över associerade databaser för efterföljande agentnyckelinlämningar hämtas inte. Databaser som registreras under en agentflytt visas inte i den ursprungliga agentinstansen.

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a>Klientagenten startar inte (fel 1069)

Du upptäcker att agenten inte körs på en dator som är värd för SQL Server. När du försöker starta agenten manuellt visas en dialogruta med meddelandet "Fel 1069: Tjänsten startades inte på grund av ett inloggningsfel".

![Data sync-fel 1069, dialogruta](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Orsak**. En trolig orsak till det här felet är att lösenordet på den lokala servern har ändrats sedan du skapade agent- och agentlösenordet.

- **Upplösning**. Uppdatera agentens lösenord till ditt nuvarande serverlösenord:

  1. Leta reda på SQL Data Sync-klientagenttjänsten.  
    a. Välj **Starta**.  
    b. Ange **services.msc**i sökrutan.  
    c. Välj **Tjänster**i sökresultaten .  
    d. Bläddra till posten för SQL **Data Sync Agent**i fönstret **Tjänster** .  
  1. Högerklicka på **SQL Data Sync Agent**och välj sedan **Stoppa**.
  1. Högerklicka på **SQL Data Sync Agent**och välj sedan **Egenskaper**.
  1. På **sql data sync agent egenskaper**väljer du fliken Logga **in.**
  1. Ange ditt lösenord i rutan **Lösenord.**
  1. Ange lösenordet igen i rutan **Bekräfta lösenord.**
  1. Tryck på **Tillämpa** och välj sedan **OK**.
  1. Högerklicka på **tjänsten SQL Data Sync Agent** i fönstret **Tjänster** och klicka sedan på **Start**.
  1. Stäng fönstret **Tjänster.**

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a>Jag kan inte skicka agentnyckeln

När du har skapat eller återskapat en nyckel för en agent försöker du skicka nyckeln via SqlAzureDataSyncAgent-programmet. Inlämningen misslyckas med att slutföras.

![Dialogrutan Synkroniseringsfel - Det går inte att skicka agentnyckel](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Förutsättningar**. Innan du fortsätter kontrollerar du följande förutsättningar:

  - SQL Data Sync Windows-tjänsten körs.

  - Tjänstkontot för SQL Data Sync Windows-tjänsten har nätverksåtkomst.

  - Den utgående 1433-porten är öppen i din lokala brandväggsregel.

  - Den lokala ip-adressen läggs till i server- eller databasbrandväggsregeln för synkroniseringsmetadatadatabasen.

- **Orsak**. Agentnyckeln identifierar unikt varje lokal agent. Nyckeln måste uppfylla två villkor:

  -   Klientagentnyckeln på SQL Data Sync-servern och den lokala datorn måste vara identisk.
  -   Klientagentnyckeln kan bara användas en gång.

- **Upplösning**. Om din agent inte fungerar beror det på att ett eller båda av dessa villkor inte är uppfyllda. Så här får du din agent att fungera igen:

  1. Generera en ny nyckel.
  1. Använd den nya nyckeln på agenten.

  Så här använder du den nya nyckeln på agenten:

  1. Gå till agentinstallationskatalogen i Utforskaren. Standardinstallationskatalogen är\\C: Programfiler\\(x86) Microsoft SQL Data Sync.
  1. Dubbelklicka på lagerplatsunderkatalogen.
  1. Öppna SqlAzureDataSyncAgent-programmet.
  1. Välj **Skicka agentnyckel**.
  1. Klistra in nyckeln från Urklipp i det angivna utrymmet.
  1. Välj **OK**.
  1. Stäng programmet.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a>Klientagenten kan inte tas bort från portalen om den associerade lokala databasen inte kan nås

Om en lokal slutpunkt (det vill vara en databas) som är registrerad hos en SQL Data Sync-klientagent inte kan nås, kan klientagenten inte tas bort.

- **Orsak**. Det går inte att ta bort den lokala agenten eftersom databasen som inte kan nås fortfarande är registrerad hos agenten. När du försöker ta bort agenten försöker borttagningsprocessen nå databasen, vilket misslyckas.

- **Upplösning**. Använd "force delete" för att ta bort databasen som inte kan nås.

> [!NOTE]
> Om metadatatabeller för synkronisering finns kvar `deprovisioningutil.exe` efter en "force delete" används du för att rensa dem.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a>Appen Lokal synkroniseringsagent kan inte ansluta till den lokala synkroniseringstjänsten

- **Upplösning**. Prova följande steg:

  1. Avsluta appen.  
  1. Öppna panelen Komponenttjänster.  
    a. Ange **services.msc**i sökrutan i Aktivitetsfältet .  
    b. Dubbelklicka på **Tjänster**i sökresultaten .  
  1. Stoppa **SQL Data** Sync-tjänsten.
  1. Starta om **SQL Data Sync-tjänsten.**  
  1. Öppna appen igen.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Köra datasynkroniseringsagenten från kommandotolken

Du kan köra följande datasynkroniseringsagentkommandon från kommandotolken:

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

### <a name="submit-the-agent-key"></a>Skicka agentnyckeln

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

När du använder det här kommandot för att avregistrera en databas avetableras databasen helt. Om databasen deltar i andra synkroniseringsgrupper bryter den här åtgärden de andra synkroniseringsgrupperna.

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
-   Övervaka - [Övervaka SQL Data Sync med Azure Monitor-loggar](sql-database-sync-monitor-oms.md)
-   Felsökning – [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
-   Uppdatera synkroniseringsschemat
    -   Med Transact-SQL – [Automatisera replikeringen av schemaändringar i Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Med PowerShell – [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)
