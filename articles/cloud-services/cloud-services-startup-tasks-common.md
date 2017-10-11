---
title: "Vanliga uppgifter för start för molntjänster | Microsoft Docs"
description: "Ger exempel på vanliga Start uppgifter som du kanske vill utföra i cloud services webbroll eller worker-rollen."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: a7095dad-1ee7-4141-bc6a-ef19a6e570f1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: cee23da5b089b02bfc0ef10afd60f0f2272585b1
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="common-cloud-service-startup-tasks"></a>Vanliga uppgifter för start av Molntjänsten
Den här artikeln innehåller några exempel på vanliga Start uppgifter som du kanske vill utföra i Molntjänsten. Du kan använda Start uppgifter för att utföra åtgärder innan du startar en roll. Åtgärder som du kanske vill utföra inkluderar installerar en komponent, registrerar COM-komponenter, ange registernycklar eller starta en tidskrävande process. 

Se [i den här artikeln](cloud-services-startup-tasks.md) att förstå hur start aktiviteter fungerar och specifikt så här skapar du poster som definierar en startaktivitet.

> [!NOTE]
> Start-aktiviteter kan inte användas för virtuella datorer, endast för Cloud Service webb- och arbetsroller.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definiera miljövariabler innan du startar en roll
Om du behöver miljövariabler som definieras för en viss aktivitet kan använda den [miljö] -element inuti den [aktivitet] element.

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

Variabler kan också använda en [giltig Azure XPath-värdet](cloud-services-role-config-xpath.md) att referera till något om distributionen. Istället för att använda den `value` attribut, definiera en [RoleInstanceValue] underordnat element.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Konfigurera IIS-start med AppCmd.exe
Den [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) kommandoradsverktyget kan användas för att hantera IIS-inställningarna vid start på Azure. *AppCmd.exe* ger praktiska, kommandoradsverktyg åtkomst till konfigurationsinställningar för start uppgifter på Azure. Med hjälp av *AppCmd.exe*, Webbplatsinställningar kan läggas till, ändras eller tas bort för program och platser.

Det finns dock några saker att se upp för användning av *AppCmd.exe* som en startåtgärd för:

* Start-uppgifter kan köras mer än en gång mellan olika omstarter. Till exempel när en roll återanvänds.
* Om en *AppCmd.exe* åtgärd utförs mer än en gång, kan ett fel genereras. Till exempel försöker lägga till ett avsnitt till *Web.config* två gånger kan genererar ett fel.
* Start misslyckas om de returnerar slutkoden noll eller **errorlevel**. Till exempel när *AppCmd.exe* genererar ett fel.

Är det en bra idé att kontrollera den **errorlevel** efter att *AppCmd.exe*, som är lätt att göra om du omsluter anropet till *AppCmd.exe* med en *.cmd* fil. Om du identifierar en känd **errorlevel** svar, du kan ignorera det eller skicka tillbaka.

