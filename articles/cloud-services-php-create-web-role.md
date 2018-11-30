---
title: Skapa Azure webb- och arbetsroller för PHP
description: En guide för att skapa PHP webb- och worker-roller i en Azure-molntjänst och konfigurera PHP-körning.
services: ''
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 83834104dd73e4381947903196ad35c3497b64a1
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425684"
---
# <a name="create-php-web-and-worker-roles"></a>Skapa PHP webb- och arbetsroller

## <a name="overview"></a>Översikt

Den här guiden visar hur du skapar PHP webb- eller worker-roller i en Windows-utvecklingsmiljö, välja en specifik version av PHP från ”inbyggd” versioner som är tillgängliga, ändra PHP-konfiguration, aktivera tillägg och slutligen kan distribuera till Azure. Det beskriver också hur du konfigurerar en web- eller worker-roll om du vill använda en PHP-körning (med anpassad konfiguration och tillägg) som du anger.

Azure har tre beräkningsmodeller för att köra program: Azure App Service, Azure virtuella datorer och Azure Cloud Services. Alla tre modeller stöder PHP. Molntjänster, vilket innefattar webb-och arbetsroller, erbjuder *plattform som en tjänst (PaaS)*. I en molntjänst tillhandahåller en webbroll en dedikerad webbserver för Internet Information Services (IIS) som värd frontend-webbprogram. En arbetsroll kan köra asynkrona, tidskrävande eller beständiga uppgifter oberoende av användarinteraktion eller indata.

