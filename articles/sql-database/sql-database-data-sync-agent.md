---
title: Data synkroniseras Agent för Azure SQL Data Sync | Microsoft Docs
description: Lär dig hur du installerar och kör Data Sync-agenten för Azure SQL Data Sync att synkronisera data med en lokal SQL Server-databaser
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 11/12/2018
ms.openlocfilehash: bb80b512176e8fe260eb4572ea9fa801a6ffc80a
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685148"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Data Sync-agenten för Azure SQL Data Sync

Synkronisera data med en lokal SQL Server-databaser genom att installera och konfigurera Data Sync-agenten för Azure SQL Data Sync. Mer information om SQL Data Sync finns i [synkronisera data i flera moln och lokala databaser med SQL Data Sync](sql-database-sync-data.md).

## <a name="download-and-install"></a>Ladda ned och installera

För att hämta Data Sync-agenten, gå till [SQL Azure Data Sync-agenten](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Tyst installation

Installera Data Sync-agenten obevakat från Kommandotolken genom att ange ett kommando som liknar följande exempel. Kontrollera namnet på den hämtade MSI-filen och ange egna värden för den **TARGETDIR** och **SERVICEACCOUNT** argument.

- Om du inte anger ett värde för **TARGETDIR**, standardvärdet är `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`.

- Om du anger `LocalSystem` som värde för **SERVICEACCOUNT**, använda SQL Server-autentisering när du konfigurerar agenten att ansluta till en lokal SQL Server.

- Om du anger ett domänanvändarkonto eller ett lokalt användarkonto som värde för **SERVICEACCOUNT**, du måste också ange lösenordet med den **SERVICEPASSWORD** argumentet. Till exempel `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Synkronisera data med SQL Server on-premises

Om du vill konfigurera Data Sync-agenten så att du kan synkronisera data med en eller flera lokala SQL Server-databaser, se [Lägg till en lokal SQL Server-databas](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="agent-faq"></a> Data Sync-agenten vanliga frågor och svar

### <a name="why-do-i-need-a-client-agent"></a>Varför behöver jag en klientagent

SQL Data Sync-tjänsten kommunicerar med SQL Server-databaser via klientagenten. Den här säkerhetsfunktionen förhindrar direktkommunikation med databaser bakom en brandvägg. När SQL Data Sync-tjänsten kommunicerar med agenten, det gör det med hjälp av krypterade anslutningar och en unik token eller *agentnyckeln*. SQL Server-databaserna autentisera agenten med anslutningsnyckel sträng och agenten. Den här designen ger en hög nivå av säkerhet för dina data.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Hur många instanser av lokal agent kan Användargränssnittet köras

Endast en instans av Användargränssnittet kan köras.

### <a name="how-can-i-change-my-service-account"></a>Hur ändrar jag mitt service-konto

När du har installerat en klientagent är det enda sättet att ändra konto för att avinstallera den och installera en ny klientagent med det nya tjänstkontot.

### <a name="how-do-i-change-my-agent-key"></a>Hur ändrar jag min agentnyckeln

En agentnyckel kan bara användas en gång av en agent. Den kan inte återanvändas när du tar bort och sedan installera om en ny agent, och kan inte användas av flera agenter. Om du vill skapa en ny nyckel för en befintlig agent måste du vara säker på att samma nyckel registreras med klientens agent och med SQL Data Sync-tjänsten.

### <a name="how-do-i-retire-a-client-agent"></a>Hur jag för att inaktivera en klientagent

Återskapa dess nyckeln i portalen för att omedelbart ogiltigförklara eller dra tillbaka en agent, men inte skickar den i Användargränssnittet för agenten. Återskapar en nyckel upphäver tidigare nyckeln oavsett om motsvarande agenten är online eller offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Hur flyttar jag en klientagent till en annan dator

Om du vill köra lokal agent från en annan dator än den för närvarande finns på gör du följande:

1. Installera agenten på önskad dator.
2. Logga in till SQL Data Sync-portalen och återskapa en agentnyckel för den nya agenten.
3. Använda den nya agenten Användargränssnittet för att skicka in den nya agentnyckeln.
4. Vänta medan klientagenten laddar ned listan över lokala databaser som har registrerats tidigare.
5. Ange autentiseringsuppgifter på databasen för alla databaser som visas som inte kan nås. Dessa databaser måste kunna nås från den nya datorn där agenten är installerad.

## <a name="agent-tshoot"></a> Felsökning av problem med Data Sync-agenten

- [Klientagenten installera, avinstallera eller reparera misslyckas](#agent-install)

- [Klientagenten fungerar inte efter att jag avbryter avinstallation](#agent-uninstall)

- [Min databas är inte visas i listan agent](#agent-list)

- [Klientagenten startar inte (fel 1069)](#agent-start)

- [Jag kan inte skicka agentnyckeln](#agent-key)

- [Klientens agent kan inte tas bort från portalen om dess associerade lokal databas inte kan nås](#agent-delete)

- [Den lokala Synkroniseringsagenten appen kan inte ansluta till lokala synkroniseringstjänsten](#agent-connect)

### <a name="agent-install"></a> Klientagenten installera, avinstallera eller reparera misslyckas

- **Orsak**. Det här felet kan leda till många scenarier. Titta på loggarna för att fastställa orsaken till felet.

- **Lösning**. Du hittar den specifika orsaken till felet genom att generera och titta på loggarna för Windows Installer. Du kan aktivera loggning i en kommandotolk. Exempel: om den nedladdade installationsfilen är `SQLDataSyncAgent-2.0-x86-ENU.msi`, generera och undersöka loggfiler med hjälp av följande kommandorader:

    -   För installationer: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
    -   Så här avinstallerar för: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Du kan också aktivera loggning för alla installationer som utförs av Windows Installer. I Microsoft Knowledge Base-artikeln [så här aktiverar du Windows Installer-loggning](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) tillhandahåller en lösning för ett klick att aktivera loggning för Windows Installer. Det ger också platsen för loggarna.

### <a name="agent-uninstall"></a> Klientagenten fungerar inte efter att jag avbryter avinstallation

Klientagenten fungerar inte även efter att du avbryter dess avinstallationen.

- **Orsak**. Det beror på att SQL Data Sync-klientagenten inte lagra autentiseringsuppgifter.

- **Lösning**. Du kan försöka dessa två lösningar:

    -   Använd services.msc för att ange autentiseringsuppgifterna för klientagenten för.
    -   Avinstallera den här klientagenten och installera en ny. Ladda ned och installera den senaste klientagenten [Download Center](https://go.microsoft.com/fwlink/?linkid=221479).

### <a name="agent-list"></a> Min databas är inte visas i listan agent

När du försöker lägga till en befintlig SQL Server-databas till en synkroniseringsgrupp visas databasen inte i listan över agenter.

Dessa scenarier kan orsaka det här problemet:

- **Orsak**. Klientgrupp för agenten och sync finns i olika datacenter.

- **Lösning**. Klientagenten och synkroniseringsgruppen måste vara i samma datacenter. Om du vill konfigurera det här har du två alternativ:

    -   Skapa en ny agent i datacenter där synkroniseringsgruppen finns. Registrera sedan databasen med den agenten.
    -   Ta bort den aktuella synkroniseringsgruppen. Sedan återskapa synkroniseringsgruppen i datacenter där agenten är placerad.

- **Orsak**. Klientagenten listan över databaser är inte aktuell.

- **Lösning**. Stoppa och starta sedan om klient-agenttjänsten.

    Lokal agent ned listan över de associerade databaserna endast på den första överföringen av agentnyckeln. Det inte att hämta listan över associerade databaserna på efterföljande agenten viktiga förslag. Databaser som är registrerade under en agent flytta visas inte i den ursprungliga agent-instansen.

### <a name="agent-start"></a> Klientagenten startar inte (fel 1069)

Du upptäcker att agenten inte körs på en dator som är värd för SQL Server. När du försöker starta agenten manuellt, visas en dialogruta som visar meddelandet ”fel 1069: tjänsten startade inte på grund av ett inloggningsfel”.

![Dialogrutan för data Sync-fel 1069](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Orsak**. En troliga orsaken till felet är att lösenord på den lokala servern har ändrats sedan du skapade agenten och agenten lösenord.

- **Lösning**. Uppdatera agentens lösenordet till ditt nuvarande lösenord för servern:

  1. Leta upp tjänsten SQL Data Sync client agent.  
    a. Välj **starta**.  
    b. I sökrutan anger **services.msc**.  
    c. I sökresultaten väljer **Services**.  
    d. I den **Services** och rulla till posten för **SQL Data Sync-agenten**.  
  1. Högerklicka på **SQL Data Sync-agenten**, och välj sedan **stoppa**.
  1. Högerklicka på **SQL Data Sync-agenten**, och välj sedan **egenskaper**.
  1. På **agentegenskaper för SQL Data Sync**väljer den **logga in** fliken.
  1. I den **lösenord** , ange ditt lösenord.
  1. I den **Bekräfta lösenord** rutan, ange ditt lösenord igen.
  1. Tryck på **Tillämpa** och välj sedan **OK**.
  1. I den **Services** fönstret högerklickar du på den **SQL Data Sync-agenten** tjänsten och klicka sedan på **starta**.
  1. Stäng den **Services** fönster.

### <a name="agent-key"></a> Jag kan inte skicka agentnyckeln

När du skapar eller återskapa en nyckel för en agent, som du försöker skicka nyckeln via SqlAzureDataSyncAgent-programmet. Det inte går att slutföra överföringen.

![Synkronisera feldialogrutan – det går inte att skicka agentnyckel](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Krav för**. Innan du fortsätter måste du kontrollera följande krav:

  - SQL Data Sync Windows-tjänsten körs.

  - Tjänstkontot för SQL Data Sync Windows-tjänsten har åtkomst till nätverket.

  - Den utgående porten 1433 är öppen i din lokala brandväggsregeln.

  - Den lokala IP-adressen läggs till servern eller databasen brandväggsregel för metadatasynkroniserings-databasen.

- **Orsak**. Agentnyckeln som identifierar unikt varje lokal agent. Nyckeln måste uppfylla förutsättningar:

  -   Klientens agentnyckel på SQL Data Sync-servern och den lokala datorn måste vara identiska.
  -   Agentnyckeln som klienten kan användas endast en gång.

- **Lösning**. Om din agent inte fungerar, beror det på att en eller båda av dessa villkor inte uppfylls. Hämta din agenten ska fungera igen:

  1. Skapa en ny nyckel.
  1. Tillämpa den nya nyckeln till agenten.

  Tillämpa den nya nyckeln för agenten:

  1. I Utforskaren går du till din katalog för agentinstallation. Standardkatalogen för installationen är C:\\programfiler (x86)\\Microsoft SQL Data Sync.
  1. Dubbelklicka på bin-katalogen.
  1. Öppna SqlAzureDataSyncAgent-programmet.
  1. Välj **skicka Agentnyckeln**.
  1. Klistra in nyckeln från Urklipp i det angivna området.
  1. Välj **OK**.
  1. Stäng programmet.

### <a name="agent-delete"></a> Klientens agent kan inte tas bort från portalen om dess associerade lokal databas inte kan nås

Om en lokal slutpunkt (det vill säga en databas) som har registrerats hos en SQL Data Sync klientagent blir oåtkomlig tas klientagenten inte bort.

- **Orsak**. Lokal agent kan inte tas bort eftersom den inte går att nå databasen är fortfarande registrerad med agenten. När du försöker ta bort agenten försöker borttagningsprocessen nå databasen, vilket misslyckas.

- **Lösning**. Använd ”tvinga delete” ta bort databasen kan inte nås.

> [!NOTE]
> Om synkronisering metadatatabeller finns kvar efter en ”tvinga delete”, Använd `deprovisioningutil.exe` att rensa dem.

### <a name="agent-connect"></a> Den lokala Synkroniseringsagenten appen kan inte ansluta till lokala synkroniseringstjänsten

- **Lösning**. Prova följande steg:

  1. Avsluta appen.  
  1. Öppna panelen Component Services.  
    a. I sökrutan i Aktivitetsfältet, ange **services.msc**.  
    b. I sökresultaten dubbelklickar du på **Services**.  
  1. Stoppa den **SQL Data Sync** service.
  1. Starta om den **SQL Data Sync** service.  
  1. Öppna appen igen.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Kör Data Sync-agenten från Kommandotolken

Du kan köra följande kommandon för Data Sync-agenten från Kommandotolken:

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

När du använder det här kommandot för att avregistrera en databas, deprovisions databasen helt. Om databasen tillhör andra synkroniseringsgrupper kan delar den här åtgärden andra synkroniseringsgrupper.

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

-   Översikt – [synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
-   Konfigurera datasynkronisering
    - I portalen – [självstudie: Ställ in SQL Data Sync att synkronisera data mellan Azure SQL Database och SQL Server lokalt](sql-database-get-started-sql-data-sync.md)
    - Med PowerShell
        -  [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Metodtips – [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Övervaka – [övervaka SQL Data Sync med Log Analytics](sql-database-sync-monitor-oms.md)
-   Felsök - [felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
-   Uppdatera synkroniseringsschemat
    -   Med Transact-SQL - [automatisera replikeringen av schemaändringar i Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Med PowerShell - [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)
