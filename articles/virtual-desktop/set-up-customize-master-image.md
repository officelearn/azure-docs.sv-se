---
title: Förbereda och anpassa en virtuell Hårddisk huvudavbildning - Azure
description: Hur du förbereder, anpassa och ladda upp en huvudavbildning för virtuellt skrivbord i Windows-förhandsversion till Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: ccea3ebae4bcc19410cfb5537a7140f69b04c4e7
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438792"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Förbereda och anpassa en VHD-huvudavbildning

Den här artikeln kommer berättar hur du förbereder en avbildning av master virtuell hårddisk (VHD) för överföring till Azure, inklusive hur du skapar virtuella datorer (VM) och installera och konfigurera programvara på dem. Dessa instruktioner är för en virtuell Windows-skrivbordet förhandsversion konfiguration som kan användas med din organisations befintliga processer.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Windows 10 Enterprise flera session är tillgänglig i Azure-bildgalleriet. Det finns två alternativ för att anpassa den här avbildningen.

Det första alternativet är att etablera en virtuell dator (VM) i Azure genom att följa instruktionerna i [skapa en virtuell dator från en hanterad avbildning](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed), och sedan gå vidare till [förberedelse av programvara och installation](set-up-customize-master-image.md#software-preparation-and-installation).

Det andra alternativet är att skapa avbildningen lokalt genom att hämta avbildningen, etablera en Hyper-V virtuell dator och anpassa den så att den passar dina behov, vilket vi tar upp i följande avsnitt.

### <a name="local-image-creation"></a>Skapa lokala avbildningar

När du har laddat ned avbildningen till en lokal plats, öppna **Hyper-V Manager** att skapa en virtuell dator med den virtuella Hårddisken som du kopierade. Följande är en enkel version, men du kan hitta mer detaljerad information i [skapa en virtuell dator i Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).

Skapa en virtuell dator med den kopierade virtuella Hårddisken:

1. Öppna den **guiden Ny virtuell dator**.

2. På sidan Ange Generation väljer **Generation 1**.

    ![En skärmbild av sidan Ange Generation. Alternativet ”Generation 1” har valts.](media/a41174fd41302a181e46385e1e701975.png)

3. Inaktivera kontrollpunkter under typ av kontrollpunkt genom att avmarkera kryssrutan.

    ![En skärmbild av den typ av kontrollpunkt delen av sidan kontrollpunkter.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Du kan också köra följande cmdlet i PowerShell för att inaktivera kontrollpunkter.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Fast hårddisk

Om du skapar en virtuell dator från en befintlig virtuell Hårddisk skapar en dynamisk disk som standard. Det kan ändras till en fast disk genom att välja **redigera Disk...**  enligt följande bild. Mer detaljerade instruktioner finns i [förbereda en Windows-VHD eller VHDX för att överföra till Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

![En skärmbild av alternativet Redigera Disk.](media/35772414b5a0f81f06f54065561d1414.png)

Du kan också köra följande PowerShell-cmdlet om du vill ändra disken till en fast disk.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Förberedelse av programvara och installation

Det här avsnittet beskriver hur du förbereder och installera Office 365 ProPlus, OneDrive, FSLogix, Windows Defender och andra vanliga program. Om dina användare behöver åtkomst till vissa LOB-program, rekommenderar vi du installera dem när du har slutfört instruktionerna i det här avsnittet.

Det här avsnittet förutsätter att du har haft utökade åtkomst på den virtuella datorn om den har etablerats i Azure eller Hyper-V Manager.

### <a name="install-office-in-shared-computer-activation-mode"></a>Installera Office i läget för aktivering av delade datorer

Använd den [distributionsverktyget för Office](https://www.microsoft.com/download/details.aspx?id=49117) att installera Office. Windows 10 Enterprise flera session stöder endast Office 365 ProPlus, inte Office 2019 beständig.

Distributionsverktyget för Office kräver en XML-konfigurationsfilen. Om du vill anpassa följande exempel finns i den [konfigurationsalternativ för distributionsverktyget för Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Den här exempelkonfigurationen XML som vi tillhandahåller kommer att göra följande:

- Installera Office från Insiders kanalen och leverera uppdateringar från Insiders kanalen när de är körs.
- Använd x64 arkitektur.
- Inaktivera automatiska uppdateringar.
- Installera Visio och projekt.
- Ta bort alla befintliga installationer av Office och migrera deras inställningar.
- Aktivera delad dator licensiering för åtgärd i en terminal server-miljö.

Här är vad den här exempelkonfigurationen XML inte göra:

- Installera Skype för företag
- Installera OneDrive i läget per användare. Mer information finns i [installera OneDrive i per dator läge](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Delad dator licensiering kan ställas in via grupprincipobjekt (GPO) eller registerinställningar. Grupprincipobjektet finns på **Datorkonfiguration\\principer\\Administrationsmallar\\Microsoft Office 2016 (dator)\\för licensiering**

Distributionsverktyget för Office innehåller setup.exe. För att installera Office, kör du följande kommando i Kommandotolken:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Exempel på configuration.xml

I följande XML-exempel installerar Insiders-versionen, även kallat Insiders snabbt eller Insiders Main.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
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
    <Updates Enabled="FALSE" UpdatePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
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

Du kan inaktivera automatiska uppdateringar manuellt.

Inaktivera automatiska uppdateringar:

1. Installera Office 365 genom att följa instruktionerna i [förberedelse av programvara och installation](set-up-customize-master-image.md#software-preparation-and-installation).
2. Installera eventuell programvara genom att följa instruktionerna i [ställa in profilen användarbehållaren (FSLogix)](set-up-customize-master-image.md#set-up-user-profile-container-fslogix), [konfigurera Windows Defender](set-up-customize-master-image.md#configure-windows-defender), och [andra program och registret konfiguration av](set-up-customize-master-image.md#other-applications-and-registry-configuration).
3. Inaktivera Windows automatiskt Update-tjänsten på den lokala virtuella datorn.
4. Öppna **Redigeraren för lokal grupprincip\\Administrationsmallar\\Windows-komponenter\\Windows Update**.
5. Högerklicka på **konfigurera automatisk uppdatering** och ge den värdet **inaktiverad**.

Du kan också köra följande kommando i Kommandotolken för att inaktivera automatiska uppdateringar.

```batch
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

Kör detta kommando för att ange en Start-layout för Windows 10-datorer.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="install-onedrive-in-per-machine-mode"></a>Installera OneDrive i per dator-läge

OneDrive är normalt installerat per användare. I den här miljön bör det vara installerad per dator.

Här är hur du installerar OneDrive i per dator-läge:

1. Skapa först en plats för att mellanlagra OneDrive-installationsprogrammet. En lokal disk-mapp eller [\\\\unc] (file://unc) plats är bra.

2. Hämta OneDriveSetup.exe att den mellanlagrade platsen med den här länken: <https://aka.ms/OneDriveWVD-Installer>

3. Om du har installerat office med OneDrive genom att utelämna  **\<ExcludeApp ID = ”OneDrive” /\>**, avinstallera alla befintliga OneDrive användarspecifika installationer från en upphöjd kommandotolk genom att köra följande kommandot:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Kör det här kommandot från en upphöjd kommandotolk för att ange den **AllUsersInstall** registervärdet:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Kör detta kommando för att installera OneDrive i per dator-läge:

    ```batch
    Run "[staged location]\OneDriveSetup.exe /allusers"
    ```

6. Kör detta kommando för att konfigurera OneDrive för att starta vid inloggning för alla användare:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Aktivera **tyst konfigurera användarkonto** genom att köra följande kommando.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Omdirigera och flyttar Windows kända mappar till OneDrive genom att köra följande kommando.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

### <a name="teams-and-skype"></a>Teams och Skype

Virtuella Windows-skrivbordet stöder inte officiellt Skype för företag och team.

### <a name="set-up-user-profile-container-fslogix"></a>Konfigurera användarbehållaren profil (FSLogix)

För att inkludera FSLogix behållaren som en del av avbildningen, följer du anvisningarna i [ställa in en användare profil resurs för en värd-pool](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Du kan testa funktionerna i behållaren FSLogix med [snabbstarten](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start).

### <a name="configure-windows-defender"></a>Konfigurera Windows Defender

Om Windows Defender konfigureras på den virtuella datorn kan du kontrollera att den har konfigurerats för att inte genomsökning hela innehållet i VHD och VHDX-filer under koppla samma.

Den här konfigurationen kan du bara tar bort genomsökning av VHD och VHDX-filer under bifogad fil, men påverkar inte genomsökning i realtid.

Mer detaljerade instruktioner för hur du konfigurerar Windows Defender i Windows Server finns i [konfigurera Windows Defender Antivirus-undantag i Windows Server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus).

Mer information om hur du konfigurerar Windows Defender för att undanta vissa filer från sökning finns [konfigurera och verifiera undantag baserat på filplats för tillägg och mappnamn](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus).

### <a name="configure-session-timeout-policies"></a>Konfigurera principer för tidsgräns för session

Fjärrsession principerna kan tillämpas på nivån Grupprincip eftersom alla virtuella datorer i en värd-pool är en del av samma säkerhetsgrupp.

Konfigurera fjärrsession principer:

1. Gå till **Administrationsmallar** > **Windows-komponenter** > **Fjärrskrivbordstjänster**  >  **Värd för fjärrskrivbordssession** > **tidsgränser för sessionen**.
2. I panelen till höger väljer du den **ange tidsgränsen för aktiva Remote Desktop Services-sessioner** princip.
3. När fönstret modal visas ändra alternativet från **inte konfigurerad** till **aktiverad** att aktivera principen.
4. I listrutan under alternativet ställer du in hur lång tid att **4 timmar**.

Du kan också konfigurera fjärrsession principer manuellt genom att köra följande kommandon:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 600000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 5000 /f
```

### <a name="set-up-time-zone-redirection"></a>Konfigurera omdirigering av tidszon

Omdirigering av tidszon kan tillämpas på nivån Grupprincip eftersom alla virtuella datorer i en värd-pool är en del av samma säkerhetsgrupp.

Att omdirigera tidszoner:

1. På Active Directory-servern öppnar du den **konsolen Grupprinciphantering**.
2. Expandera din domän och grupprincipobjekt.
3. Högerklicka på den **grupprincipobjekt** som du skapade för inställningar av grupprinciper och välj **redigera**.
4. I den **Redigeraren för Grupprinciphantering**, gå till **Datorkonfiguration** > **principer** > **administrativa Mallar** > **Windows-komponenter** > **Horizon visa RDSH tjänster** > **fjärrskrivbordssession Värden** > **enhet och resurs**.
5. Aktivera den **Tillåt omdirigering av tidszon** inställningen.

Du kan också köra det här kommandot på huvudavbildningen att omdirigera tidszoner:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Inaktivera Sense för lagring

För Windows virtuella värd för fjärrskrivbordssessioner som använder Windows 10 Enterprise eller Windows 10 Enterprise flera session, rekommenderar vi att inaktiverar Storage mening. Du kan inaktivera Sense för lagring på menyn Inställningar under **Storage**, enligt följande skärmbild:

![En skärmbild av menyn lagring under inställningar. Alternativet ”Storage förnuft” är inaktiverad.](media/storagesense.png)

Du kan också ändra inställningen med registret genom att köra följande kommando:

```batch
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Inkludera ytterligare språk som stöds

Den här artikeln täcker inte så här konfigurerar du språk och nationella support. Mer information finns i följande artiklar:

- [Lägga till språk i Windows-avbildningar](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [Funktioner på begäran](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [Språk och din region funktioner på begäran (departement)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>Andra program och registerkonfigurationen

Det här avsnittet beskriver program- och operativsystemets konfiguration. All konfiguration i det här avsnittet görs via registerposter som kan utföras av kommandorad och regedit verktyg.

>[!NOTE]
>Du kan implementera bästa praxis i konfigurationen med allmänna gruppolicy objekt (GPO) eller registret import. Administratören kan välja något av alternativen utifrån organisationens krav.

För feedback hub insamlingen av dessa data på flera session för Windows 10 Enterprise kan du köra det här kommandot:

```batch
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection "AllowTelemetry"=dword:00000003
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection /v AllowTelemetry /d 3
```

Kör följande kommando för att åtgärda Watson krascher:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Ange följande kommandon i Registereditorn för att åtgärda 5 k matchningsstöd. Du måste köra kommandon innan du kan aktivera sida-vid-sida-stack.

```batch
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Förbereda avbildningen för uppladdning till Azure

När du har slutfört konfigurationen och installerat alla program, följer du anvisningarna i [förbereda en Windows-VHD eller VHDX för att överföra till Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) förbereda avbildningen.

När du har förberett avbildningen för uppladdning, kontrollera den virtuella datorn fortfarande är i eller om det finns frigjort tillstånd.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Ladda upp huvudavbildningen till ett lagringskonto i Azure

Det här avsnittet gäller bara när huvudavbildningen har skapats lokalt.

Följande instruktioner berätta hur du överför huvudavbildningen till ett Azure storage-konto. Om du inte redan har ett Azure storage-konto, följer du anvisningarna i [i den här artikeln](https://code.visualstudio.com/tutorials/static-website/create-storage) att skapa en.

1. Konvertera virtuell datoravbildning (VHD) som fast om du inte redan har gjort. Om du inte konverterar bilden till fast, kan du skapa avbildningen.

2. Ladda upp den virtuella Hårddisken till en blobbehållare i ditt storage-konto. Du kan ladda upp snabbt med den [Storage Explorer-verktyget](https://azure.microsoft.com/features/storage-explorer/). Läs mer om Storage Explorer-verktyget i [i den här artikeln](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![En skärmbild av Microsoft Azure Storage Explorer verktygets sökfönstret. Kryssrutan ”ladda upp VHD- eller vhdx-filer som sidblobbar (rekommenderas)” är markerad.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Gå sedan till Azure-portalen i webbläsaren och Sök efter ”avbildningar”. Sökningen ska leder dig till den **Skapa avbildning** sidan som visas i följande skärmbild:

    ![En skärmbild av sidan Skapa avbildning i Azure Portal fyllda med exempelvärden för avbildningen.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. När du har skapat avbildningen, bör du se ett meddelande som i följande skärmbild:

    ![En skärmbild av ”har skapats bild”-meddelande.](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har någon bild kan du skapa eller uppdatera värd pooler. Mer information om hur du skapar och uppdatera värden pooler finns i följande artiklar:

- [Skapa en värd-pool med en Azure Resource Manager-mall](create-host-pools-arm-template.md)
- [Självstudier: Skapa en värd-pool med Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Skapa en värd-pool med PowerShell](create-host-pools-powershell.md)
- [Skapa en användare profil resurs för en värd-pool](create-host-pools-user-profile.md)
- [Konfigurera den virtuella Windows-skrivbordet belastningsutjämningsmetoden](configure-host-pool-load-balancing.md)