Mer information om alternativen finns i [Datorvärdalternativ från Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Ladda ner Azure SDK för PHP

Den [Azure SDK för PHP](php-download-sdk.md) består av flera komponenter. Den här artikeln används två av dem: Azure PowerShell och Azure-emulatorer. Dessa två komponenter kan installeras via Microsoft Web Platform Installer. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Skapa en Cloud Services-projekt

Det första steget i att skapa en PHP web- eller worker-roll är att skapa ett Azure Service-projekt. ett Azure Service-projekt som fungerar som en logisk behållare för webb- och worker-roller och innehåller projektets [tjänstdefinitionsfilen (.csdef)] och [service-konfiguration (.cscfg)] filer.

Skapa ett nytt Azure Service-projekt, köra Azure PowerShell som administratör och kör följande kommando:

    PS C:\>New-AzureServiceProject myProject

Det här kommandot skapar en ny katalog (`myProject`) där du kan lägga till webb-och arbetsroller.

## <a name="add-php-web-or-worker-roles"></a>Lägga till PHP web- eller worker-roller

Kör följande kommando från i projektets rotkatalog för att lägga till en PHP-webbroll till ett projekt:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Använd följande kommando för en worker-roll:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> Den `roleName` parametern är valfri. Om det utelämnas ska rollnamnet skapas automatiskt. Första webbrollen skapat kommer att `WebRole1`, andra blir `WebRole2`och så vidare. Den första worker-roll som skapas kommer att `WorkerRole1`, andra blir `WorkerRole2`och så vidare.
>
>

## <a name="specify-the-built-in-php-version"></a>Ange den inbyggda PHP-versionen

När du lägger till en PHP web- eller worker-roll till ett projekt ändras i projektets konfigurationsfiler så att PHP kommer att installeras på varje web- eller worker-instans av programmet när det distribueras. Om du vill se vilken version av PHP som kommer att installeras som standard, kör du följande kommando:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Utdata från kommandot ovan ser ut ungefär som det som anges nedan. I det här exemplet på `IsDefault` är inställd på `true` för PHP 5.3.17, som anger att det är PHP-standardversionen installerad.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

Du kan ange PHP-körtidsversion till någon av PHP-versioner som listas. Till exempel vill ange PHP-version (för en roll med namnet `roleName`) till 5.4.0, använder du följande kommando:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Tillgängliga PHP-versioner kan ändras i framtiden.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Anpassa den inbyggda PHP-körningen

Du har fullständig kontroll över konfigurationen av PHP-körning som installeras när du följer du stegen ovan, även ändringar av `php.ini` inställningar och aktivering av tillägg.

Följ dessa steg om du vill anpassa den inbyggda PHP-körningen:

1. Lägg till en ny mapp som heter `php`, i den `bin` katalogen för webbrollen. Lägga till den till rollens rotkatalogen för worker-roll.
2. I den `php` mapp, skapa en annan mapp med namnet `ext`. Placera någon `.dll` tilläggsfiler (t.ex. `php_mongo.dll`) som du vill aktivera i den här mappen.
3. Lägg till en `php.ini` filen till den `php` mapp. Aktivera anpassade tillägg och konfigurera alla PHP-direktiv i den här filen. Exempel: Om du vill aktivera `display_errors` på och aktivera den `php_mongo.dll` tillägg, innehållet i din `php.ini` filen skulle vara följande:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Alla inställningar som du inte uttryckligen anger i den `php.ini` fil att du anger kommer automatiskt anges till sina standardvärden. Men tänk på att du kan lägga till ett komplett `php.ini` fil.
>
>

## <a name="use-your-own-php-runtime"></a>Använd din egen PHP-körning

I vissa fall kan i stället för att välja en inbyggd PHP-körning och konfigurera det som beskrivs ovan, kan du ange egna PHP-körning. Exempelvis kan du använda samma PHP-körning i en web- eller worker-roll som du använder i din utvecklingsmiljö. Detta gör det enklare att se till att programmet inte ändrar beteende i din produktionsmiljö.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurera en webbroll om du vill använda din egen PHP-körning

Följ dessa steg om du vill konfigurera en webbroll om du vill använda en PHP-körning som du anger:

1. Skapa ett Azure Service-projekt och Lägg till en PHP-webbroll enligt beskrivningen tidigare i det här avsnittet.
2. Skapa en `php` mapp i den `bin` mapp som finns i rotkatalogen för din webbroll och Lägg sedan till din PHP-körning (alla binärfiler, konfigurationsfiler, undermappar osv.) till den `php` mapp.
3. (VALFRITT) Om din PHP-körning använder den [Drivers Microsoft för PHP för SQL Server][sqlsrv drivers], behöver du konfigurera din webbroll installera [SQL Server Native Client 2012] [ sql native client] när den har etablerats. Gör detta genom att lägga till den [sqlncli.msi x64 installer] till den `bin` mapp i rotkatalogen för din webbroll. Startskript som beskrivs i nästa steg körs tyst installationsprogrammet när rollen har etablerats. Om din PHP-körning inte använder Microsoft-Drivers för PHP för SQL Server, kan du ta bort följande rad från skriptet visas i nästa steg:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definiera en startåtgärd som konfigurerar [Internet Information Services (IIS)] [ iis.net] du använder PHP-körning för att hantera begäranden för `.php` sidor. Gör detta genom att öppna den `setup_web.cmd` fil (i den `bin` filen rotkatalogen för din webbroll) i en textredigerare och Ersätt innehållet med följande skript:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Lägger till programfiler till rotkatalogen för din webbroll. Det här är webbserverns rotkatalog.
6. Publicera programmet enligt beskrivningen i den [publicera programmet](#publish-your-application) nedan.

> [!NOTE]
> Den `download.ps1` skript (i den `bin` mappen web-roll rotkatalogen) kan tas bort när du har följt stegen ovan för att använda din egen PHP-körning.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurera en arbetsroll fungerar för att använda din egen PHP-körning

Följ dessa steg om du vill konfigurera en arbetsroll fungerar för att använda en PHP-körning som du anger:

1. Skapa ett Azure Service-projekt och Lägg till en PHP-arbetsroll som beskrivs tidigare i det här avsnittet.
2. Skapa en `php` mapp i rotkatalogen för worker-roll, och sedan lägga till PHP-körning (alla binärfiler, konfigurationsfiler, undermappar osv.) i den `php` mapp.
3. (VALFRITT) Om din PHP-körning använder [Drivers Microsoft för PHP för SQL Server][sqlsrv drivers], behöver du konfigurera worker-roll för att installera [SQL Server Native Client 2012] [ sql native client] när den har etablerats. Gör detta genom att lägga till den [sqlncli.msi x64 installer] till rotkatalogen för worker-roll. Startskript som beskrivs i nästa steg körs tyst installationsprogrammet när rollen har etablerats. Om din PHP-körning inte använder Microsoft-Drivers för PHP för SQL Server, kan du ta bort följande rad från skriptet visas i nästa steg:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definiera en startåtgärd som lägger till din `php.exe` körbara till worker-roll PATH-miljövariabeln när rollen har etablerats. Gör detta genom att öppna den `setup_worker.cmd` (i rotkatalogen för worker-roll) i en textredigerare och Ersätt innehållet med följande skript:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Lägger till programfiler till rotkatalogen för din arbetsroll.
6. Publicera programmet enligt beskrivningen i den [publicera programmet](#publish-your-application) nedan.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Kör ditt program i beräknings- och emulatorer

Azure-emulatorer tillhandahåller en lokal miljö där du kan testa ditt Azure-program innan du distribuerar den till molnet. Det finns några skillnader mellan emulatorerna och Azure-miljön. För att förstå det bättre se [använder Azure storage-emulatorn för utveckling och testning](storage/common/storage-use-emulator.md).

Observera att du måste ha installerats lokalt för att använda beräkningsemulatorn för PHP. Beräkningsemulatorn använder din lokala PHP-installation för att köra programmet.

Kör projektet i emulatorerna genom att köra följande kommando från projektets rotkatalog:

    PS C:\MyProject> Start-AzureEmulator

Du ser utdata som liknar detta:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Du kan se ditt program som körs i emulatorn genom att öppna en webbläsare och gå till den lokala adressen som visas i utdata (`http://127.0.0.1:81` i ovanstående Exempelutdata).

Om du vill stoppa emulatorerna, kör du kommandot:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicera programmet

För att publicera ditt program, måste du först importera dina publiceringsinställningar med hjälp av den [Import AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) cmdlet. Och sedan publicera dina program med hjälp av den [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) cmdlet. Information om att logga in finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Nästa steg

Mer information finns i den [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[tjänstdefinitionsfilen (.csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[service-konfiguration (.cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[sqlncli.msi x64 installer]: https://go.microsoft.com/fwlink/?LinkID=239648
