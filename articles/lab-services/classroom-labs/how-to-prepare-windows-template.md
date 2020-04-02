---
title: Guide för att konfigurera en Windows-malldator | Microsoft-dokument
description: Allmänna steg för att förbereda en Windows-malldator i Lab Services.  De här stegen omfattar att ställa in Windows Update-schema, installera OneDrive och installera Office.
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c1aaf588f61b329fa3b838b8a92f3e287897315b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521175"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Guide för att konfigurera en Windows-malldator i Azure Lab Services

Om du konfigurerar en Windows 10-malldator för Azure Lab Services kan du överväga följande: Här är några metodtips och tips. Konfigurationsstegen nedan är alla valfria.  De här förberedande stegen kan dock göra dina elever mer produktiva, minimera klasstidsavbrott och se till att de använder den senaste tekniken.

>[!IMPORTANT]
>Den här artikeln innehåller PowerShell-kodavsnitt för att effektivisera ändringsprocessen för datormallen.  För alla PowerShell-skript som visas bör du köra dem i Windows PowerShell med administratörsbehörighet. I Windows 10, ett snabbt sätt att göra det är att högerklicka på Start-menyn och välj "Windows PowerShell (Admin)".

## <a name="install-and-configure-onedrive"></a>Installera och konfigurera OneDrive

För att skydda elevdata från att gå förlorade om en virtuell dator återställs rekommenderar vi eleverna att säkerhetskopiera sina data till molnet.  Microsoft OneDrive kan hjälpa eleverna att skydda sina data.  

### <a name="install-onedrive"></a>Installera OneDrive