Errorlevel som returneras av *AppCmd.exe* listas i filen winerror.h och kan också visas på [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### <a name="example-of-managing-the-error-level"></a>Exempel för att hantera Felnivån
Det här exemplet lägger till ett avsnitt med komprimering och en post för komprimering för JSON till det *Web.config* fil med felhantering och loggning.

De relevanta avsnitten i den [ServiceDefinition.csdef] filen visas de här, som innehåller inställningen av [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) attributet `elevated` att ge *AppCmd.exe* behörighet att ändra inställningarna i den *Web.config* fil:

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

Den *Startup.cmd* batch-filen använder *AppCmd.exe* att lägga till ett avsnitt för komprimering och en post för komprimering för JSON till det *Web.config* fil. Den förväntade **errorlevel** av 183 har angetts till noll med hjälp av kontrollera. Exe för kommandoraden. Oväntat errorlevels loggas StartupErrorLog.txt.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL

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
I Azure finns effektivt två brandväggar. Första brandväggen styr anslutningar mellan den virtuella datorn och omvärlden. Den här brandväggen styrs av den [slutpunkter] element i den [ServiceDefinition.csdef] fil.

Andra brandväggen styr anslutningar mellan den virtuella datorn och processer i den virtuella datorn. Den här brandväggen kan styras av den `netsh advfirewall firewall` kommandoradsverktyget.

Azure skapar brandväggsregler för processer som startas inom dina roller. Till exempel när du startar en tjänst eller ett program, skapar Azure automatiskt de nödvändiga brandväggsreglerna som tillåter att tjänsten ska kunna kommunicera med Internet. Om du skapar en tjänst som startas av en process utanför din roll (till exempel en COM +-tjänst eller en schemalagd aktivitet i Windows), måste du dock manuellt skapa en brandväggsregel för att tillåta åtkomst till den tjänsten används. Brandväggsreglerna kan skapas med hjälp av en startaktivitet.

En startåtgärd som skapar en brandväggsregel måste ha en [executionContext][aktivitet] av **utökade**. Lägg till följande startåtgärd för att den [ServiceDefinition.csdef] fil.

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

Om du vill lägga till brandväggsregeln, måste du använda rätt `netsh advfirewall firewall` kommandon i kommandofilen startades. I det här exemplet kräver startaktivitet säkerhet och kryptering för TCP-port 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Blockera en specifik IP-adress
Du kan begränsa en roll för Azure Webbåtkomst till en uppsättning angivna IP-adresser genom att ändra din IIS **web.config** fil. Du måste också använda en kommandofil som låser upp det **ipSecurity** avsnitt i den **ApplicationHost.config** fil.

Att låsa upp den **ipSecurity** avsnitt i den **ApplicationHost.config** fil, skapa en kommandofil som körs vid start av rollen. Skapa en mapp på rotnivå web rollen kallas **Start** och skapa en batchfil som anropas i den här mappen **startup.cmd**. Lägg till den här filen i Visual Studio-projekt och att ange egenskaperna **Kopiera alltid** så att den ingår i paketet.

Lägg till följande startåtgärd för att den [ServiceDefinition.csdef] fil.

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

Lägg till det här kommandot till det **startup.cmd** fil:

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Uppgiften gör den **startup.cmd** batchfil som ska köras varje gång webbrollen initieras att säkerställa att de nödvändiga **ipSecurity** avsnitt är olåst.

Slutligen ändrar den [system.webServer avsnittet](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) din webbroll **web.config** fil att lägga till en lista över IP-adresser som beviljas åtkomst till, enligt följande exempel:

Det här exemplet config **kan** alla IP-adresser till servern, förutom de två definierats

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

Det här exemplet config **nekar** alla IP-adresser från att komma åt servern utom de två definierats.

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

## <a name="create-a-powershell-startup-task"></a>Skapa en startåtgärd för PowerShell
Windows PowerShell-skript kan inte anropas direkt från den [ServiceDefinition.csdef] -fil, men de kan anropas från inom en Startkommandofil.

Osignerade skript körs inte i PowerShell (som standard). Om du inte loggar skriptet, måste du konfigurera PowerShell för att köra osignerade skript. Att köra osignerade skript i **ExecutionPolicy** måste anges till **obegränsat**. Den **ExecutionPolicy** inställning som du används baserat på version av Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Om du använder ett Gästoperativsystem som kör PowerShell 2.0 eller 1.0 kan du tvinga version 2 för att köra, och om det är inte tillgänglig, kan du använda version 1.

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

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Skapa filer i lokal lagring från en startåtgärd
Du kan använda en lokal lagringsresurs för att lagra filer som skapas av din startaktivitet som senare används av ditt program.

När du skapar resursen lokal lagring till en [LocalResources] avsnittet till den [ServiceDefinition.csdef] filen och Lägg sedan till den [LocalStorage] underordnat element. Ge resursen lokal lagring ett unikt namn och en lämplig storlek för din startaktivitet.

Om du vill använda en lokal lagring-resurs i din startaktivitet, måste du skapa en miljövariabel för att referera till den lokala resurs lagringsplatsen. Sedan kan aktiviteten startades och programmet läsa och skriva filer till resursen i lokal lagring.

De relevanta avsnitten i den **ServiceDefinition.csdef** filen visas här:

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

Exempelvis detta **Startup.cmd** batch-filen använder den **PathToStartupStorage** miljövariabeln för att skapa filen **MyTest.txt** på lokala lagringsplatsen.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Du kan komma åt mappen för lokal lagring från Azure SDK med hjälp av den [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) metod.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Kör i emulatorn eller molnet
Du kan ha din startaktivitet som utför olika steg när den körs i molnet jämfört med när den är i beräkningsemulatorn. Exempelvis kanske du vill använda en ny kopia av SQL-data endast när den körs i emulatorn. Eller så kanske du vill göra prestandaoptimeringar för det moln som du inte behöver göra när du kör i emulatorn.

Den här möjligheten att utföra olika åtgärder på beräkningsemulatorn och molnet kan åstadkommas genom att skapa en miljövariabel i den [ServiceDefinition.csdef] fil. Testa att miljövariabeln för ett värde i din startaktivitet.

När du skapar miljövariabeln till den [variabeln]/[RoleInstanceValue] element och skapa en XPath-värdet för `/RoleEnvironment/Deployment/@emulated`. Värdet för den **ComputeEmulatorRunning %** miljövariabel är `true` vid körning på beräkningsemulatorn, och `false` när du kör i molnet.

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

Uppgiften kan nu kontrollera den **ComputeEmulatorRunning %** miljövariabeln för att utföra olika åtgärder baserat på om rollen körs i molnet eller emulatorn. Här är ett cmd shell-skript som söker efter att miljövariabeln.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Identifiera uppgiften har körts
Rollen kan återvinna utan en omstart orsakar Start aktiviteterna körs igen. Det finns ingen flagga för att indikera att en aktivitet har redan körts på den värd virtuella datorn. Du kan ha vissa uppgifter där det spelar ingen roll de köras flera gånger. Du kan dock köra i en situation där du behöver förhindra att en aktivitet körs mer än en gång.

Det enklaste sättet att identifiera en aktivitet har körts är att skapa en fil i den **% TEMP %** mapp när aktiviteten lyckas och leta efter den i början av uppgiften. Här är ett exempel cmd shell-skript som gör som du.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%RoleRoot%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"

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

## <a name="task-best-practices"></a>Metodtips för aktiviteten
Här följer några rekommendationer som du bör följa när du konfigurerar för din webbplats eller arbetsprocess roll.

### <a name="always-log-startup-activities"></a>Alltid logga Start aktiviteter
Visual Studio tillhandahåller inte en felsökare för att gå igenom batch-filer, så det är bra för att få så mycket data för driften av batch-filer som möjligt. Loggning av utdata från batch-filer, både **stdout** och **stderr**, kan ge dig viktig information vid försök att felsöka och lösa batch-filer. Logga både **stdout** och **stderr** till StartupLog.txt filen i katalogen som pekar på den **% TEMP %** miljövariabeln, lägga till texten `>>  "%TEMP%\\StartupLog.txt" 2>&1` till slutet av specifika rader som du vill logga. Till exempel för att köra setup.exe i den **% PathToApp1Install** directory:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

För att förenkla XML-data, kan du skapa en wrapper *cmd* -fil som anropar alla programstart uppgifter tillsammans med loggning och säkerställer att varje underordnad aktivitet delar samma miljövariabler.

Det kan vara irriterande om för att använda `>> "%TEMP%\StartupLog.txt" 2>&1` i slutet av varje startaktivitet. Du kan tillämpa uppgiften loggning genom att skapa en wrapper som hanterar loggning för dig. Den här wrapper anropar batchfilen verkliga som du vill köra. Alla utdata från batch målfilen omdirigeras till den *Startuplog.txt* fil.

I följande exempel visas hur du dirigerar alla utdata från en Startkommandofil. I det här exemplet skapar filen ServerDefinition.csdef en startåtgärd som anropar *logwrap.cmd*. *logwrap.cmd* anrop *Startup2.cmd*, omdirigering av utdata till **% TEMP %\\StartupLog.txt**.

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

**Startup2.cmd:**

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

Exempel på utdata i den **StartupLog.txt** fil:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> Den **StartupLog.txt** filen finns i den *C:\Resources\temp\\{rollen identifieraren} \RoleTemp* mapp.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Ange executionContext korrekt för start uppgifter
Ange behörigheter för aktiviteten startades. Ibland måste start aktiviteter köras med utökade privilegier trots att rollen körs med normal behörighet.

Den [executionContext][aktivitet] attribut anger behörighetsnivå för aktiviteten startades. Med hjälp av `executionContext="limited"` innebär startaktivitet har samma behörighetsnivå som rollen. Med hjälp av `executionContext="elevated"` innebär startaktivitet har administratörsbehörighet, vilket gör att startåtgärd för att utföra administratörsåtgärder utan att bevilja administratörsbehörighet för din roll.

Ett exempel på en startuppgift som kräver utökade privilegier är en startåtgärd som använder **AppCmd.exe** att konfigurera IIS. **AppCmd.exe** kräver `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Använd lämplig taskType
Den [taskType][aktivitet] attributet avgör hur startaktivitet körs. Det finns tre värden: **enkel**, **bakgrund**, och **förgrunden**. Har startats asynkront i förgrunden och bakgrunden uppgifter och sedan enkla uppgifter köras synkront en i taget.

Med **enkel** Start uppgifter som du kan ange i vilken ordning som uppgifterna köras i den ordning som uppgifterna som anges i filen ServiceDefinition.csdef. Om en **enkel** aktivitet avslutas med slutkoden noll och sedan starten avbryts och rollen startar inte.

Skillnaden mellan **bakgrund** Start uppgifter och **förgrunden** Start uppgifter är att **förgrunden** uppgifter hålla rollen körs förrän den **förgrunden** slutar. Detta betyder också att om den **förgrunden** aktivitet låser sig eller kraschar, rollen kommer inte att återanvändas förrän den **förgrunden** aktivitet tvingas stängd. Därför **bakgrund** uppgifter rekommenderas för asynkron Start uppgifter om du inte behöver den funktionen för den **förgrunden** aktivitet.

### <a name="end-batch-files-with-exit-b-0"></a>End batch-filer med avsluta /B 0
Rollen börjar endast om den **errorlevel** från var och en av dina enkla Start uppgift är noll. Inte alla program ange den **errorlevel** (slutkod) korrekt så kommandofilen ska avslutas med ett `EXIT /B 0` om allt körde korrekt.

Det saknas `EXIT /B 0` i slutet av en start-batch-filen är en vanlig orsak till roller som inte startar.

> [!NOTE]
> Jag har lagt märke till den kapslade batchen filer ibland låser sig när du använder den `/B` parameter. Du kanske vill kontrollera att det här låser sig problemet inte sker om en annan kommandofil anropar den aktuella batchfilen, som om du använder den [loggen wrapper](#always-log-startup-activities). Du kan hoppa över den `/B` parameter i det här fallet.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Förvänta dig Start aktiviteter att köra mer än en gång
Inte alla rollen återanvänder inkludera en omstart, men alla rollen återanvänder innehålla alla Start-aktiviteter som körs. Det innebär att start-aktiviteter måste kunna köras flera gånger mellan olika omstarter utan problem. Det beskrivs i den [föregående avsnitt](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Använd lokal lagring för att lagra filer som måste kunna nås i rollen
Om du vill kopiera eller skapa en fil under din startaktivitet är sedan tillgängliga för din roll måste filen placeras i lokal lagring. Finns det [föregående avsnitt](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Nästa steg
Granska molnet [service model och paket](cloud-services-model-and-package.md)

Läs mer om hur [uppgifter](cloud-services-startup-tasks.md) fungerar.

[Skapa och distribuera](cloud-services-how-to-create-deploy-portal.md) ditt moln-abonnemang.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[aktivitet]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[miljö]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[variabeln]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Slutpunkter]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
