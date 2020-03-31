---
title: Installera .NET på Azure Cloud Services-roller | Microsoft-dokument
description: I den hÃ¤r artikeln beskrivs hur du installerar .NET Framework manuellt på molntjänstwebbplatsen och arbetarrollerna
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 06/22/2018
ms.author: tagore
ms.openlocfilehash: c830dc0ee38ad808579a62274e3db87d0696e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214710"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Installera .NET i Azure Cloud Services-roller
I den här artikeln beskrivs hur du installerar versioner av .NET Framework som inte levereras med Azure Guest OS. Du kan använda .NET på gästoperativsystemet för att konfigurera molntjänstwebb- och arbetsrollerna.

Du kan till exempel installera .NET Framework 4.6.2 på Gäst-OS-familjen 4, som inte kommer med någon utgåva av .NET Framework 4.6. (Gästoperativsystemets familj 5 levereras med .NET Framework 4.6.) Den senaste informationen om Azure Guest OS-utgåvorna finns i [Azure Guest OS release news](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Azure SDK 2.9 innehåller en begränsning för distribution av .NET Framework 4.6 i Gästoperativsystemet 4 eller tidigare. En korrigering för begränsningen finns på [webbplatsen Microsoft Docs.](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)

Om du vill installera .NET på webb- och arbetsrollerna inkluderar du .NET-webbinstallationsprogrammet som en del av molntjänstprojektet. Starta installationsprogrammet som en del av rollens startuppgifter. 

## <a name="add-the-net-installer-to-your-project"></a>Lägga till .NET-installationsprogrammet i projektet
Om du vill hämta webbinstallationsprogrammet för .NET Framework väljer du den version som du vill installera:

* [.NET Framework 4.8 webbinstallationsprogram](https://dotnet.microsoft.com/download/thank-you/net48)
* [.NET Framework 4.7.2 webbinstallationsprogram](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET Framework 4.6.2 webbinstallationsprogram](https://www.microsoft.com/download/details.aspx?id=53345)

Så här lägger du till installationsprogrammet för en *webbroll:*
  1. Högerklicka på *webbrollen* under **Roller** i molntjänstprojektet i **Solution Explorer**och välj **Lägg till** > **ny mapp**. Skapa en mapp med namnet **bin**.
  2. Högerklicka på lagerplatsmappen och välj **Lägg till** > **befintligt objekt**. Markera .NET-installationsprogrammet och lägg till det i lagerplatsmappen.
  
Så här lägger du till installationsprogrammet för en *arbetarroll:*
* Högerklicka på *arbetarrollen* och välj **Lägg till** > **befintligt objekt**. Välj .NET-installationsprogrammet och lägg till det i rollen. 

När filer läggs till på det här sättet i mappen med rollinnehåll läggs de automatiskt till i molntjänstpaketet. Filerna distribueras sedan till en konsekvent plats på den virtuella datorn. Upprepa den här processen för varje webb- och arbetsroll i molntjänsten så att alla roller har en kopia av installationsprogrammet.

> [!NOTE]
> Du bör installera .NET Framework 4.6.2 på din molntjänstroll även om ditt program är inriktat på .NET Framework 4.6. Gästoperativsystemet innehåller Knowledge [Base-uppdateringen 3098779](https://support.microsoft.com/kb/3098779) och [uppdatering 3097997](https://support.microsoft.com/kb/3097997). Problem kan uppstå när du kör .NET-programmen om .NET Framework 4.6 är installerat ovanpå Knowledge Base-uppdateringarna. Om du vill undvika dessa problem installerar du .NET Framework 4.6.2 i stället för version 4.6. Mer information finns i [knowledge base-artikeln 3118750](https://support.microsoft.com/kb/3118750) och [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Rollinnehåll med installationsfiler][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definiera startuppgifter för dina roller
Du kan använda startuppgifter för att utföra åtgärder innan en roll startar. Om du installerar .NET Framework som en del av startuppgiften säkerställs att ramverket installeras innan någon programkod körs. Mer information om startuppgifter finns [i Köra startuppgifter i Azure](cloud-services-startup-tasks.md). 

1. Lägg till följande innehåll i filen ServiceDefinition.csdef under noden **WebRole** eller **WorkerRole** för alla roller:
   
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
   
    Den föregående konfigurationen kör `install.cmd` konsolkommandot med administratörsbehörighet för att installera .NET Framework. Konfigurationen skapar också ett **LocalStorage-element** med namnet **NETFXInstall**. Startskriptet anger att temporärmappen ska använda den här lokala lagringsresursen. 
    
    > [!IMPORTANT]
    > Om du vill säkerställa korrekt installation av ramverket anger du resursens storlek till minst 1 024 MB.
    
    Mer information om startuppgifter finns i [Vanliga startuppgifter för Azure Cloud Services](cloud-services-startup-tasks-common.md).

2. Skapa en fil med namnet **install.cmd** och lägg till följande installationsskript i filen.

   Skriptet kontrollerar om den angivna versionen av .NET Framework redan är installerad på datorn genom att fråga registret. Om .NET Framework-versionen inte är installerad öppnas webbinstallationsprogrammet .NET Framework. Skriptet loggar all aktivitet till filen startuptasklog-(aktuellt datum och aktuellt).txt som lagras i Lokal lagring i **InstallLogs.**
   
   > [!IMPORTANT]
   > Använd en grundläggande textredigerare som Windows Anteckningar för att skapa filen install.cmd. Om du använder Visual Studio för att skapa en textfil och ändra tillägget till .cmd kan filen fortfarande innehålla ett ORDERMÄRKE PÅ UTF-8 byte. Det här märket kan orsaka ett fel när den första raden i skriptet körs. Undvik det här felet genom att göra den första raden i skriptet till en REM-sats som kan hoppas över av byteorderbearbetningen. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
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
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
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
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Lägg till filen install.cmd i varje roll med hjälp av **Lägg till** > **befintligt objekt** i **Lösningsutforskaren** enligt beskrivningen tidigare i det här avsnittet. 

    När det här steget är klart bör alla roller ha .NET-installationsfilen och filen install.cmd.

   ![Rollinnehåll med alla filer][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Konfigurera diagnostik för att överföra startloggar till Blob-lagring
För att förenkla felsökning av installationsproblem kan du konfigurera Azure Diagnostics för att överföra alla loggfiler som genereras av startskriptet eller .NET-installationsprogrammet till Azure Blob-lagring. Med den här metoden kan du visa loggarna genom att hämta loggfilerna från Blob-lagring i stället för att behöva fjärrskrivbord till rollen.


Om du vill konfigurera Diagnostik öppnar du filen diagnostics.wadcfgx och lägger till följande innehåll under noden **Kataloger:** 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Den här XML-koden konfigurerar diagnostik för att överföra filerna i loggkatalogen i **NETFXInstall-resursen** till diagnostiklagringskontot i **blob-behållaren netfx-install.**

## <a name="deploy-your-cloud-service"></a>Distribuera din molntjänst
När du distribuerar molntjänsten installerar startuppgifterna .NET Framework om det inte redan är installerat. Dina molntjänstroller *busy* är upptagna medan ramverket installeras. Om raminstallationen kräver en omstart kan tjänstrollerna också startas om. 

## <a name="additional-resources"></a>Ytterligare resurser
* [Installera .NET Framework][Installing the .NET Framework]
* [Ta reda på vilka .NET Framework-versioner som är installerade][How to: Determine Which .NET Framework Versions Are Installed]
* [Felsöka .NET Framework-installationer][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



