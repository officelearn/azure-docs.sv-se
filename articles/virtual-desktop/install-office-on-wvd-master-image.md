---
title: Installera Office på en huvud hård disk avbildning – Azure
description: Så här installerar och anpassar du Office på en Windows Virtual Desktop Preview Master-avbildning till Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: 9243b3e6ae9e164930373960757109ae8a4512e4
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501196"
---
# <a name="install-office-on-a-master-vhd-image"></a>Installera Office på en VHD-huvudavbildning

Den här artikeln beskriver hur du installerar Office 365 ProPlus, OneDrive och andra vanliga program på en avbildning av en virtuell hård disk (VHD) för uppladdning till Azure. Om dina användare behöver åtkomst till vissa LOB-program (Line of Business) rekommenderar vi att du installerar dem när du har slutfört instruktionerna i den här artikeln.

Den här artikeln förutsätter att du redan har skapat en virtuell dator (VM). Om inte, se [förbereda och anpassa en huvud-VHD-avbildning](set-up-customize-master-image.md#create-a-vm)

Den här artikeln förutsätter också att du har förhöjd åtkomst på den virtuella datorn, oavsett om den är etablerad i Azure eller Hyper-V Manager. Om inte, se [öka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Dessa anvisningar gäller för en för hands version av en Windows Virtual Desktop-konfiguration som kan användas med din organisations befintliga processer.

## <a name="install-office-in-shared-computer-activation-mode"></a>Installera Office i läget för delad dator aktivering

Med den delade dator aktiveringen kan du distribuera Office 365 ProPlus till en dator i organisationen som används av flera användare. Mer information om aktivering av delade datorer finns i [Översikt över delad dator aktivering för Office 365 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Använd [distributions verktyget för Office](https://www.microsoft.com/download/details.aspx?id=49117) för att installera Office. Windows 10 Enterprise multi-session stöder endast följande versioner av Office:
- Office 365 ProPlus
- Office 365-verksamhet som medföljer en Microsoft 365 Business prenumeration

Distributions verktyget för Office kräver en konfigurations-XML-fil. Information om hur du anpassar följande exempel finns i [konfigurations alternativen för distributions verktyget för Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Den här exempel konfigurations-XML: en som vi har angett kommer att göra följande:

- Installera Office från Insiders-kanalen och leverera uppdateringar från insides-kanalen när de körs.
- Använd x64-arkitekturen.
- Inaktivera automatiska uppdateringar.
- Installera Visio och Project.
- Ta bort alla befintliga installationer av Office och migrera sina inställningar.
- Aktivera aktivering av delad dator.

>[!NOTE]
>Sök funktionen för stencil i Visio fungerar inte i Windows Virtual Desktop under för hands versions konfigurationen.

Det här är den här exempel konfigurations-XML: en:

- Installera Skype för företag
- Installera OneDrive i per användare-läge. Läs mer i [Installera OneDrive i per dator läge](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Aktivering av delade datorer kan konfigureras via grupprincip objekt (GPO) eller register inställningar. Grupprincipobjektet finns på **dator konfigurations\\principer\\administrativa mallar\\Microsoft Office 2016 (dator)\\licens inställningar**

Distributions verktyget för Office innehåller setup. exe. Installera Office genom att köra följande kommando på en kommando rad:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Exempel på Configuration. XML

Följande XML-exempel kommer att installera Insiders-versionen.

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
>Office-teamet rekommenderar att du använder 64-bitars installation för parametern **OfficeClientEdition** .

När du har installerat Office kan du uppdatera standard beteendet för Office. Kör följande kommandon individuellt eller i en kommando fil för att uppdatera beteendet.

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

OneDrive installeras vanligt vis per användare. I den här miljön bör den installeras per dator.

Så här installerar du OneDrive i per dator läge:

1. Börja med att skapa en plats för att mellanlagra OneDrive-installationsprogrammet. En lokal disk-mapp eller\\[\\UNC]-plats (File://UNC) är felfri.

2. Hämta OneDriveSetup. exe till den mellanlagrade platsen med den här länken:<https://aka.ms/OneDriveWVD-Installer>

3. Om du har installerat Office med OneDrive genom att utesluta  **\<ExcludeApp ID = "OneDrive\>"/** avinstallerar du alla befintliga OneDrive-användarspecifika installationer från en upphöjd kommando tolk genom att köra följande kommando:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Kör det här kommandot från en upphöjd kommando tolk för att ange registervärdet **AllUsersInstall** :

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Kör det här kommandot för att installera OneDrive i per dator-läge:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Kör det här kommandot för att konfigurera OneDrive för att starta vid inloggning för alla användare:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Aktivera **tyst konfiguration av användar konto** genom att köra följande kommando.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Omdirigera och flytta Windows-kända mappar till OneDrive genom att köra följande kommando.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Team och Skype

Virtuella Windows-datorer har inte stöd för Skype för företag och team.

## <a name="next-steps"></a>Nästa steg

Nu när du har lagt till Office i avbildningen kan du fortsätta att anpassa din huvud-VHD-avbildning. Se [förbereda och anpassa en huvud-VHD-avbildning](set-up-customize-master-image.md).
