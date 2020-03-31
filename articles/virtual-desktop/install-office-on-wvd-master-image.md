---
title: Installera Office på en virtuell huvudavbildning - Azure
description: Så här installerar och anpassar du Office på en Windows Virtual Desktop-huvudavbildning till Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b93f26a6799a50868feb1f3350a3dc4a73a0b2e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127847"
---
# <a name="install-office-on-a-master-vhd-image"></a>Installera Office på en VHD-huvudavbildning

I den här artikeln beskrivs hur du installerar Office 365 ProPlus, OneDrive och andra vanliga program på en virtuell hårddiskavbildning (Master Virtual Hard Disk) för överföring till Azure. Om användarna behöver komma åt vissa LOB-program (LOB) rekommenderar vi att du installerar dem när du har slutfört instruktionerna i den här artikeln.

Den här artikeln förutsätter att du redan har skapat en virtuell dator (VM). Om inte, se [Förbereda och anpassa en huvud-VHD-bild](set-up-customize-master-image.md#create-a-vm)

Den här artikeln förutsätter också att du har förhöjd åtkomst på den virtuella datorn, oavsett om den är etablerad i Azure eller Hyper-V Manager. Om inte, se [Öka åtkomsten för att hantera alla Azure-prenumerations- och hanteringsgrupper](../role-based-access-control/elevate-access-global-admin.md).

>[!NOTE]
>De här anvisningarna gäller en Windows Virtual Desktop-specifik konfiguration som kan användas med organisationens befintliga processer.

## <a name="install-office-in-shared-computer-activation-mode"></a>Installera Office i aktiveringsläge för delad dator

Med aktivering av delade datorer kan du distribuera Office 365 ProPlus till en dator i organisationen som används av flera användare. Mer information om aktivering av delade datorer finns i [Översikt över aktivering av delade datorer för Office 365 ProPlus](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/).

Använd [Office-distributionsverktyget](https://www.microsoft.com/download/details.aspx?id=49117) för att installera Office. Windows 10 Enterprise multi-session stöder endast följande versioner av Office:
- Office 365 ProPlus
- Office 365 Business som levereras med en Microsoft 365 Business-prenumeration

Office Deployment Tool kräver en XML-fil för konfiguration. Mer om du vill anpassa följande exempel finns i [konfigurationsalternativen för Office Deployment Tool](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Det här exemplet konfiguration XML vi har lämnat kommer att göra följande saker:

- Installera Office från den månatliga kanalen och leverera uppdateringar från den månatliga kanalen när de körs.
- Använd arkitekturen x64.
- Inaktivera automatiska uppdateringar.
- Ta bort alla befintliga installationer av Office och migrera deras inställningar.
- Aktivera aktivering av delad dator.

>[!NOTE]
>Visios stencilsökfunktion kanske inte fungerar som förväntat i Windows Virtual Desktop.

Så här gör inte den här exempelkonfigurations-XML:en:

- Installera Skype för företag
- Installera OneDrive i läget per användare. Mer information finns [i Installera OneDrive i läget per dator](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Aktivering av delad dator kan ställas in via grupprincipobjekt (GRUPPRINCIPOBJEKT) eller registerinställningar. Det nationella användarobjektet finns på **administrativa mallar\\för datorkonfigurationsprinciper\\\\Microsoft Office 2016 (Dator)\\**

Distributionsverktyget för Office innehåller setup.exe. Om du vill installera Office kör du följande kommando på en kommandorad:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Exempel på configuration.xml

Följande XML-exempel installerar månadsversionen.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Office-teamet rekommenderar att du använder 64-bitarsinstallation för parametern **OfficeClientEdition.**

När du har installerat Office kan du uppdatera standardbeteendet för Office. Kör följande kommandon individuellt eller i en kommandofil för att uppdatera beteendet.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Installera OneDrive i läge per dator

OneDrive installeras normalt per användare. I den här miljön bör den installeras per dator.

Så här installerar du OneDrive i läget per dator:

1. Skapa först en plats för att arrangera OneDrive-installationsprogrammet. En lokal diskmapp\\\\eller [unc] (file://unc) plats är bra.

2. Ladda ned OneDriveSetup.exe till din iscensatta plats med den här länken:<https://aka.ms/OneDriveWVD-Installer>

3. Om du har installerat Office med OneDrive genom att utelämna ** \<ExcludeApp ID="OneDrive" /\>** avinstallerar du alla befintliga OneDrive-installationer per användare från en upphöjd kommandotolk genom att köra följande kommando:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Kör det här kommandot från en upphöjd kommandotolk för att ange registervärdet **AllUsersInstall:**

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Kör det här kommandot om du vill installera OneDrive i läget per dator:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Kör det här kommandot om du vill konfigurera OneDrive så att det startar vid inloggning för alla användare:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Aktivera **Konfigurera tyst användarkonto** genom att köra följande kommando.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Omdirigera och flytta Windows kända mappar till OneDrive genom att köra följande kommando.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Team och Skype

Windows Virtual Desktop stöder inte Skype för företag och teams.

## <a name="next-steps"></a>Nästa steg

Nu när du har lagt till Office i bilden kan du fortsätta att anpassa huvud-VHD-avbildningen. Se [Förbereda och anpassa en huvud-VHD-bild](set-up-customize-master-image.md).
