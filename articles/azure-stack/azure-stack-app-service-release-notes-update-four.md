---
title: App Service i Azure Stack uppdatera 4 viktig information | Microsoft Docs
description: Läs mer om nyheter i uppdateringen fyra för App Service i Azure Stack, kända problem och var du kan hämta uppdateringen.
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
ms.date: 11/13/2018
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 2555ce4b9485ba6464bde3422df114343b579641
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243427"
---
# <a name="app-service-on-azure-stack-update-4-release-notes"></a>App Service i Azure Stack uppdatering 4 viktig information

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Följande versionsinformation innehåller förbättringar och korrigeringar i Azure App Service på Azure Stack uppdatering 4 och kända problem. Kända problem är indelade i problem som är direkt relaterade till distribution, uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]
> Uppdateringen är 1809 integrerade Azure Stack-system eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service 1.4.
>
>

## <a name="build-reference"></a>Skapa referens

App Service på Azure Stack uppdatering 4 build-nummer är **78.0.13698.5**

### <a name="prerequisites"></a>Förutsättningar

Referera till den [innan du börjar dokumentation](azure-stack-app-service-before-you-get-started.md) innan du påbörjar distributionen.

Innan du börjar uppgraderingen av Azure App Service i Azure Stack till 1.4:

- Se till att alla roller är klar i Azure App Service-Administration i Azure Stack-Administratörsportalen

- Säkerhetskopiera App Service och Master-databaser:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Säkerhetskopiera filresursen Fleranvändarapp innehåll

- Syndikera tillägget för anpassat skript version 1.9 från Marketplace

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

Azure App Service i Azure Stack uppdatering 4 innehåller följande förbättringar och korrigeringar:

- Lösning för [CVE 2018 8600](https://aka.ms/CVE20188600) Cross Site Scripting säkerhetsproblem.

- Stöd har lagts till för App Service 2018-02-01-API-version

- Uppdaterar till **App Service-klient, Admin, portaler för funktioner och verktyg för Kudu**. Konsekvent med Azure Stack Portal SDK-version.

- Core-tjänst för att förbättra tillförlitlighet och felmeddelanden aktiverar enklare undersöka vanliga problem med uppdateringar.

- **Uppdateringar till följande ramverk för programmet och verktyg**:
  - Har lagts till NodeJS 10.6.0
  - Har lagts till NPM 6.1.0
  - Har lagts till Zulu OpenJDK 8.31.0.2
  - Har lagts till Tomcat 8.5.34 och 9.0.12
  - Har lagts till PHP-versioner:
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Uppdatera Python versioner:
    - 2.7.15
    - 3.6.6
  - Uppdaterade Git för Windows till v 2.17.1.2
  - Uppdaterade Kudu till 78.11022.3613
  
- **Uppdateringar av underliggande operativsystemet för alla roller**:
  - [2018-10 kumulativ uppdatering för Windows Server 2016 för x64-baserade system (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- Löst verifieringsproblem för mallen när du distribuerar Wordpress; DNN; och Orchard CMS galleriobjekt

- Löst ett problem när Azure Stack roterar klientcertifikatet Azure Resource Manager

- Återställa funktioner i inställningarna för Cross Origin Resource Sharing i klientportal för App Service

- Visa ett felmeddelande i App Service-administratörsportalen när kontrollplanet resource provider inte kan ansluta till den Konfigurera SQL Server-instansen

- Se till att slutpunkten anges i anslutningssträngen för anpassad lagring när anges i den nya funktionen programmet

### <a name="post-update-steps-optional"></a>Uppdatera steg efter (valfritt)

För kunder som vill migrera till innesluten databas för befintliga Azure App Service i Azure Stack-distributioner, kör du dessa steg när Azure App Service i Azure Stack 1.4 uppdateringen har slutförts:

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
