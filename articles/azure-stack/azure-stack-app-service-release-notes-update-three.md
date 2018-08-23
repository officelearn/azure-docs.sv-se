---
title: App Service i Azure Stack uppdatera 3 viktig information | Microsoft Docs
description: Läs mer om nyheter i uppdateringen tre för App Service i Azure Stack, kända problem och var du kan hämta uppdateringen.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 88a4bcf018387ac83b485ec9e2efac11f85ba97c
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42432297"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>App Service i Azure Stack uppdatering 3 viktig information

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Följande versionsinformation innehåller förbättringar och korrigeringar i Azure App Service på Azure Stack uppdatering 3 och kända problem. Kända problem är indelade i problem som är direkt relaterade till distribution, uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]
> Uppdateringen är 1807 integrerade Azure Stack-system eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service 1.3.
>
>

## <a name="build-reference"></a>Skapa referens

App Service på Azure Stack Update 3 build-nummer är **74.0.13698.31**

### <a name="prerequisites"></a>Förutsättningar

Referera till den [innan du börjar dokumentation](azure-stack-app-service-before-you-get-started.md) innan du påbörjar distributionen.

Kontrollera alla roller är redo innan du påbörjar uppgraderingen av Azure App Service i Azure Stack till 1.3 i Azure App Service-Administration i Azure Stack-Administratörsportalen

![Rollstatus för App Service](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

Azure App Service i Azure Stack Update 3 innehåller följande förbättringar och korrigeringar:

- Stöd för användning av SQL Server Always On för databaser i Azure App Service-Resursprovider.

- Parameter har lagts till ny miljö att skapa AADIdentityApp helper skriptet att hjälpa målobjekt olika AAD-regioner.

- Uppdaterar till **App Service-klient, Admin, portaler för funktioner och verktyg för Kudu**. Konsekvent med Azure Stack Portal SDK-version.

- Core-tjänst för att förbättra tillförlitlighet och felmeddelanden aktiverar enklare undersöka vanliga problem med uppdateringar.

- **Uppdateringar till följande ramverk för programmet och verktyg**:
  - Har lagts till ASP.Net Core 2.1.2
  - Har lagts till NodeJS 10.0.0
  - Har lagts till Zulu OpenJDK 8.30.0.1
  - Har lagts till Tomcat 8.5.31 och 9.0.8
  - Har lagts till PHP-versioner:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Har lagts till Wincache 2.0.0.8
  - Uppdaterade Git för Windows till v 2.17.1.2
  - Uppdaterade Kudu till 74.10611.3437
  
- **Uppdateringar av underliggande operativsystemet för alla roller**:
  - [Servicing stack-uppdatering för Windows Server 2016 för x64-baserade system (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [2018-07 kumulativ uppdatering för Windows Server 2016 för x64-baserade system (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Uppdatera steg efter (valfritt)

För kunder som vill migrera till innesluten databas för befintliga Azure App Service i Azure Stack-distributioner, kör du dessa steg när Azure App Service i Azure Stack 1.3 uppdateringen har slutförts:

> [!IMPORTANT]
> Den här proceduren tar cirka 5 – 10 minuter.  Den här proceduren innebär att du avslutar de befintliga databasen Inloggningssessioner.  Planera för avbrott att migrera och validera Azure App Service på Azure Stack efter migreringen
>
>

1. Lägg till [AppService-databaser (appservice_hosting och appservice_metering) till en tillgänglighetsgrupp](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. Aktivera innehåller databas
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Konvertering av en databas till delvis ingår.  Det här steget medför driftstopp eftersom alla aktiva sessioner måste avslutas

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

Verifiera

1. Kontrollera om SQL Server har inneslutning aktiverat

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Kontrollera befintlig inneslutna beteende
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Kända problem med Cloud administratörerna fungerar Azure App Service i Azure Stack

Finns i dokumentationen i den [Azure Stack 1807 viktig information](azure-stack-update-1807.md)

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure App Service finns i [Azure App Service i Azure Stack-översikt](azure-stack-app-service-overview.md).
- Mer information om hur du förbereder att distribuera App Service i Azure Stack finns i [innan du sätter igång med App Service i Azure Stack](azure-stack-app-service-before-you-get-started.md).
