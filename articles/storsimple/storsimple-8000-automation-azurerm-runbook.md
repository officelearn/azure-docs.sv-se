---
title: Använd Azure Automation-Runbook för att hantera StorSimple-enheter | Microsoft Docs
description: Lär dig hur du använder Azure Automation-Runbook för att automatisera StorSimple-jobb
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: cfd0e4dbb6a4f24df5ba42cd45f9c16fbe5b493c
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
ms.locfileid: "23934833"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Använd Azure Automation-runbooks för att hantera StorSimple-enheter

Den här artikeln beskriver hur du använder Azure Automation runbook att hantera enheten StorSimple 8000-serien i Azure-portalen. En exempel-runbook ingår för att vägleder dig genom stegen för att konfigurera din miljö för att köra denna runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurera, lägga till och köra Azure runbook

Det här avsnittet tar ett exempel Windows PowerShell-skript för StorSimple och beskrivs de olika stegen som krävs för att importera skriptet till en runbook och sedan publicera och köra runbook.

### <a name="prerequisites"></a>Krav

Innan du börjar bör du kontrollera att du har:

* en aktiv Azure-prenumeration som är associerade med din StorSimple Device Manager-tjänst har registrerats med en enhet för StorSimple 8000-serien.


* Windows PowerShell 5.0 installerat på datorn (eller Windows Server som värd för din StorSimple om du använder en).


### <a name="create-automation-runbook-module-in-windows-powershell"></a>Skapa automation runbook-modulen i Windows PowerShell

Utför följande steg för att skapa en automation-modul för StorSimple 8000-serien enhetshantering:

1. Starta Windows PowerShell. Skapa en ny mapp och ändra katalogen till den nya mappen.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Hämta NuGet CLI](http://www.nuget.org/downloads) under mappen skapade i föregående steg. Det finns olika versioner av _nuget.exe_. Välj den version som motsvarar din SDK. Varje hämtningslänken pekar direkt till en _.exe_ fil. Se till att högerklicka på och spara filen på datorn i stället för körs från webbläsaren.

    Du kan också köra följande kommando för att hämta och spara skriptet i samma mapp som du skapade tidigare.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Hämta beroende SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Ladda ned skriptet från GitHub exempelprojektet.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1

    ```
5. Skapa en Azure Automation Runbook-modulen för hantering av enheter StorSimple 8000-serien. Skriv följande kommandon i Windows Powershell-fönstret:

    ```
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"
            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip

    ```

6. Kontrollera att ett automation-modul zip-filen skapas i `C:\scripts\StorSimpleSDKTools`.

    ![Kontrollera-automation-modul](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Följande utdata visas när automation-modul har skapats via Windows PowerShell. 

    ```
    -----------------------------------------
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\WINDOWS\system32> mkdir C:\scripts\StorSimpleSDKTools

        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools


    PS C:\WINDOWS\system32> cd c:\scripts\StorSimpleSDKTools
    PS C:\scripts\StorSimpleSDKTools> wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorS
    impleSDKTools\nuget.exe
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8
    000series

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.Active
    Directory -Version 2.28.3

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Aut
    hentication -Version 2.2.9-preview

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTool
    s
    Executing nuget actions took 717.48 ms
    PS C:\scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Mo
    nitor-Backups.ps1 -Out Monitor-Backups.ps1
    PS C:\scripts\StorSimpleSDKTools> # set path variables
    PS C:\scripts\StorSimpleSDKTools>             $downloadDir = "C:\scripts\StorSimpleSDKTools"
    PS C:\scripts\StorSimpleSDKTools>             $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.Sto
    rSimple8000Series"
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Se
    ries"
    PS C:\scripts\StorSimpleSDKTools>             mkdir "$moduleDir"

        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule


    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series

    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\
    lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Mic
    rosoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft
    .Rest.ClientRuntime*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $
    moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9
    -preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\l
    ib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #Don't change the name of the Archive
    PS C:\scripts\StorSimpleSDKTools>             compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Manag
    ement.StorSimple8000Series.zip
    PS C:\scripts\StorSimpleSDKTools>

    -------------------------------------------

    ```

### <a name="import-publish-and-run-automation-runbook"></a>Importera, publicera och köra Automation-runbook

1. Skapa ett kör som i Azure automation-konto i Azure-portalen. Det gör du genom att gå till **Azure marketplace > allt** och sök sedan efter **Automation**. Välj **Automation-konton**.

    ![Sök-automatisering](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. I den **lägga till Automation-konto** bladet:

    1. Ange den **namn** för Automation-konto.
    2. Välj den **prenumeration** som är kopplade till din StorSimple Enhetshanteraren.
    3. Skapa en ny resursgrupp eller välj en befintlig resursgrupp.
    4. Välj en **plats** (om möjligt samma som där din tjänst körs).
    5. Låt standardvärdet **skapa kör som-konto** alternativ som valts.
    5. Du kan också kontrollera **fäst på instrumentpanelen**. Klicka på **Skapa**.

        ![Skapa-automation-konto](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

    När automation-kontot har skapats visas ett meddelande. Mer information om hur du skapar ett Automation-konto går du till [skapa ett kör som-konto](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. För att säkerställa att skapa automation-kontot kan komma åt StorSimple enheten Manager-tjänsten, måste du tilldela behörighet till automation-kontot. Gå till **åtkomstkontroll** i Enhetshanteraren för StorSimple-tjänsten. Klicka på **+ Lägg till** och ange namnet på ditt Azure Automation-konto. **Spara** inställningarna.

    ![Lägg till-behörigheter-automation-konto](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. I det nya kontot, går du till **delade resurser > moduler** och på **+ Lägg till modulen**.

5. I den **Lägg till modulen** bladet, bläddra till platsen där komprimerade modulen, och markera och öppna modulen. Klicka på **OK**.

    ![Lägg till modul](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Gå till **Processautomatisering > Runbooks och klicka på + Lägg till en runbook**. I den **lägga till runbook** bladet, klickar du på **importera en befintlig runbook**. Peka på Windows PowerShell-skriptfil för den **Runbook-filen**. Runbook-typen väljs automatiskt. Ange ett namn och en valfri beskrivning för runbook. Klicka på **Skapa**.

    ![Lägg till modul](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Runbook har lagts till i listan över runbooks. Välj och klicka på denna runbook.

    ![Klicka på ny runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Redigera runbook och klicka på **Test fönstret**. Ange parametrar, till exempel namnet på din StorSimple-enheten Manager-tjänsten, namnet på StorSimple-enheten och prenumerationen. **Starta** testet. Rapporten genereras när körningen har slutförts. Mer information finns på [hur du testar en runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![test-runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Granska utdata från runbooken i rutan. Stäng fönstret om uppfyllt. Klicka på **publicera** och när du uppmanas att bekräfta bekräfta och publicera en runbook.

    ![Publicera runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)


## <a name="next-steps"></a>Nästa steg

[Använd Enhetshanteraren för StorSimple-tjänsten för att hantera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).