Om du vill hämta och installera OneDrive manuellt finns på hämtningssidorna för [OneDrive](https://onedrive.live.com/about/download/) eller [OneDrive för företag.](https://onedrive.live.com/about/business/)

Du kan också använda följande PowerShell-skript.  Den hämtar och installerar automatiskt den senaste versionen av OneDrive.  När OneDrive-klienten har installerats kör du installationsprogrammet.  I vårt exempel använder `/allUsers` vi växeln för att installera OneDrive för alla användare på datorn. Vi använder `/silent` också växeln för att tyst installera OneDrive.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>Anpassningar av OneDrive

Det finns många [anpassningar som kan göras på OneDrive](https://docs.microsoft.com/onedrive/use-group-policy). Låt oss täcka några av de vanligaste anpassningarna.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Flytta Windows kända mappar tyst till OneDrive

Mappar som Dokument, Nedladdningar och Bilder används ofta för att lagra elevfiler. För att säkerställa att dessa mappar säkerhetskopieras till OneDrive rekommenderar vi att du flyttar mapparna till OneDrive.

Om du använder en dator som inte använder Active Directory kan användarna manuellt flytta mapparna till OneDrive när de har autentiserats till OneDrive.

1. Öppna Utforskaren
2. Högerklicka på mappen Dokument, Nedladdningar eller Bilder.
3. Gå till Egenskaper > plats.  Flytta mappen till en ny mapp i OneDrive-katalogen.

Om den virtuella datorn är ansluten till Active Directory kan du ställa in malldatorn så att deltagarna automatiskt uppmanas att flytta de kända mapparna till OneDrive.  

Du måste hämta ditt Office-klient-ID först.  Mer information finns i [hitta ditt Klient-ID för Office 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).  Du kan också hämta Klient-ID:t för Office 365 med hjälp av följande PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

När du har ditt Klient-ID för Office 365 anger du att OneDrive uppmanas att flytta kända mappar till OneDrive med följande PowerShell.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Använda OneDrive-filer på begäran

Deltagare kan ha många filer i sina OneDrive-konton. För att spara utrymme på datorn och minska hämtningstiden rekommenderar vi att alla filer som lagras i elevens OneDrive-konto blir på begäran.  Filer på begäran hämtas bara när en användare har åtkomst till filen.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Logga in användare tyst på OneDrive

OneDrive kan ställas in så att den inloggade användaren automatiskt loggar in med Windows-autentiseringsuppgifterna.  Automatisk inloggning är användbar för klasser där eleven loggar in med sina Office 365-skolreferenser.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Inaktivera självstudien som visas i slutet av OneDrive-konfigurationen

Med den här inställningen kan du förhindra att självstudien startar i en webbläsare i slutet av installationsprogrammet för OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Ange den maximala storleken på en fil som ska hämtas automatiskt

Den här inställningen används tillsammans med Logga in användare tyst i OneDrive-synkroniseringsklienten med sina Windows-autentiseringsuppgifter på enheter som inte har OneDrive-filer på begäran aktiverat. Alla användare som har en OneDrive som är större än det angivna tröskelvärdet (i MB) uppmanas att välja de mappar som de vill synkronisera innan OneDrive-synkroniseringsklienten (OneDrive.exe) hämtar filerna.  I vårt exempel är "1111-2222-3333-4444" office 365-klient-ID och 0005000 anger en tröskel på 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>Installera och konfigurera Microsoft Office 365

### <a name="install-microsoft-office-365"></a>Installera Microsoft Office 365

Om mallmaskinen behöver Office rekommenderar vi installation av Office via [OFFICE Deployment Tool (ODT)](https://www.microsoft.com/download/details.aspx?id=49117 ). Du måste skapa en återanvändbar konfigurationsfil med [office 365-klientkonfigurationstjänsten](https://config.office.com/) för att välja vilken arkitektur, vilka funktioner du behöver från Office och hur ofta den uppdateras.

1. Gå till [Office 365 Client Configuration Service](https://config.office.com/) och hämta din egen konfigurationsfil.
2. Hämta [Distributionsverktyg för Office](https://www.microsoft.com/download/details.aspx?id=49117).  Nedladdad fil `setup.exe`kommer att vara .
3. Kör `setup.exe /download configuration.xml` för att hämta Office-komponenter.
4. Kör `setup.exe /configure configuration.xml` för att installera Office-komponenter.

### <a name="change-the-microsoft-office-365-update-channel"></a>Ändra uppdateringskanalen för Microsoft Office 365

Med office-konfigurationsverktyget kan du ange hur ofta Office tar emot uppdateringar. Om du behöver ändra hur ofta Office tar emot uppdateringar efter installationen kan du ändra url:en för uppdateringskanaler. Url-adresser för uppdateringskanaler finns på [Ändra uppdateringskanalen för Office 365 ProPlus för enheter i organisationen](https://docs.microsoft.com/deployoffice/change-update-channels). Exemplet nedan visar hur du ställer in Office 365 så att den använder den månatliga uppdateringskanalen.

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Installera och konfigurera uppdateringar

### <a name="install-the-latest-windows-updates"></a>Installera de senaste Windows-uppdateringarna

Vi rekommenderar att du installerar de senaste Microsoft-uppdateringarna på malldatorn av säkerhetsskäl innan du publicerar mallen VM.  Det kan också undvika studenter från att störas i sitt arbete när uppdateringar körs vid oväntade tidpunkter.

1. Starta **inställningar** från Start-menyn
2. Klicka på **Uppdatera** &-säkerhet
3. Klicka på **Sök efter uppdateringar**
4. Uppdateringar hämtas och installeras.

Du kan också använda PowerShell för att uppdatera malldatorn.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Vissa uppdateringar kan kräva att datorn startas om.  Du tillfrågas om en omstart krävs.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Installera de senaste uppdateringarna för Microsoft Store-appar

Vi rekommenderar att alla Microsoft Store-appar uppdateras till de senaste versionerna.  Här följer instruktioner om hur du uppdaterar program manuellt från Microsoft Store.  

1. Starta **Microsoft Store-programmet.**
2. Klicka på ellipsen (...) bredvid användarfotot i det övre hörnet av programmet.
3. Välj **Hämta** och uppdateringar på rullgardinsmenyn.
4. Klicka på **Hämta uppdateringsknapp.**

Du kan också använda PowerShell för att uppdatera Microsoft Store-program som redan är installerade.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Stoppa automatiska Windows-uppdateringar

När du har uppdaterat Windows till den senaste versionen kan du överväga att stoppa Windows-uppdateringar.  Automatiska uppdateringar kan eventuellt störa schemalagd klasstid.  Om din kurs körs längre bör du be deltagarna att manuellt söka efter uppdateringar eller ställa in automatiska uppdateringar under en tid utanför schemalagda lektionstimmar.  Mer information om anpassningsalternativ för Windows Update finns i [hantera ytterligare Windows Update-inställningar](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings).

Automatiska Windows-uppdateringar kan stoppas med följande PowerShell-skript.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Installera paket med främmande språk

Om du behöver ytterligare språk installerade på den virtuella datorn kan du lägga till dem via Microsoft Store.

1. Starta Microsoft Store
2. Sök efter "språkpaket"
3. Välj språk att installera

Om du redan är inloggad på mallen VM använder du [genvägen "Installera språkpaket"](ms-settings:regionlanguage?activationSource=SMC-IA-4027670) för att gå direkt till lämplig inställningssida.

## <a name="remove-unneeded-built-in-apps"></a>Ta bort inbyggda appar som inte behövers

Windows 10 levereras med många inbyggda program som kanske inte behövs för just din klass. Om du vill förenkla datoravbildningen för deltagare kanske du vill avinstallera vissa program från mallmaskinen.  Om du vill visa en lista `Get-AppxPackage` över installerade program använder du PowerShell-cmdleten.  Exemplet nedan visar alla installerade program som kan tas bort.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Om du vill ta bort ett program använder du cmdleten Remove-Appx.  Exemplet nedan visar hur du tar bort allt Xbox relaterade.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Installera vanliga undervisningsrelaterade program

Installera andra appar som ofta används för undervisning via Windows Store-appen. Förslag inkluderar program som [Microsoft Whiteboard app](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)och Minecraft [Education Edition](https://education.minecraft.net/). Dessa program måste installeras manuellt via Windows Store eller via deras respektive webbplatser på mallen VM.

## <a name="conclusion"></a>Slutsats

I den här artikeln visas valfria steg för att förbereda din virtuella Windows-mall för en effektiv klass.  I stegen ingår att installera OneDrive och installera Office 365, installera uppdateringar för Windows och installera uppdateringar för Microsoft Store-appar.  Vi diskuterade också hur du ställer in uppdateringar till ett schema som fungerar bäst för din klass.  

## <a name="next-steps"></a>Nästa steg
Se artikeln om hur du styr Windows avstängning beteende för att hantera kostnader: [Guide för att kontrollera Windows avstängning beteende](how-to-windows-shutdown.md)
