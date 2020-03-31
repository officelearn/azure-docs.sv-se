---
title: Skapa Azure-webb- och arbetsroller för PHP
description: En guide till att skapa PHP-webb- och arbetsroller i en Azure-molntjänst och konfigurera PHP-körningen.
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
ms.openlocfilehash: 54410e1e70a2ec0d3a9e2f853dc9556cd05996ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297262"
---
# <a name="create-php-web-and-worker-roles"></a>Skapa PHP webb- och arbetsroller

## <a name="overview"></a>Översikt

Den här guiden visar hur du skapar PHP-webb- eller arbetarroller i en Windows-utvecklingsmiljö, väljer en specifik version av PHP från de "inbyggda" versionerna som är tillgängliga, ändrar PHP-konfigurationen, aktiverar tillägg och slutligen distribuerar till Azure. Den beskriver också hur du konfigurerar en webb- eller arbetarroll för att använda en PHP-körning (med anpassad konfiguration och tillägg) som du anger.

Azure tillhandahåller tre beräkningsmodeller för program som körs: Azure App Service, Virtuella Azure-datorer och Azure Cloud Services. Alla tre modellerna stöder PHP. Molntjänster, som innehåller webb- och arbetsroller, tillhandahåller *plattform som en tjänst (PaaS)*. Inom en molntjänst tillhandahåller en webbroll en dedikerad IIS-webbserver (Internet Information Services) som är värd för frontend-webbprogram. En arbetarroll kan köra asynkrona, långvariga eller eviga uppgifter oberoende av användarinteraktion eller indata.

