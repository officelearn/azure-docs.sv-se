---
title: Vanliga startuppgifter för Molntjänster | Microsoft-dokument
description: Innehåller några exempel på vanliga startuppgifter som du kanske vill utföra i webbrollen eller arbetsrollen för molntjänster.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 4fe1ee3ccf2849943959889838ba0f22fb64bb9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273064"
---
# <a name="common-cloud-service-startup-tasks"></a>Vanliga startuppgifter för Molntjänsten
Den här artikeln innehåller några exempel på vanliga startuppgifter som du kanske vill utföra i molntjänsten. Du kan använda startuppgifter för att utföra åtgärder innan en roll startar. Åtgärder som du kanske vill utföra är att installera en komponent, registrera COM-komponenter, ange registernycklar eller starta en tidskrävande process. 

Se [den här artikeln](cloud-services-startup-tasks.md) om du vill veta hur startuppgifter fungerar och specifikt hur du skapar de poster som definierar en startuppgift.

> [!NOTE]
> Startuppgifter är inte tillämpliga på virtuella datorer, bara på Molntjänstwebb- och arbetarroller.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definiera miljövariabler innan en roll startar
Om du behöver definiera miljövariabler för [Environment] en viss uppgift använder du miljöelementet i [aktivitetselementet.]

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Variabler kan också använda ett [giltigt Azure XPath-värde](cloud-services-role-config-xpath.md) för att referera till något om distributionen. I stället `value` för att använda attributet definierar du ett [underordnat RoleInstanceValue-element.]

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Konfigurera IIS-start med AppCmd.exe
Kommandoradsverktyget [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) kan användas för att hantera IIS-inställningar vid start på Azure. *AppCmd.exe* ger bekväm kommandoradsåtkomst till konfigurationsinställningar för användning i startuppgifter på Azure. Med *AppCmd.exe*kan webbplatsinställningar läggas till, ändras eller tas bort för program och webbplatser.

Det finns dock några saker att se upp för i användningen av *AppCmd.exe* som en startuppgift:

* Startuppgifter kan köras mer än en gång mellan omstarter. Till exempel när en roll återvinns.
* Om en *AppCmd.exe-åtgärd* utförs mer än en gång kan den generera ett fel. Om du till exempel försöker lägga till ett avsnitt *i Web.config* två gånger kan det skapa ett fel.
* Startaktiviteter misslyckas om de returnerar en utgångskod eller **felnivå**som inte är noll . Till exempel när *AppCmd.exe* genererar ett fel.

Det är en bra idé att kontrollera **felnivån** efter att ha *ringt AppCmd.exe*, vilket är lätt att göra om du radbryt samtalet till *AppCmd.exe* med en *CMD-fil.* Om du upptäcker ett känt **feltlevel-svar** kan du ignorera det eller skicka tillbaka det.

