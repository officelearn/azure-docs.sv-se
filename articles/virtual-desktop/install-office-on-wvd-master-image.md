---
title: Installera Office på en virtuell Hårddisk huvudavbildning - Azure
description: Så här att installera och anpassa Office på en virtuell Windows-skrivbordet förhandsversion huvudavbildning till Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: 0e89d37011ccdfc3acdace5b45faa8e9a64e4d3e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620471"
---
# <a name="install-office-on-a-master-vhd-image"></a>Installera Office på en VHD-huvudavbildning

Den här artikeln visar hur du installerar Office 365 ProPlus, OneDrive och andra vanliga program på en master virtuell hårddisk (VHD)-avbildning för överföring till Azure. Om användarna behöver för att få åtkomst till vissa verksamhetsspecifika (LOB) program, rekommenderar vi du installera dem när du har slutfört instruktionerna i den här artikeln.

Den här artikeln förutsätter att du redan har skapat en virtuell dator (VM). Om den inte finns i [Förbered och anpassa en master VHD-avbildning](set-up-customize-master-image.md#create-a-vm)

Den här artikeln förutsätts även att du har haft utökade åtkomst på den virtuella datorn om den har etablerats i Azure eller Hyper-V Manager. Om den inte finns i [utöka behörighet för att hantera alla Azure-prenumeration och hantering av grupper](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Dessa instruktioner är för en virtuell Windows-skrivbordet förhandsversion konfiguration som kan användas med din organisations befintliga processer.

## <a name="install-office-in-shared-computer-activation-mode"></a>Installera Office i läget för aktivering av delade datorer

Aktivering av delade datorer kan du distribuera Office 365 ProPlus till en dator i din organisation som används av flera användare. Mer information om aktivering av delade datorer finns i [översikt över delad aktivering för Office 365 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Använd den [distributionsverktyget för Office](https://www.microsoft.com/download/details.aspx?id=49117) att installera Office. Windows 10 Enterprise flera session stöder endast följande versioner av Office:
- Office 365 ProPlus
- Office 365 Business som levereras med en prenumeration för Microsoft 365 Business

Distributionsverktyget för Office kräver en XML-konfigurationsfilen. Om du vill anpassa följande exempel finns i den [konfigurationsalternativ för distributionsverktyget för Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Den här exempelkonfigurationen XML som vi tillhandahåller kommer att göra följande:

- Installera Office från Insiders kanalen och leverera uppdateringar från Insiders kanalen när de är körs.
- Använd x64 arkitektur.
- Inaktivera automatiska uppdateringar.
- Installera Visio och projekt.
- Ta bort alla befintliga installationer av Office och migrera deras inställningar.
- Aktivera delad aktivering.

>[!NOTE]
>Stencil sökfunktionen i Visio fungerar inte i virtuella Windows-skrivbordet under förhandsversionen konfigurationen.

Här är vad den här exempelkonfigurationen XML inte göra:

- Installera Skype för företag
- Installera OneDrive i läget per användare. Mer information finns i [installera OneDrive i per dator läge](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Delad aktivering kan ställas in via grupprincipobjekt (GPO) eller registerinställningar. Grupprincipobjektet finns på **Datorkonfiguration\\principer\\Administrationsmallar\\Microsoft Office 2016 (dator)\\för licensiering**

Distributionsverktyget för Office innehåller setup.exe. För att installera Office, kör du följande kommando i Kommandotolken:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Exempel på configuration.xml

I följande XML-exempel installerar Insiders-versionen.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Office-teamet rekommenderar att du använder 64-bitars installation för den **OfficeClientEdition** parametern.

När du har installerat Office, kan du uppdatera Office standardbeteendet. Kör följande kommandon individuellt eller i en batchfil att uppdatera beteendet.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>Installera OneDrive i per dator-läge

OneDrive är normalt installerat per användare. I den här miljön bör det vara installerad per dator.

Här är hur du installerar OneDrive i per dator-läge:

1. Skapa först en plats för att mellanlagra OneDrive-installationsprogrammet. En lokal disk-mapp eller [\\\\unc] (file://unc) plats är bra.

2. Hämta OneDriveSetup.exe att den mellanlagrade platsen med den här länken: <https://aka.ms/OneDriveWVD-Installer>

3. Om du har installerat office med OneDrive genom att utelämna  **\<ExcludeApp ID = ”OneDrive” /\>** , avinstallera alla befintliga OneDrive användarspecifika installationer från en upphöjd kommandotolk genom att köra följande kommandot:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Kör det här kommandot från en upphöjd kommandotolk för att ange den **AllUsersInstall** registervärdet:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Kör detta kommando för att installera OneDrive i per dator-läge:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Kör detta kommando för att konfigurera OneDrive för att starta vid inloggning för alla användare:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Aktivera **tyst konfigurera användarkonto** genom att köra följande kommando.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Omdirigera och flyttar Windows kända mappar till OneDrive genom att köra följande kommando.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Teams och Skype

Virtuella Windows-skrivbordet stöder inte Skype för företag och team.

## <a name="next-steps"></a>Nästa steg

Nu när du har lagt till Office på avbildningen, kan du fortsätta att anpassa din master VHD-avbildning. Se [Förbered och anpassa en huvudavbildning VHD](set-up-customize-master-image.md).