Mer information om dessa alternativ finns i [Alternativ för beräkningshosting som tillhandahålls av Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Ladda ned Azure SDK för PHP

[Azure SDK för PHP](https://github.com/Azure/azure-sdk-for-php) består av flera komponenter. Den här artikeln kommer att använda två av dem: Azure PowerShell och Azure emulatorer. Dessa två komponenter kan installeras via Installationsprogrammet för Microsoft Web Platform. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Skapa ett molntjänstprojekt

Det första steget i att skapa en PHP-webb- eller arbetsroll är att skapa ett Azure Service-projekt. ett Azure Service-projekt fungerar som en logisk behållare för webb- och arbetsroller och innehåller projektets [tjänstdefinition (.csdef)] och [tjänstkonfigurationsfiler (.cscfg).]

Om du vill skapa ett nytt Azure Service-projekt kör du Azure PowerShell som administratör och kör följande kommando:

    PS C:\>New-AzureServiceProject myProject

Det här kommandot skapar`myProject`en ny katalog ( ) som du kan lägga till webb- och arbetsroller till.

## <a name="add-php-web-or-worker-roles"></a>Lägga till PHP-webb- eller arbetarroller

Om du vill lägga till en PHP-webbroll i ett projekt kör du följande kommando inifrån projektets rotkatalog:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Använd det här kommandot för en arbetsroll:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> Parametern `roleName` är valfri. Om det utelämnas genereras rollnamnet automatiskt. Den första webbrollen `WebRole1`som skapas `WebRole2`kommer att vara , den andra kommer att vara , och så vidare. Den första arbetarrollen `WorkerRole1`som skapas `WorkerRole2`kommer att vara , den andra kommer att vara , och så vidare.
>
>

## <a name="use-your-own-php-runtime"></a>Använd din egen PHP runtime

I vissa fall, istället för att välja en inbyggd PHP runtime och konfigurera den som beskrivs ovan, kanske du vill ge din egen PHP runtime. Du kan till exempel använda samma PHP-körning i en webb- eller arbetarroll som du använder i utvecklingsmiljön. Detta gör det enklare att se till att programmet inte ändrar beteendet i produktionsmiljön.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurera en webbroll för att använda din egen PHP-körning

Så här konfigurerar du en webbroll så att du använder en PHP-körning som du anger:

1. Skapa ett Azure Service-projekt och lägg till en PHP-webbroll som beskrivits tidigare i det här avsnittet.
2. Skapa `php` en mapp `bin` i mappen som finns i webbrollens rotkatalog och lägg sedan till din PHP-körning (alla `php` binärfiler, konfigurationsfiler, undermappar osv.) i mappen.
3. (VALFRITT) Om din PHP-körning använder [Microsoft Drivers för PHP för SQL Server][sqlsrv drivers]måste du konfigurera webbrollen för att installera SQL Server Native Client [2012][sql native client] när den etableras. Det gör du genom att lägga till [installationsprogrammet sqlncli.msi x64] i `bin` mappen i webbrollens rotkatalog. Startskriptet som beskrivs i nästa steg kör tyst installationsprogrammet när rollen etableras. Om din PHP-körning inte använder Microsoft Drivers för PHP för SQL Server kan du ta bort följande rad från skriptet som visas i nästa steg:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definiera en startuppgift som konfigurerar [Internet Information Services (IIS)][iis.net] för `.php` att använda din PHP-körning för att hantera begäranden om sidor. Det gör du `setup_web.cmd` genom att `bin` öppna filen (i filen i webbrollens rotkatalog) i en textredigerare och ersätta innehållet med följande skript:

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
5. Lägg till dina programfiler i webbrollens rotkatalog. Detta kommer att vara webbserverns rotkatalog.
6. Publicera din ansökan enligt beskrivningen i avsnittet [Publicera din ansökan](#publish-your-application) nedan.

> [!NOTE]
> Skriptet `download.ps1` (i `bin` mappen för webbrollens rotkatalog) kan tas bort när du följer stegen som beskrivs ovan för att använda din egen PHP-körning.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurera en arbetarroll för att använda din egen PHP-körning

Så här konfigurerar du en arbetsroll så att du använder en PHP-körning som du anger:

1. Skapa ett Azure Service-projekt och lägg till en PHP-arbetsroll som beskrivits tidigare i det här avsnittet.
2. Skapa `php` en mapp i arbetarrollens rotkatalog och lägg sedan till din PHP-körning (alla binärfiler, `php` konfigurationsfiler, undermappar osv.) i mappen.
3. (VALFRITT) Om din PHP-körning använder [Microsoft Drivers för PHP för SQL Server][sqlsrv drivers]måste du konfigurera arbetarrollen för att installera SQL Server Native Client [2012][sql native client] när den etableras. Det gör du genom att lägga till [installationsprogrammet sqlncli.msi x64] i arbetarrollens rotkatalog. Startskriptet som beskrivs i nästa steg kör tyst installationsprogrammet när rollen etableras. Om din PHP-körning inte använder Microsoft Drivers för PHP för SQL Server kan du ta bort följande rad från skriptet som visas i nästa steg:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definiera en startuppgift `php.exe` som lägger till din körbara till arbetarrollens PATH-miljövariabel när rollen etableras. Det gör du `setup_worker.cmd` genom att öppna filen (i arbetarrollens rotkatalog) i en textredigerare och ersätta innehållet med följande skript:

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
5. Lägg till dina programfiler i arbetarrollens rotkatalog.
6. Publicera din ansökan enligt beskrivningen i avsnittet [Publicera din ansökan](#publish-your-application) nedan.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Kör ditt program i beräknings- och lagringsemulatorer

Azure-emulatorer tillhandahåller en lokal miljö där du kan testa ditt Azure-program innan du distribuerar det till molnet. Det finns vissa skillnader mellan emulatorer och Azure-miljön. Mer information om detta finns [i Använda Azure-lagringsemulatorn för utveckling och testning](storage/common/storage-use-emulator.md).

Observera att du måste ha PHP installerat lokalt för att kunna använda beräkningsemmulatorn. Beräkningsemulatorn kommer att använda din lokala PHP-installation för att köra ditt program.

Om du vill köra projektet i emulatorerna kör du följande kommando från projektets rotkatalog:

    PS C:\MyProject> Start-AzureEmulator

Du bör se utdata som liknar följande: 

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Du kan se ditt program som körs i emulatorn genom att öppna en`http://127.0.0.1:81` webbläsare och bläddra till den lokala adressen som visas i utdata (i exemplet utdata ovan).

Så här stoppar du emulatorerna:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicera programmet

Om du vill publicera programmet måste du först importera publiceringsinställningarna med hjälp av cmdleten [Import-AzurePublishSettingsFile.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) Sedan kan du publicera ditt program med hjälp av [cmdleten Publish-AzureServiceProject.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) Information om hur du loggar in finns i [Så här installerar och konfigurerar](/powershell/azure/overview)du Azure PowerShell .

## <a name="next-steps"></a>Nästa steg

Mer information finns i [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[tjänstdefinition (.csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[tjänstkonfiguration (.cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[sqlncli.msi x64 installationsprogrammet]: https://go.microsoft.com/fwlink/?LinkID=239648
