---
title: "Installera .NET på Azure Cloud Services roller | Microsoft Docs"
description: "Den här artikeln beskriver hur du manuellt installera .NET Framework på dina webb- och arbetsroller molntjänstroller"
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: adegeo
ms.openlocfilehash: cc4b62bc554757e6e394b78334f52f45aa08efe8
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Installera .NET på Azure Cloud Services roller
Den här artikeln beskriver hur du installerar .NET Framework-versioner som inte ingår i Azure-Gästoperativsystem. Du kan använda .NET på gäst-OS för att konfigurera dina webb- och arbetsroller molntjänstroller.

Du kan till exempel installera .NET 4.6.1 på gäst-OS-familjen 4, som inte ingår i någon version av .NET 4.6. (Gäst-OS-familjen 5 kommer med .NET 4.6). Den senaste informationen på Azure gäst-OS-versioner finns i [Azure Gästoperativsystem släpper nyheter](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Azure SDK 2.9 innehåller en begränsning för hur du distribuerar .NET 4.6 på gäst-OS-familjen 4 eller tidigare. En korrigering för begränsningen är tillgängligt på den [Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) plats.

Om du vill installera .NET på din webb- och arbetsroller roller inkludera .NET webbinstallationsprogram som en del av ditt molntjänstprojekt. Starta installationsprogrammet som en del av rollens Start uppgifter. 

## <a name="add-the-net-installer-to-your-project"></a>Lägg till .NET-installationsprogrammet till ditt projekt
Välj den version som du vill installera för att hämta webbinstallationsprogrammet för .NET Framework:

* [.NET 4.7.1 web installer](http://go.microsoft.com/fwlink/?LinkId=852095)
* [.NET 4.6.1 web installer](http://go.microsoft.com/fwlink/?LinkId=671729)

Att lägga till installationsprogrammet för en *web* roll:
  1. I **Solution Explorer**under **roller** i ditt molntjänstprojekt högerklickar du på din *web* rollen och välj **Lägg till** > **ny mapp**. Skapa en mapp med namnet **bin**.
  2. Högerklicka på bin-mappen och välj **Lägg till** > **befintlig artikel**. Välj .NET installationsprogrammet och lägga till bin-mappen.
  
Att lägga till installationsprogrammet för en *worker* roll:
* Högerklicka på din *worker* rollen och välj **Lägg till** > **befintlig artikel**. Välj .NET installationsprogrammet och lägga till i rollen. 

Om filer som läggs till på detta sätt till innehållsmappen roll, läggs de automatiskt till ditt moln-abonnemang. Filerna distribueras sedan till samma plats på den virtuella datorn. Upprepa proceduren för varje roll för webb- och arbetsroller i din molntjänst så att alla roller har en kopia av installationsprogrammet.

> [!NOTE]
> Du bör installera .NET 4.6.1 på rolltjänst för molnet även om ditt program mål .NET 4.6. Gäst-OS innehåller kunskapsbasen [uppdatera 3098779](https://support.microsoft.com/kb/3098779) och [uppdatera 3097997](https://support.microsoft.com/kb/3097997). Problem kan uppstå när du kör .NET-program om .NET 4.6 installeras ovanpå Knowledge Base-uppdateringar. Undvik dessa problem genom att installera .NET 4.6.1 snarare än version 4.6. Mer information finns i [Knowledge Base-artikel 3118750](https://support.microsoft.com/kb/3118750).
> 
> 

![Rollen innehållet med installer-filer][1]

## <a name="define-startup-tasks-for-your-roles"></a>Ange start-aktiviteter för dina roller
Du kan använda Start uppgifter för att utföra åtgärder innan du startar en roll. Installera .NET Framework som en del av startaktivitet säkerställer att framework är installerad innan alla programkoden körs. Mer information om uppgifter för start finns [köra startade uppgifter i Azure](cloud-services-startup-tasks.md). 

1. Lägg till följande innehåll i filen ServiceDefinition.csdef under den **WebRole** eller **WorkerRole** nod för alla roller:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    Ovanstående konfiguration körs kommandot konsolen `install.cmd` med administratörsbehörighet för att installera .NET Framework. Konfigurationen skapar även en **LocalStorage** element med namnet **NETFXInstall**. Startskriptet anger tillfällig mapp för att använda den här resursen för lokal lagring. 
    
    > [!IMPORTANT]
    > Ange storleken på den här resursen till minst 1 024 MB rätt att installationen av framework.
    
    Mer information om uppgifter för start finns [vanliga Azure Cloud Services startade uppgifter](cloud-services-startup-tasks-common.md).

2. Skapa en fil med namnet **install.cmd** och Lägg till följande installera skript till filen.

    Skriptet kontrollerar om den angivna versionen av .NET Framework redan är installerad på datorn genom att fråga registret. Om .NET-versionen inte har installerats, öppna webbinstallationsprogrammet för .NET. För att felsöka eventuella problem, skriptet loggar alla aktiviteter i filen startuptasklog-(aktuellt datum och tid) .txt som lagras i **InstallLogs** lokal lagring.
  
    > [!IMPORTANT]
    > Använd en textredigerare, grundläggande t.ex Windows för att skapa filen install.cmd. Om du använder Visual Studio för att skapa en textfil och ändra filnamnstillägget till .cmd kan kan filen fortfarande innehålla en UTF-8 byte-ordningsmarkering. Detta märke kan orsaka ett fel när den första raden i skriptet körs. För att undvika det här felet kan se den första raden i skriptet instruktionen b, som kan vara hoppas över av Orderbearbetning byte. 
    > 
    >
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    REM ***** To install .NET 4.7 set the variable netfx to "NDP47" *****
    REM ***** To install .NET 4.7.1 set the variable netfx to "NDP47" *****
    set netfx="NDP471"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
    
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP471" goto NDP471
    if %netfx%=="NDP47" goto NDP47
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
    
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
    
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
    
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
    goto logtimestamp
    
    :NPD47
    set "netfxinstallfile=NDP47-KB3186500-Web.exe"
    set netfxregkey="0x707FE"
    goto logtimestamp
    
    :NDP471
    set "netfxinstallfile=NDP471-KB4033344-Web.exe"
    set netfxregkey="0x709fc"
    goto logtimestamp
    
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
    
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
    
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
    
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
    
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
    
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    
    :exit
    EXIT /B 0
    ```

3. Lägga till filen install.cmd på varje roll med hjälp av **Lägg till** > **befintligt objekt** i **Solution Explorer** som beskrivits tidigare i det här avsnittet. 

    När det här steget har slutförts ha alla roller för .NET installer-fil och filen install.cmd.

   ![Rollen innehållet med alla filer][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Konfigurera diagnostik för att överföra Start loggar till Blob storage
Du kan konfigurera Azure-diagnostik för att överföra alla loggfiler som genererats av startskriptet eller .NET installationsprogrammet till Azure Blob storage för att underlätta felsökning av installationsproblem. Med den här metoden kan visa du loggarna genom att hämta filerna från Blob storage i stället för att behöva fjärrskrivbord till rollen.

Konfigurera diagnostik, öppna filen diagnostics.wadcfgx och Lägg till följande innehåll under den **kataloger** nod: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Den här XML konfigurerar diagnostik för att överföra filer i loggkatalogen i den **NETFXInstall** resurs till diagnostiklagringskonto i den **netfx installera** blob-behållare.

## <a name="deploy-your-cloud-service"></a>Distribuera din molntjänst
När du distribuerar din molntjänst installera .NET Framework i Start-uppgifter om den inte redan är installerad. Dina molntjänstroller finns i den *upptagen* tillstånd medan ramverket installeras. Om installationen av framework kräver en omstart, service-roller kan också starta om. 

## <a name="additional-resources"></a>Ytterligare resurser
* [Installera .NET Framework][Installing the .NET Framework]
* [Ta reda på vilka versioner av .NET Framework är installerad][How to: Determine Which .NET Framework Versions Are Installed]
* [Felsökning av .NET Framework-installationer][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png
