---
title: Använda Azure Automation Runbook för att hantera StorSimple-enheter
description: Lär dig hur du använder Azure Automation Runbook för att automatisera StorSimple-jobb
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: 727bebe0c190ed4dff4408884c45fe166ad541a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276962"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Använda Azure Automation-runbooks för att hantera StorSimple-enheter

I den här artikeln beskrivs hur Azure Automation-runbooks används för att hantera din StorSimple 8000-serieenhet i Azure-portalen. En exempelkörningsbok ingår för att gå igenom stegen för att konfigurera din miljö för att köra den här runbooken.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurera, lägga till och köra Azure-runbook

Det här avsnittet tar ett exempel på Windows PowerShell-skript för StorSimple och beskriver de olika steg som krävs för att importera skriptet till en runbook och sedan publicera och köra runbooken.

### <a name="prerequisites"></a>Krav

Innan du börjar, se till att du har:

* en aktiv Azure-prenumeration som är associerad med din StorSimple Device Manager-tjänst som registrerats med en StorSimple 8000-serieenhet.

* Windows PowerShell 5.0 installerat på datorn (Eller, din Windows Server värd för din StorSimple om du använder en).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Skapa automationskörningsmodul i Windows PowerShell

Så här skapar du en automatiseringsmodul för enhetshantering i StorSimple 8000:

1. Starta Windows PowerShell. Skapa en ny mapp och ändra katalogen till den nya mappen.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Ladda ner NuGet CLI](https://www.nuget.org/downloads) under mappen som skapades i föregående steg. Det finns olika versioner av _nuget.exe_. Välj den version som motsvarar din SDK. Varje nedladdningslänk pekar direkt på en _EXE-fil._ Var noga med att högerklicka och spara filen på datorn i stället för att köra den från webbläsaren.

    Du kan också köra följande kommando för att hämta och lagra skriptet i samma mapp som du skapade tidigare.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Ladda ner den beroende SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Hämta skriptet från GitHub-projektet.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Skapa en Azure Automation Runbook-modul för enhetshantering i StorSimple 8000-serien. Skriv följande kommandon i Powershell-fönstret i Powershell-fönstret:

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. Kontrollera att en zip-fil `C:\scripts\StorSimpleSDKTools`för en automationsmodul har skapats i .

    ![verifiera-automation-modul](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Följande utdata visas när automatiseringsmodulen skapas via Windows PowerShell.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Importera, publicera och köra Automation-runbook

1. Skapa ett Azure Run As-automatiseringskonto i Azure-portalen. Det gör du genom att gå till **Azure marketplace > Everything** och sedan söka efter **Automation**. Välj **Automation-konton**.

    ![sökautomatisering](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. I bladet **Lägg till automationskonto:**

   1. Ange **namnet** på ditt Automation-konto.
   2. Välj den **prenumeration** som är länkad till tjänsten StorSimple Device Manager.
   3. Skapa en ny resursgrupp eller välj från en befintlig resursgrupp.
   4. Välj en **plats** (om möjligt samma som där tjänsten körs).
   5. Låt standardalternativet **Skapa kör som** konto markerat.
   6. Markera även **fäst på instrumentpanelen**. Klicka på **Skapa**.

       ![skapa-automation-konto](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      När automatiseringskontot har skapats meddelas du. Mer information om hur du skapar ett Automation-konto finns i [Skapa ett Kör som-konto](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. För att säkerställa att det skapade automatiseringskontot kan komma åt Tjänsten StorSimple Device Manager måste du tilldela lämpliga behörigheter till automationskontot. Gå till **åtkomstkontrollen** i tjänsten StorSimple Device Manager. Klicka på **+ Lägg till** och ange namnet på ditt Azure Automation-konto. **Spara** inställningarna.

    ![add-permissions-automation-konto](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Gå till Delade resurser **> moduler i** det nyligen skapade kontot och klicka på **+ Lägg till modul**.

5. I **Bladet Lägg till modul** bläddrar du till platsen för den zippade modulen och väljer och öppnar modulen. Klicka på **OK**.

    ![tilläggsmodul](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Gå till **ProcessAutomatisering > Runbooks och klicka på + Lägg till en runbook**. Klicka på Importera en **befintlig runbook**i bladet **Lägg till runbook.** Peka på windows PowerShell-skriptfilen för **Runbook-filen**. Runbook-typen väljs automatiskt. Ange ett namn och en valfri beskrivning för runbooken. Klicka på **Skapa**.

    ![tilläggsmodul](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Runbooken läggs till i listan över runbooks. Markera och klicka på den här runbooken.

    ![klicka på ny runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Redigera runbooken och klicka på **Testfönstret**. Ange parametrar som namnet på Tjänsten StorSimple Device Manager, namnet på StorSimple-enheten och prenumerationen. **Starta** testet. Rapporten genereras när körningen är klar. Mer information finns i [hur du testar en runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![testkörningsbok](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Kontrollera utdata från runbooken i testfönstret. Om du är nöjd stänger du fönstret. Klicka på **Publicera** och bekräfta och publicera runbooken när du uppmanas att bekräfta.

    ![publicera-runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Device Manager-tjänsten för att hantera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).
