---
title: App Service i Azure Stack uppdatera 5 viktig information | Microsoft Docs
description: Läs mer om nyheter i uppdateringen fem för App Service i Azure Stack, kända problem och var du kan hämta uppdateringen.
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
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 192ac256f013498e57ecf7939d29796af073b948
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260569"
---
# <a name="app-service-on-azure-stack-update-5-release-notes"></a>App Service i Azure Stack uppdatering 5 viktig information

*Gäller för Integrerade Azure Stack-system och Azure Stack Development Kit*

Följande versionsinformation innehåller förbättringar och korrigeringar i Azure App Service på Azure Stack uppdatering 5 och kända problem. Kända problem är indelade i problem som är direkt relaterade till distribution, uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]
> Uppdateringen är 1901 integrerade Azure Stack-system eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service 1.5.


## <a name="build-reference"></a>Skapa referens

App Service på Azure Stack uppdatering 5 build-nummer är **80.0.2.15**

### <a name="prerequisites"></a>Förutsättningar

Referera till den [innan du börjar dokumentation](azure-stack-app-service-before-you-get-started.md) innan du påbörjar distributionen.

Innan du börjar uppgraderingen av Azure App Service i Azure Stack till 1.5:

- Se till att alla roller är klar i Azure App Service-Administration i Azure Stack-Administratörsportalen

- Säkerhetskopiera App Service och Master-databaser:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Säkerhetskopiera filresursen Fleranvändarapp innehåll

- Syndikera den **tillägget för anpassat skript** version **1.9.1** från Marketplace

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

Azure App Service i Azure Stack uppdatering 5 innehåller följande förbättringar och korrigeringar:

- Uppdaterar till **App Service-klient, Admin, portaler för funktioner och verktyg för Kudu**. Konsekvent med Azure Stack Portal SDK-version.

- Uppdateringar **Azure Functions-körningen** till **v1.0.12205**.

- Uppdaterar till **Kudu verktyg** att lösa problem med formatering och funktioner för kunder som är verksamma **frånkopplad** Azure Stack. 

- Core-tjänst för att förbättra tillförlitlighet och felmeddelanden aktiverar enklare undersöka vanliga problem med uppdateringar.

- **Uppdateringar till följande ramverk för programmet och verktyg**:
  - Har lagts till ASP.NET-Core 2.1.6 och 2.2.0
  - Har lagts till NodeJS 10.14.1
  - Har lagts till NPM punkterna 6.4.1
  - Uppdaterade Kudu till 79.20129.3767
  
- **Uppdateringar av underliggande operativsystemet för alla roller**:
  - [2019-02 kumulativ uppdatering för Windows Server 2016 för x64-baserade system (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

### <a name="post-deployment-steps"></a>Steg efter distribution

> [!IMPORTANT]  
> Om du har angett App Service-resursprovidern med en SQL alltid på instans måste du [lägga till appservice_hosting och appservice_metering databaser i en tillgänglighetsgrupp](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) och synkronisera databaser för att förhindra förlust av tjänsten i händelse av ett databasfel.

### <a name="post-update-steps"></a>Steg efter uppdateringen

För kunder som vill migrera till innesluten databas för befintliga Azure App Service i Azure Stack-distributioner, kör du dessa steg när Azure App Service i Azure Stack 1.5-uppdateringen har slutförts:

> [!IMPORTANT]
> Migreringsproceduren tar cirka 5 – 10 minuter.  Metoden innebär att du avslutar de befintliga databasen Inloggningssessioner.  Planera för avbrott att migrera och validera Azure App Service på Azure Stack efter migreringen.  De här stegen är inte obligatoriska om du har slutfört de här stegen när du har uppdaterat till Azure App Service i Azure Stack 1.3.
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

1. Konverterar en databas som delvis ingå medför konverteringen driftstopp eftersom alla aktiva sessioner måste avslutas

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

### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

- Arbetare är inte nå filserver när App Service har distribuerats i ett befintligt virtuellt nätverk och servern är endast tillgänglig i det privata nätverket som påpekas i Azure App Service i Azure Stack-dokumentationen.

Om du väljer att distribuera till ett befintligt virtuellt nätverk och en intern IP-adress för att ansluta till filservern, du måste lägga till en utgående säkerhetsregel att aktivera SMB-trafik mellan worker-undernät och filservern. Gå till WorkersNsg i Admin Portal och Lägg till en utgående säkerhetsregel med följande egenskaper:
 * Källa: Alla
 * Käll-portintervall: *
 * Mål: IP-adresser
 * Mål-IP-adressintervall: Intervall av IP-adresser för din filserver
 * Målportintervall: 445
 * Protokoll: TCP
 * Åtgärd: Tillåt
 * Prioritet: 700
 * Namn: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Kända problem med Cloud administratörerna fungerar Azure App Service i Azure Stack

Finns i dokumentationen i den [viktig information om Azure Stack 1809](azure-stack-update-1809.md)

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure App Service finns i [Azure App Service i Azure Stack-översikt](azure-stack-app-service-overview.md).
- Mer information om hur du förbereder att distribuera App Service i Azure Stack finns i [innan du sätter igång med App Service i Azure Stack](azure-stack-app-service-before-you-get-started.md).
