---
title: Återställ App Service i Azure Stack | Microsoft Docs
description: Detaljerad vägledning för App Service i Azure Stack disaster recovery
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: b034259dde817c863d976384b08da17983ed9de7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340247"
---
# <a name="recovery-of-app-service-on-azure-stack"></a>Återställning av App Service i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*  

Det här dokumentet innehåller anvisningar om åtgärder som ska vidtas för katastrofåterställning för App Service.

Följande åtgärder måste vidtas för att återställa App Service i Azure Stack från säkerhetskopia:
1.  Återställ App Service-databaser
2.  Återställa filinnehållet server resurs
3.  Återställ App Service-roller och tjänster

Om Azure Stack-lagring har använts för Funktionsappar lagring, måste du även vidta åtgärder för att återställa Funktionsappar.

## <a name="restore-the-app-service-databases"></a>Återställ App Service-databaser
App-tjänsten SQL Server-databaser ska återställas på en klar SQL Server-instans för produktion. 

Efter [förbereda SQL Server-instansen](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) ska vara värd för App Service-databaser, Använd dessa steg för att återställa databaser från en säkerhetskopia:

1. Logga in på den SQL Server som är värd för de återställda App Service-databaserna med administratörsbehörighet.
2. Använd följande kommandon för att återställa App Service-databaser från en kommandotolk med administratörsbehörighet:
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Kontrollera att både App Service-databaser har återställts och avsluta SQL Server Management Studio.

> [!NOTE]
> Återställa från failover-kluster Instansfel [instruktionerna](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017). 

## <a name="restore-the-app-service-file-share-content"></a>Återställ App Service-filinnehåll för resursen
Efter [förbereda filservern](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) ska vara värd för App Service-filresurs, måste du återställa filresursen filinnehållet klient från en säkerhetskopia. Du kan använda metoden som finns tillgängliga att kopiera filerna till den nyligen skapade filresursplatsen för App Service. Kör det här exemplet på filservern använder PowerShell och robocopy att ansluta till en fjärransluten resurs och kopiera filerna till resursen:

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

Förutom att kopiera filresurs filinnehållet, måste du också återställa behörigheter på filresursen själva. Gör detta genom att öppna en administrativ kommandotolk på filserverdatorn och kör den **ReACL.cmd** fil. Den **ReACL.cmd** filen finns i installationsfilerna för App Service i den **BCDR** directory.

## <a name="restore-app-service-roles-and-services"></a>Återställ App Service-roller och tjänster
När App Service-databaser och dela innehåll har återställts, därefter måste du använda PowerShell för att återställa App Service-roller och tjänster. De här stegen återställer App Service-hemligheter och konfigurationer.  

1. Logga in på den App Service-styrenheten **CN0 VM** VM som **roleadmin** med det lösenord du angav under installationen av App Service. 
    > [!TIP]
    > Du behöver ändra nätverkssäkerhetsgruppen för den virtuella datorn för att tillåta RDP-anslutningar. 
2. Kopiera den **SystemSecrets.JSON** filen lokalt till VM-styrenhet. Du måste ange sökvägen till filen som den `$pathToExportedSecretFile` parameter i nästa steg. 
3. Använd följande kommandon i en förhöjd PowerShell-konsolfönster för att återställa App Service-roller och tjänster:

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> Vi rekommenderar starkt att Stäng det här PowerShell-sessionen när kommandot har slutförts.

## <a name="restore-function-apps"></a>Återställa Funktionsappar 
App Service för Azure Stack har inte stöd för återställning av appar för klient-användare eller andra data än filinnehåll för resursen. Därför kan dessa måste säkerhetskopieras och återställas utanför App Service säkerhetskopiering och återställning. Om Azure Stack-lagring har använts för Funktionsappar lagring, bör följande steg vidtas för att återställa förlorade data:

1. Skapa ett nytt lagringskonto som ska användas av Funktionsappen. Den här lagringen kan vara Azure Stack-lagring, Azure storage eller all lagring som är kompatibla.
2. Hämta anslutningssträngen för lagring.
3. Öppna funktion-portal, bläddra till funktionsappen.
4. Bläddra till den **plattformsfunktioner** fliken och klicka på **programinställningar**.
5. Ändra **AzureWebJobsDashboard** och **AzureWebJobsStorage** till ny anslutningssträng och klickar på **spara**.
6. Växla till **översikt**.
7. Starta om appen. Det kan ta flera försök att rensa alla fel.

## <a name="next-steps"></a>Nästa steg
[Översikt över App Service i Azure Stack](azure-stack-app-service-overview.md)