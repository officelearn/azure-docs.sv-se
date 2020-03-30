---
title: Förbereda och anpassa en huvud-VHD-avbildning - Azure
description: Så här förbereder, anpassar och laddar du upp en windows virtuell skrivbord-huvudavbildning till Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 867b327ac25d51cd3955e622da9d8067ae6d9ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127723"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Förbereda och anpassa en VHD-huvudavbildning

I den här artikeln beskrivs hur du förbereder en virtuell hårddiskavbildning (Master Virtual Hard Disk) för överföring till Azure, inklusive hur du skapar virtuella datorer (VMs) och installerar programvara på dem. De här anvisningarna gäller en Windows Virtual Desktop-specifik konfiguration som kan användas med organisationens befintliga processer.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Windows 10 Enterprise multi-session är tillgängligt i Azure Image Gallery. Det finns två alternativ för att anpassa den här bilden.

Det första alternativet är att etablera en virtuell dator (VM) i Azure genom att följa instruktionerna i [Skapa en virtuell dator från en hanterad avbildning](../virtual-machines/windows/create-vm-generalized-managed.md)och sedan hoppa vidare till Förberedelse och installation av [programvara](set-up-customize-master-image.md#software-preparation-and-installation).

Det andra alternativet är att skapa bilden lokalt genom att ladda ner bilden, etablera en Hyper-V VM och anpassa den efter dina behov, som vi täcker i följande avsnitt.

### <a name="local-image-creation"></a>Skapa lokal bild

När du har hämtat avbildningen till en lokal plats öppnar du **Hyper-V Manager** för att skapa en virtuell dator med den virtuella hårddisk som du kopierade. Följande instruktioner är en enkel version, men du kan hitta mer detaljerade instruktioner i [Skapa en virtuell dator i Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Så här skapar du en virtuell dator med den kopierade virtuella hårddisken:

1. Öppna **guiden Ny virtuell dator**.

2. Välj Generation **1**på sidan Ange generering .

    ![En skärmbild av sidan Ange generation. Alternativet "Generation 1" är markerat.](media/a41174fd41302a181e46385e1e701975.png)

3. Inaktivera kontrollpunkter under Kontrollpunktstyp genom att avmarkera kryssrutan.

    ![En skärmbild av avsnittet Kontrollpunktstyp på sidan Kontrollpunkter.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Du kan också köra följande cmdlet i PowerShell för att inaktivera kontrollpunkter.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Fast disk

Om du skapar en virtuell dator från en befintlig virtuell hårddisk skapas en dynamisk disk som standard. Det kan ändras till en fast disk genom att välja **Redigera disk ...** som visas i följande bild. Mer detaljerade instruktioner finns i [Förbereda en Windows VHD eller VHDX för att ladda upp till Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

![En skärmbild av alternativet Redigera disk.](media/35772414b5a0f81f06f54065561d1414.png)

Du kan också köra följande PowerShell-cmdlet för att ändra disken till en fast disk.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Förberedelse och installation av programvara

Det här avsnittet beskriver hur du förbereder och installerar FSLogix och Windows Defender, samt några grundläggande konfigurationsalternativ för appar och avbildningens register. 

Om du installerar Office 365 ProPlus och OneDrive på den virtuella datorn går du till [Installera Office på en huvud-VHD-avbildning](install-office-on-wvd-master-image.md) och följer instruktionerna för att installera apparna. När du är klar går du tillbaka till den här artikeln.

Om användarna behöver komma åt vissa LOB-program rekommenderar vi att du installerar dem när du har slutfört instruktionerna i det här avsnittet.

### <a name="set-up-user-profile-container-fslogix"></a>Konfigurera behållare för användarprofiler (FSLogix)

Om du vill inkludera FSLogix-behållaren som en del av avbildningen följer du instruktionerna i [Skapa en profilbehållare för en värdpool med hjälp av en filresurs](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Du kan testa fslogix-behållarens funktioner med [den här snabbstarten](/fslogix/configure-cloud-cache-tutorial/).

### <a name="configure-windows-defender"></a>Konfigurera Windows Defender

Om Windows Defender är konfigurerat i den virtuella datorn kontrollerar du att det är konfigurerat för att inte söka igenom hela innehållet i VHD- och VHDX-filer under bifogad fil.

Den här konfigurationen tar bara bort skanning av VHD- och VHDX-filer under bifogad fil, men påverkar inte skanningen i realtid.

Mer detaljerade instruktioner om hur du konfigurerar Windows Defender på Windows Server finns i [Konfigurera undantag för Antivirus från Windows Defender på Windows Server](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Mer information om hur du konfigurerar Windows Defender så att vissa filer kan skannas finns i [Konfigurera och validera undantag baserat på filnamnstillägg och mappplats](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/).

### <a name="disable-automatic-updates"></a>Inaktivera automatiska uppdateringar

Så här inaktiverar du automatiska uppdateringar via lokal grupprincip:

1. Öppna **administrativa mallar\\\\för lokala\\grupprinciper i Windows Components Windows Update**.
2. Högerklicka på **Konfigurera automatisk uppdatering** och ställ in den på **Inaktiverad**.

Du kan också köra följande kommando i en kommandotolk för att inaktivera Automatiska uppdateringar.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Ange Startlayout för Windows 10-datorer (valfritt)

Kör det här kommandot om du vill ange en Start-layout för Windows 10-datorer.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Ställ in omdirigering av tidszon

Omdirigering av tidszoner kan tillämpas på grupprincipnivå eftersom alla virtuella datorer i en värdpool ingår i samma säkerhetsgrupp.

Så här omdirigerar du tidszoner:

1. Öppna **konsolen Grupprinciphantering**på Active Directory-servern.
2. Expandera domän- och grupprincipobjekt.
3. Högerklicka på det **grupprincipobjekt** som du skapade för grupprincipinställningarna och välj **Redigera**.
4. I **redigeraren för grupprinciphantering**navigerar du till**Policies** > **administrativa mallar** > för **datorkonfigurationsprinciper** > **i Windows Components** > **Remote Desktop Services** > **För fjärrskrivbordssession** > **värdenhet och resursomdirigering**.
5. Aktivera inställningen **Tillåt omdirigering av tidszon.**

Du kan också köra det här kommandot på huvudavbildningen för att omdirigera tidszoner:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Inaktivera lagringskänsla

För Windows Virtual Desktop-sessionsvärd som använder windows 10 Enterprise eller Windows 10 Enterprise multisession rekommenderar vi att du inaktiverar Storage Sense. Du kan inaktivera Storage Sense på menyn Inställningar under **Lagring**, som visas i följande skärmbild:

![En skärmbild av lagringsmenyn under Inställningar. Alternativet "Lagringskänsla" är inaktiverat.](media/storagesense.png)

Du kan också ändra inställningen med registret genom att köra följande kommando:

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Inkludera ytterligare språkstöd

Den här artikeln beskriver inte hur du konfigurerar språk och regionalt stöd. Mer information finns i följande artiklar:

- [Lägga till språk i Windows-bilder](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Funktioner på begäran](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Språk- och regionfunktioner på begäran (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Andra program och registerkonfigurationer

Det här avsnittet täcker konfiguration av program och operativsystem. All konfiguration i det här avsnittet görs via registerposter som kan köras med kommandorads- och regeditverktyg.

>[!NOTE]
>Du kan implementera metodtips i konfigurationen med grupprincipobjekt eller registerimport. Administratören kan välja något av alternativen baserat på organisationens krav.

Om du vill hämta data för återkopplingshubben för telemetridata i multisessioner i Windows 10 Enterprise kör du det här kommandot:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Kör följande kommando för att åtgärda Watson kraschar:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Ange följande kommandon i registereditorn för att åtgärda stöd för 5k-upplösning. Du måste köra kommandona innan du kan aktivera stacken sida vid sida.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Förbereda avbildningen för överföring till Azure

När du har konfigurerat och installerat alla program följer du instruktionerna i [Förbered en Windows VHD eller VHDX för att ladda upp till Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) för att förbereda avbildningen.

När du har förberett avbildningen för överföring kontrollerar du att den virtuella datorn förblir i tillståndet av eller frigör.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Ladda upp huvudavbildning till ett lagringskonto i Azure

Det här avsnittet gäller bara när huvudbilden skapades lokalt.

Följande instruktioner talar om för dig hur du laddar upp din huvudavbildning till ett Azure-lagringskonto. Om du inte redan har ett Azure-lagringskonto följer du instruktionerna i den [här artikeln](/azure/javascript/tutorial-vscode-static-website-node-03) för att skapa ett.

1. Konvertera VM-avbildningen (VHD) till Fast om du inte redan har gjort det. Om du inte konverterar bilden till Fast kan du inte skapa bilden.

2. Ladda upp den virtuella hårddisken till en blob-behållare i ditt lagringskonto. Du kan ladda upp snabbt med [verktyget Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/). Mer information om verktyget Lagringsutforskaren finns i [den här artikeln](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![En skärmbild av sökfönstret i Microsoft Azure Storage Explorer Tool. Kryssrutan "Ladda upp VHD- eller vhdx-filer som sidblobar (rekommenderas)" är markerad.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Gå sedan till Azure-portalen i webbläsaren och sök efter "Bilder". Sökningen ska leda dig till sidan **Skapa bild,** som visas i följande skärmbild:

    ![En skärmbild av sidan Skapa avbildning i Azure-portalen, fylld med exempelvärden för avbildningen.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. När du har skapat bilden bör du se ett meddelande som det i följande skärmbild:

    ![En skärmbild av meddelandet "skapad bild".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har en avbildning kan du skapa eller uppdatera värdpooler. Mer information om hur du skapar och uppdaterar värdpooler finns i följande artiklar:

- [Skapa en värdpool med en Azure Resource Manager-mall](create-host-pools-arm-template.md)
- [Självstudiekurs: Skapa en värdpool med Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Skapa en värdpool med PowerShell](create-host-pools-powershell.md)
- [Skapa en profilcontainer för en värdpool med hjälp av en filresurs](create-host-pools-user-profile.md)
- [Konfigurera metoden för belastningsutjämning för Windows Virtual Desktop](configure-host-pool-load-balancing.md)
