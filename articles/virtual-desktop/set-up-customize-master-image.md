---
title: Förbereda och anpassa en virtuell Hårddisk huvudavbildning - Azure
description: Hur du förbereder, anpassa och ladda upp en huvudavbildning för virtuellt skrivbord i Windows-förhandsversion till Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2019
ms.author: helohr
ms.openlocfilehash: 9df4be5534a1cbe6aa4ffb9c60bb180fd4587d32
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551035"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Förbereda och anpassa en VHD-huvudavbildning

Den här artikeln beskriver hur du förbereder en avbildning av master virtuell hårddisk (VHD) för överföring till Azure, inklusive hur du skapar virtuella datorer (VM) och installera programvara på dem. Dessa instruktioner är för en virtuell Windows-skrivbordet förhandsversion konfiguration som kan användas med din organisations befintliga processer.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Windows 10 Enterprise flera session är tillgänglig i Azure-bildgalleriet. Det finns två alternativ för att anpassa den här avbildningen.

Det första alternativet är att etablera en virtuell dator (VM) i Azure genom att följa instruktionerna i [skapa en virtuell dator från en hanterad avbildning](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed), och sedan gå vidare till [förberedelse av programvara och installation](set-up-customize-master-image.md#software-preparation-and-installation).

Det andra alternativet är att skapa avbildningen lokalt genom att hämta avbildningen, etablera en Hyper-V virtuell dator och anpassa den så att den passar dina behov, vilket vi upp i följande avsnitt.

### <a name="local-image-creation"></a>Skapa lokala avbildningar

När du har laddat ned avbildningen till en lokal plats, öppna **Hyper-V Manager** att skapa en virtuell dator med den virtuella Hårddisken som du kopierade. Följande instruktioner är en enkel version, men du kan hitta mer detaljerad information i [skapa en virtuell dator i Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).

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

Det här avsnittet beskriver hur du förbereder och installera FSLogix, Windows Defender och andra vanliga program. 

Om du installerar Office 365 ProPlus och OneDrive på den virtuella datorn, se [installerar Office på en VHD-huvudavbildning](install-office-on-wvd-master-image.md). Följ länken i nästa steg i den här artikeln att gå tillbaka till den här artikeln och slutföra master VHD-processen.

Om dina användare behöver åtkomst till vissa LOB-program, rekommenderar vi du installera dem när du har slutfört instruktionerna i det här avsnittet.

### <a name="disable-automatic-updates"></a>Inaktivera automatiska uppdateringar

Inaktivera automatiska uppdateringar via lokal grupprincip:

1. Öppna **Redigeraren för lokal grupprincip\\Administrationsmallar\\Windows-komponenter\\Windows Update**.
2. Högerklicka på **konfigurera automatisk uppdatering** och ge den värdet **inaktiverad**.

Du kan också köra följande kommando i Kommandotolken för att inaktivera automatiska uppdateringar.

```batch
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Ange Start-layout för Windows 10-datorer (valfritt)

Kör detta kommando för att ange en Start-layout för Windows 10-datorer.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-user-profile-container-fslogix"></a>Konfigurera användarbehållaren profil (FSLogix)

För att inkludera FSLogix behållaren som en del av avbildningen, följer du anvisningarna i [ställa in en användare profil resurs för en värd-pool](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Du kan testa funktionerna i behållaren FSLogix med [snabbstarten](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start).

### <a name="configure-windows-defender"></a>Konfigurera Windows Defender

Om Windows Defender konfigureras på den virtuella datorn kan du kontrollera att den har konfigurerats för att inte genomsökning hela innehållet i VHD och VHDX-filer under bifogad fil.

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
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 10800000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 7200000 /f
```

### <a name="set-up-time-zone-redirection"></a>Konfigurera omdirigering av tidszon

Omdirigering av tidszon kan tillämpas på nivån Grupprincip eftersom alla virtuella datorer i en värd-pool är en del av samma säkerhetsgrupp.

Att omdirigera tidszoner:

1. På Active Directory-servern öppnar du den **konsolen Grupprinciphantering**.
2. Expandera din domän och grupprincipobjekt.
3. Högerklicka på den **grupprincipobjekt** som du skapade för inställningar av grupprinciper och välj **redigera**.
4. I den **Redigeraren för Grupprinciphantering**, gå till **Datorkonfiguration** > **principer** > **administrativa Mallar** > **Windows-komponenter** > **Fjärrskrivbordstjänster** > **värd för fjärrskrivbordssession**   >  **Enhet och resurs**.
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
>Du kan implementera bästa praxis i konfigurationen med grupprincipobjekt (GPO) eller registret import. Administratören kan välja något av alternativen utifrån organisationens krav.

För feedback hub insamlingen av dessa data på flera session för Windows 10 Enterprise kan du köra det här kommandot:

```batch
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection "AllowTelemetry"=dword:00000003
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
- [Självstudie: Skapa en värd-pool med Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Skapa en värd-pool med PowerShell](create-host-pools-powershell.md)
- [Skapa en användare profil resurs för en värd-pool](create-host-pools-user-profile.md)
- [Konfigurera den virtuella Windows-skrivbordet belastningsutjämningsmetoden](configure-host-pool-load-balancing.md)