Den felnivå som returneras av *AppCmd.exe* visas i filen winerror.h och kan även ses på [MSDN](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Exempel på hantering av felnivån
I det här exemplet läggs ett komprimeringsavsnitt och en komprimeringspost för JSON till *filen Web.config,* med felhantering och loggning.

De relevanta avsnitten i [filen ServiceDefinition.csdef] visas här, vilket inkluderar `elevated` att ange attributet [executionContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task) för att ge *AppCmd.exe* tillräcklig behörighet för att ändra inställningarna i *filen Web.config:*

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

*Batchfilen Startup.cmd* använder *AppCmd.exe* för att lägga till ett komprimeringsavsnitt och en komprimeringspost för JSON i *filen Web.config.* Den förväntade **felnivån** på 183 är inställd på noll med hjälp av VERIFY. EXE-kommandoradsprogram. Oväntade felnivåer loggas till StartupErrorLog.txt.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Lägga till brandväggsregler
I Azure finns det i själva verket två brandväggar. Den första brandväggen styr anslutningar mellan den virtuella datorn och omvärlden. Den här brandväggen styrs av [slutpunktselementet] i filen [ServiceDefinition.csdef.]

Den andra brandväggen styr anslutningar mellan den virtuella datorn och processerna inom den virtuella datorn. Den här brandväggen `netsh advfirewall firewall` kan styras av kommandoradsverktyget.

Azure skapar brandväggsregler för de processer som startats i dina roller. När du till exempel startar en tjänst eller ett program skapar Azure automatiskt de brandväggsregler som krävs för att tjänsten ska kunna kommunicera med Internet. Om du skapar en tjänst som startas av en process utanför rollen (till exempel en COM+-tjänst eller en schemalagd aktivitet i Windows) måste du dock manuellt skapa en brandväggsregel för att tillåta åtkomst till den tjänsten. Dessa brandväggsregler kan skapas med hjälp av en startuppgift.

En startuppgift som skapar en brandväggsregel måste ha en [körningKontextuppgift][Task] för **förhöjd**. Lägg till följande startuppgift i filen [ServiceDefinition.csdef.]

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Om du vill lägga till brandväggsregeln måste du använda lämpliga `netsh advfirewall firewall` kommandon i startbatchfilen. I det här exemplet kräver startuppgiften säkerhet och kryptering för TCP-port 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Blockera en specifik IP-adress
Du kan begränsa en Azure-webbrollåtkomst till en uppsättning angivna IP-adresser genom att ändra filen IIS **web.config.** Du måste också använda en kommandofil som låser upp **ipSecurity-delen** av **filen ApplicationHost.config.**

Om du vill låsa upp **ipSecurity-delen** i filen **ApplicationHost.config** skapar du en kommandofil som körs vid rollstart. Skapa en mapp på rotnivån i webbrollen som kallas **start** och skapa i den här mappen en kommandofil som heter **startup.cmd**. Lägg till den här filen i Visual Studio-projektet och ange egenskaperna till **Kopiera alltid** för att säkerställa att den ingår i paketet.

Lägg till följande startuppgift i filen [ServiceDefinition.csdef.]

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Lägg till det här kommandot i **startup.cmd-filen:**

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Den här aktiviteten gör att **batchfilen startup.cmd** körs varje gång webbrollen initieras, vilket säkerställer att avsnittet **ipSecurity** som krävs låses upp.

Slutligen ändrar du [avsnittet system.webServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) i webbrollens **webrolls web.config-fil** för att lägga till en lista över IP-adresser som beviljas åtkomst, vilket visas i följande exempel:

Med det här exemplet **kan** alla ÅTKOMST TILL servern utom de två definierade

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Det här exemplet **konfigurerar** alla IPs från att komma åt servern förutom de två definierade.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>Skapa en startuppgift för PowerShell
Det går inte att anropa Windows PowerShell-skript direkt från filen [ServiceDefinition.csdef,] men de kan anropas inifrån en startbatchfil.

PowerShell (som standard) körs inte osignerade skript. Om du inte signerar skriptet måste du konfigurera PowerShell så att det körs osignerade skript. Om du vill köra osignerade skript måste **ExecutionPolicy** anges till **Obegränsad**. Inställningen **ExecutionPolicy** som du använder baseras på versionen av Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Om du använder ett gästoperativsystem som kör PowerShell 2.0 eller 1.0 kan du tvinga version 2 att köras och om det inte är tillgängligt använder du version 1.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Skapa filer i lokal lagring från en startuppgift
Du kan använda en lokal lagringsresurs för att lagra filer som skapats av startuppgiften och som används senare av ditt program.

Om du vill skapa den lokala lagringsresursen lägger du till avsnittet [LocalResources] i filen [ServiceDefinition.csdef] och lägger sedan till det underordnade elementet [LocalStorage.] Ge den lokala lagringsresursen ett unikt namn och en lämplig storlek för startaktiviteten.

Om du vill använda en lokal lagringsresurs i startaktiviteten måste du skapa en miljövariabel för att referera till den lokala lagringsresursplatsen. Sedan kan startaktiviteten och programmet läsa och skriva filer till den lokala lagringsresursen.

De relevanta avsnitten i **filen ServiceDefinition.csdef** visas här:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Som ett exempel använder den här **startup.cmd-kommandofilen** miljövariabeln **PathToStartupStorage** för att skapa filen **MyTest.txt** på den lokala lagringsplatsen.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Du kan komma åt lokal lagringsmapp från Azure SDK med hjälp av metoden [GetLocalResource.](/previous-versions/azure/reference/ee772845(v=azure.100))

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Kör i emulatorn eller molnet
Du kan låta din startuppgift utföra olika steg när den fungerar i molnet jämfört med när den är i beräkningsemulatorn. Du kanske till exempel bara vill använda en ny kopia av dina SQL-data när du kör i emulatorn. Eller så kanske du vill göra några prestandaoptimeringar för molnet som du inte behöver göra när du kör i emulatorn.

Den här möjligheten att utföra olika åtgärder på beräkningemulatorn och molnet kan utföras genom att skapa en miljövariabel i filen [ServiceDefinition.csdef.] Du testar sedan den miljövariabeln för ett värde i startuppgiften.

Om du vill skapa miljövariabeln lägger du till elementet `/RoleEnvironment/Deployment/@emulated` [Variable]/[RoleInstanceValue] och skapar ett XPath-värde för . Värdet för miljövariabeln **%ComputeEmulatorRunning%** körs `true` när den körs `false` på beräkningsemulatorn och när du kör på molnet.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

Aktiviteten kan nu kontrollera miljövariabeln **%ComputeEmulatorRunning%** för att utföra olika åtgärder baserat på om rollen körs i molnet eller emulatorn. Här är ett CMD-skalskript som söker efter den miljövariabeln.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Identifiera att aktiviteten redan har körts
Rollen kan återvinnas utan en omstart som gör att startuppgifterna körs igen. Det finns ingen flagga som anger att en aktivitet redan har körts på den virtuella värddatorn. Du kan ha vissa uppgifter där det inte spelar någon roll att de körs flera gånger. Du kan dock stöta på en situation där du måste förhindra att en aktivitet körs mer än en gång.

Det enklaste sättet att identifiera att en aktivitet redan har körts är att skapa en fil i mappen **%TEMP%** när aktiviteten lyckas och leta efter den i början av aktiviteten. Här är ett prov cmd skal skript som gör det åt dig.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>Metodtips för uppgifter
Här är några metodtips som du bör följa när du konfigurerar uppgiften för din webb- eller arbetsroll.

### <a name="always-log-startup-activities"></a>Logga alltid startaktiviteter
Visual Studio ger inte en felsökare för att gå igenom batchfiler, så det är bra att få så mycket data om driften av batch-filer som möjligt. Logga utdata från batchfiler, både **stdout** och **stderr**, kan ge dig viktig information när du försöker felsöka och åtgärda batchfiler. Om du vill logga både **stdout** och **stderr** till filen StartupLog.txt i katalogen `>>  "%TEMP%\\StartupLog.txt" 2>&1` som pekas ut av miljövariabeln **%TEMP%** lägger du till texten i slutet av specifika rader som du vill logga. Om du till exempel vill köra setup.exe i katalogen **%PathToApp1Install%:**

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Om du vill förenkla xml-koden kan du skapa en *cmd-fil* för omslag som anropar alla startuppgifter tillsammans med loggning och säkerställer att varje underordnad uppgift delar samma miljövariabler.

Du kan finna det `>> "%TEMP%\StartupLog.txt" 2>&1` irriterande men att använda i slutet av varje start uppgift. Du kan framtvinga uppgiftsloggning genom att skapa ett omslag som hanterar loggning åt dig. Det här omslaget anropar den riktiga kommandofilen som du vill köra. Alla utdata från målbatchfilen omdirigeras till filen *Startuplog.txt.*

I följande exempel visas hur du omdirigerar alla utdata från en startbatchfil. I det här exemplet skapar filen ServerDefinition.csdef en startuppgift som anropar *logwrap.cmd*. *logwrap.cmd* *anropar Startup2.cmd*och omdirigerar all utdata till **%TEMP%\\StartupLog.txt**.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap.cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Start2.cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Exempel på utdata i **filen StartupLog.txt:**

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> **Filen StartupLog.txt** finns i mappen *C:\Resources\temp\\{role identifier}\RoleTemp.*
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Ange körningKontext på rätt sätt för startuppgifter
Ange privilegier på lämpligt sätt för startuppgiften. Ibland måste startaktiviteter köras med förhöjda privilegier även om rollen körs med normala privilegier.

[Attributet executionContext][Task] anger behörighetsnivån för startuppgiften. Med `executionContext="limited"` hjälp innebär att startuppgiften har samma behörighetsnivå som rollen. Med `executionContext="elevated"` hjälp av innebär att startuppgiften har administratörsbehörighet, vilket gör att startuppgiften kan utföra administratörsuppgifter utan att ge administratörsbehörighet till din roll.

Ett exempel på en startuppgift som kräver förhöjda privilegier är en startuppgift som använder **AppCmd.exe** för att konfigurera IIS. **AppCmd.exe** `executionContext="elevated"`kräver .

### <a name="use-the-appropriate-tasktype"></a>Använda lämplig taskType
[Attributet taskType][Task] avgör hur startaktiviteten körs. Det finns tre värden: **enkel,** **bakgrund**och **förgrund**. Bakgrunds- och förgrundsaktiviteterna startas asynkront och sedan utförs de enkla aktiviteterna synkront en i taget.

Med **enkla** startuppgifter kan du ange i vilken ordning aktiviteterna körs i den ordning som aktiviteterna visas i filen ServiceDefinition.csdef. Om en **enkel** aktivitet slutar med en slutkod som inte är noll stoppas startproceduren och rollen startar inte.

Skillnaden mellan **startaktiviteter** i bakgrunden och **startaktiviteter för förgrunden** är att **förgrundsaktiviteter** håller rollen igång tills **förgrundsaktiviteten** avslutas. Detta innebär också att om **förgrundsaktiviteten** låser sig eller kraschar, kommer rollen inte att återvinnas förrän **förgrundsaktiviteten** tvingas stängas. Därför rekommenderas **bakgrundsaktiviteter** för asynkrona startaktiviteter om du inte behöver funktionen **i förgrundsaktiviteten.**

### <a name="end-batch-files-with-exit-b-0"></a>Avsluta batchfiler med EXIT /B 0
Rollen startar bara om **felnivån** från var och en av dina enkla startuppgiftar är noll. Inte alla program ställa in **felnivå** (exit kod) korrekt, så `EXIT /B 0` batch-filen bör sluta med en om allt gick korrekt.

En `EXIT /B 0` saknas i slutet av en startbatchfil är en vanlig orsak till roller som inte startar.

> [!NOTE]
> Jag har märkt att kapslade batchfiler `/B` ibland hänger sig när du använder parametern. Du kanske vill vara säker på att det här låser problemet inte uppstår om en annan kommandofil anropar din aktuella kommandofil, som om du använder [loggomslaget](#always-log-startup-activities). Du kan utelämna `/B` parametern i det här fallet.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Förvänta dig att startaktiviteter ska köras mer än en gång
Alla rollåtervinningar inkluderar inte en omstart, men alla rollåtervinning omfattar att köra alla startuppgifter. Detta innebär att startaktiviteter måste kunna köras flera gånger mellan omstarter utan problem. Detta beskrivs i [föregående avsnitt](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Använd lokal lagring för att lagra filer som måste nås i rollen
Om du vill kopiera eller skapa en fil under startuppgiften som sedan är tillgänglig för din roll måste filen placeras i lokalt lagringsutrymme. Se [föregående avsnitt](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Nästa steg
Granska [molntjänstmodellen och -paketet](cloud-services-model-and-package.md)

Läs mer om hur [uppgifter](cloud-services-startup-tasks.md) fungerar.

[Skapa och distribuera](cloud-services-how-to-create-deploy-portal.md) ditt molntjänstpaket.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Aktivitet]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Miljö]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variabel]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RollInstanceVärde]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Slutpunkter]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[Localstorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[Lokala resurser]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RollInstanceVärde]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue



