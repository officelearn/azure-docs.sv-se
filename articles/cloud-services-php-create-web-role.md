---
title: "Skapa Azure webb- och arbetsroller roller för PHP | Microsoft Docs"
description: "En guide för att skapa roller för PHP webb- och arbetsroller i ett Azure-molntjänst och konfigurera PHP-körning."
services: 
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 214fdcfe20f3fa4ebcbe41308404f8b7e7d15310
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-php-web-and-worker-roles"></a>Så här skapar du PHP webb- och arbetsroller roller
## <a name="overview"></a>Översikt
Den här guiden visar hur du skapar PHP webb eller arbetare roller i en Windows-utvecklingsmiljö, välja en viss version av PHP från ”inbyggd” versioner som är tillgängliga, ändra PHP-konfiguration, aktivera tillägg och slutligen kan distribuera till Azure. Det beskriver också hur du konfigurerar en webb- eller arbetarroll roll om du vill använda en PHP-körning (med anpassad konfiguration och tillägg) som du anger.

## <a name="what-are-php-web-and-worker-roles"></a>Vad är PHP webb- och arbetsroller roller?
Azure tillhandahåller tre beräkningsmodeller för program som körs: Azure App Service, Azure virtuella datorer och Azure Cloud Services. Alla tre modeller stöder PHP. Molntjänster som innehåller webb-och arbetsroller innehåller *plattform som en tjänst (PaaS)*. I en molntjänst tillhandahåller en webbroll en dedikerad Internet Information Services (IIS) webbserver som värd för frontend-webbservrar program. En arbetsroll kan köra asynkrona, tidskrävande eller beständiga uppgifter oberoende av användarinteraktion eller indata.

Mer information om dessa alternativ finns [beräkning som värd för alternativen i Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Ladda ner Azure SDK för PHP
Den [Azure SDK för PHP] består av flera komponenter. Den här artikeln använder två av dem: Azure PowerShell och Azure emulatorerna. Dessa två komponenter kan installeras via Microsoft Web Platform Installer. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Skapa ett projekt med molntjänster
Det första steget i att skapa en PHP-webbplats eller arbetsprocess roll är att skapa en Azure Service-projekt. Azure Service-projekt som fungerar som en logisk behållare för webb-och arbetsroller och innehåller projektets [service definition (.csdef)] och [tjänstkonfiguration (.cscfg)] filer.

Skapa ett nytt projekt i Azure-tjänsten, köra Azure PowerShell som administratör och kör följande kommando:

    PS C:\>New-AzureServiceProject myProject

Det här kommandot skapar en ny katalog (`myProject`) som du kan lägga till webb-och arbetsroller.

## <a name="add-php-web-or-worker-roles"></a>Lägga till PHP-webbplats eller arbetsprocess roller
Om du vill lägga till en PHP-webbroll till ett projekt, kör du följande kommando från i projektets rotkatalog:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Använd följande kommando för en arbetsroll:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> Den `roleName` parametern är valfri. Om det utelämnas ska rollnamnet skapas automatiskt. Första webbrollen skapat kommer att `WebRole1`, andra blir `WebRole2`och så vidare. Den första worker-rollen skapats kommer att `WorkerRole1`, andra blir `WorkerRole2`och så vidare.
>
>

## <a name="specify-the-built-in-php-version"></a>Ange den inbyggda PHP-versionen
När du lägger till en PHP-webbplats eller arbetsprocess roll till ett projekt ändras projektfiler konfiguration så att PHP kommer att installeras på varje webb eller arbetare instans av programmet när det distribueras. Om du vill se versionen av PHP som installeras som standard, kör du följande kommando:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Utdata från kommandot ovan ser ut ungefär som visas nedan. I det här exemplet i `IsDefault` -flaggan inställd på `true` för PHP 5.3.17, som anger att det är den standard PHP-version som installeras.

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

Du kan ange PHP-körtidsversion för PHP-versioner som anges. Till exempel för att ange PHP-version (för en roll med namnet `roleName`) till 5.4.0, Använd följande kommando:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Tillgängliga PHP-versioner kan ändras i framtiden.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Anpassa inbyggda PHP-körning
Du har fullständig kontroll över konfigurationen för PHP-körningen som installeras när du följer du stegen ovan, även ändringar av `php.ini` inställningar och aktivering av tillägg.

Följ dessa steg om du vill anpassa den inbyggda PHP-körningen:

1. Lägg till en ny mapp med namnet `php`, i den `bin` katalogen för webbrollen. Lägga till rollens rotkatalogen för en arbetsroll.
2. I den `php` mapp, skapa en annan mapp med namnet `ext`. Placera alla `.dll` tilläggsfiler (t.ex. `php_mongo.dll`) som du vill aktivera i den här mappen.
3. Lägg till en `php.ini` filen till den `php` mapp. Aktivera alla anpassade tillägg och ange en PHP-direktiv i filen. Till exempel om du vill aktivera `display_errors` på och aktivera den `php_mongo.dll` tillägg, innehållet i din `php.ini` filen blir som följer:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Alla inställningar som du inte uttryckligen anger i den `php.ini` filen som du anger kommer automatiskt anges till sina standardvärden. Tänk dock på att du kan lägga till en fullständig `php.ini` fil.
>
>

## <a name="use-your-own-php-runtime"></a>Använda din egen PHP-körning
I vissa fall kan i stället för att välja en inbyggd PHP-körning och konfigurera det som beskrivs ovan, vill du ange egna PHP-körning. Exempelvis kan du använda samma PHP-körning i en webb- eller arbetarroll roll som du använder i din utvecklingsmiljö. På så sätt blir det lättare att se till att programmet inte att ändra beteende i din produktionsmiljö.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurera en webbroll om du vill använda din egen PHP-körning
Följ dessa steg om du vill konfigurera en webbroll om du vill använda en PHP-körning som du anger:

1. Skapa ett Azure Service-projekt och Lägg till en PHP-webbroll enligt beskrivningen tidigare i det här avsnittet.
2. Skapa en `php` mapp i den `bin` mapp som finns i rotkatalogen för din webbroll och sedan lägga till PHP-körning (alla binärfiler, konfigurationsfiler, undermappar osv.) den `php` mapp.
3. (VALFRITT) Om PHP-körning använder den [Drivers Microsoft för PHP för SQL Server][sqlsrv drivers], behöver du konfigurera din webbroll installera [SQL Server Native Client 2012] [ sql native client] när den har etablerats. Det gör du genom att lägga till den [sqlncli.msi x64 installer] till den `bin` mapp i rotkatalogen för din webbroll. Startskript som beskrivs i nästa steg körs tyst installationsprogrammet när rollen har etablerats. Om PHP-körning inte använder Microsoft Drivers för PHP för SQL Server, kan du ta bort följande rad från skriptet som visas i nästa steg:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definiera en startåtgärd som konfigurerar [Internet Information Services (IIS)] [ iis.net] du använder PHP-körning för att hantera begäranden om `.php` sidor. Gör detta genom att öppna den `setup_web.cmd` fil (i den `bin` filen i rotkatalogen för din webbroll) i en textredigerare och Ersätt innehållet med följande skript:

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
6. Publicera programmet som beskrivs i den [publicera programmet](#publish-your-application) nedan.

> [!NOTE]
> Den `download.ps1` skript (i det `bin` mapp i rotkatalogen för rollen webbserver) kan tas bort när du har följt stegen ovan för att använda din egen PHP-körning.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurera en arbetsroll om du vill använda din egen PHP-körning
Följ dessa steg om du vill konfigurera en arbetsroll om du vill använda en PHP-körning som du anger:

1. Skapa ett Azure Service-projekt och Lägg till en PHP-arbetsroll som beskrivs tidigare i det här avsnittet.
2. Skapa en `php` mapp i rotkatalogen för worker-rollen och Lägg sedan till PHP-körning (alla binärfiler, konfigurationsfiler, undermappar osv.) den `php` mapp.
3. (VALFRITT) Om PHP-körning använder [Drivers Microsoft för PHP för SQL Server][sqlsrv drivers], behöver du konfigurera worker-rollen för att installera [SQL Server Native Client 2012] [ sql native client] när den har etablerats. Det gör du genom att lägga till den [sqlncli.msi x64 installer] till den arbetsroll rotkatalogen. Startskript som beskrivs i nästa steg körs tyst installationsprogrammet när rollen har etablerats. Om PHP-körning inte använder Microsoft Drivers för PHP för SQL Server, kan du ta bort följande rad från skriptet som visas i nästa steg:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definiera en startåtgärd som lägger till din `php.exe` körbara till den arbetsroll PATH-miljövariabeln när rollen har etablerats. Det gör du genom att öppna den `setup_worker.cmd` filen (i rotkatalogen för worker-rollen) i en textredigerare och Ersätt innehållet med följande skript:

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
5. Lägger till programfiler till rotkatalogen för worker-rollen.
6. Publicera programmet som beskrivs i den [publicera programmet](#publish-your-application) nedan.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Kör ditt program i emulatorerna beräkning och lagring
Azure emulatorerna ange en lokal miljö där du kan testa din Azure-program innan du distribuerar den till molnet. Det finns några skillnader mellan emulatorerna och Azure-miljön. För att förstå det bättre se [Använd Azure storage-emulatorn för utveckling och testning](storage/common/storage-use-emulator.md).

Observera att du måste ha installerat lokalt om du vill använda beräkningsemulatorn för PHP. Beräkningsemulatorn använder din lokala PHP-installation för att köra programmet.

Om du vill köra projektet i emulatorerna, kör du följande kommando från projektets rotkatalog:

    PS C:\MyProject> Start-AzureEmulator

Du kommer se utdata som liknar detta:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Du kan se ditt program som körs i emulatorn genom att öppna en webbläsare och bläddra till den lokala adressen visas (`http://127.0.0.1:81` i ovanstående exempel utdata).

Om du vill stoppa emulatorerna, kör du kommandot:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicera programmet
Om du vill publicera programmet måste du först importera dina publiceringsinställningar med hjälp av den [importera AzurePublishSettingsFile](https://msdn.microsoft.com/library/azure/dn790370.aspx) cmdlet. Sedan kan du publicera dina program med hjälp av den [Publish-AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) cmdlet. Information om att logga in finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Nästa steg
Mer information finns i [PHP Developer Center](/develop/php/).

[Azure SDK för PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[service definition (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[tjänstkonfiguration (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[sqlncli.msi x64 installer]: http://go.microsoft.com/fwlink/?LinkID=239648
