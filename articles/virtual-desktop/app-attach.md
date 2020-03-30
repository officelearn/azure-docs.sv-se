---
title: Windows Virtual Desktop MSIX-app bifoga - Azure
description: Konfigurera MSIX-app bifoga för Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 20a82cbd7de4b5678648bac19ab9b59bf557b0ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128318"
---
# <a name="set-up-msix-app-attach"></a>Konfigurera MSIX-appbifogning

> [!IMPORTANT]
> MSIX app bifoga är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I det här avsnittet får du lära dig hur du konfigurerar MSIX-appansatta i en Windows Virtual Desktop-miljö.

## <a name="requirements"></a>Krav

Innan du börjar behöver du konfigurera MSIX-app bifoga:

- Tillgång till Windows Insider-portalen för att hämta versionen av Windows 10 med stöd för MSIX-appen bifoga API:er.
- En fungerande Windows Virtual Desktop-distribution. Mer information finns [i Skapa en klient i Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
- MSIX-förpackningsverktyget
- En nätverksresurs i windows Virtual Desktop-distributionen där MSIX-paketet lagras

## <a name="get-the-os-image"></a>Hämta OS-avbildningen

Först måste du hämta den OS-avbildning som du ska använda för MSIX-appen. Så här hämtar du OS-avbildningen:

1. Öppna [Windows Insider-portalen](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) och logga in.

     >[!NOTE]
     >Du måste vara medlem i Windows Insider-programmet för att få tillgång till Windows Insider-portalen. Mer information om Windows Insider-programmet finns i vår [Windows Insider-dokumentation](/windows-insider/at-home/).

2. Bläddra ned till avsnittet **Välj utgåva** och välj **Windows 10 Insider Preview Enterprise (FAST) – Bygg 19035** eller senare.

3. Välj **Bekräfta**och välj sedan det språk du vill använda och välj sedan **Bekräfta** igen.
    
     >[!NOTE]
     >För närvarande är engelska det enda språk som har testats med funktionen. Du kan välja andra språk, men de kanske inte visas som de är avsedda.
    
4. När nedladdningslänken genereras väljer du **64-bitarshämtningen** och sparar den på den lokala hårddisken.

## <a name="prepare-the-vhd-image-for-azure"></a>Förbereda VHD-avbildningen för Azure 

Innan du börjar måste du skapa en huvud-VHD-avbildning. Om du inte har skapat din huvud VHD-avbildning ännu går du till [Förbered och anpassa en huvud-VHD-avbildning](set-up-customize-master-image.md) och följer instruktionerna där. 

När du har skapat den virtuella huvudavbildningen måste du inaktivera automatiska uppdateringar för MSIX-appanklädnadsprogram. Om du vill inaktivera automatiska uppdateringar måste du köra följande kommandon i en upphöjd kommandotolk:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

Förbered sedan den virtuella datorn för Azure och ladda upp den resulterande VHD-disken till Azure. Mer information finns i [Förbereda och anpassa en virtuell huvudbild](set-up-customize-master-image.md).

När du har laddat upp den virtuella hårddisken till Azure skapar du en värdpool som baseras på den här nya avbildningen genom att följa instruktionerna i guiden Skapa en värdpool med hjälp av azure [marketplace-självstudien.](create-host-pools-azure-marketplace.md)

## <a name="prepare-the-application-for-msix-app-attach"></a>Förbereda programmet för MSIX-app bifoga 

Om du redan har ett MSIX-paket går du vidare till [Konfigurera Windows Virtual Desktop-infrastruktur](#configure-windows-virtual-desktop-infrastructure). Om du vill testa äldre program följer du instruktionerna i [Skapa ett MSIX-paket från ett skrivbordsinstallatör på en virtuell dator](/windows/msix/packaging-tool/create-app-package-msi-vm/) för att konvertera det äldre programmet till ett MSIX-paket.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Generera ett VHD- eller VHDX-paket för MSIX

Paketen är i VHD- eller VHDX-format för att optimera prestanda. MSIX kräver VHD- eller VHDX-paket för att fungera korrekt.

Så här genererar du ett VHD- eller VHDX-paket för MSIX:

1. [Hämta msixmgr-verktyget](https://aka.ms/msixmgr) och spara ZIP-mappen i en mapp i en virtuell dator för session.

2. Packa upp msixmgr-verktyget .zip-mappen.

3. Placera käll-MSIX-paketet i samma mapp där du packade upp msixmgr-verktyget.

4. Kör följande cmdlet i PowerShell för att skapa en virtuell hårddisk:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Kontrollera att storleken på den virtuella hårddisken är tillräckligt stor för att rymma den expanderade MSIX.*

5. Kör följande cmdlet för att montera den nyskapade virtuella hårddisken:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Kör den här cmdleten för att initiera den virtuella hårddisken:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Kör den här cmdleten för att skapa en ny partition:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Kör den här cmdleten för att formatera partitionen:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Skapa en överordnad mapp på den monterade virtuella hårddisken. Det här steget är obligatoriskt eftersom MSIX-appen som bifogas kräver en överordnad mapp. Du kan namnge den överordnade mappen vad du vill.

### <a name="expand-msix"></a>Expandera MSIX

Därefter måste du "expandera" MSIX-avbildningen genom att packa upp den. Så här packar du upp MSIX-avbildningen:

1. Öppna en kommandotolk som administratör och navigera till mappen där du hämtade och packade upp msixmgr-verktyget.

2. Kör följande cmdlet för att packa upp MSIX i den virtuella hårddisk som du skapade och monterade i föregående avsnitt.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Följande meddelande ska visas när uppackningen är klar:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Om du använder paket från Microsoft Store för företag (eller utbildning) i nätverket eller på enheter som inte är anslutna till internet måste du skaffa paketlicenserna från Store och installera dem för att kunna köra appen. Se [Använda paket offline](#use-packages-offline).

3. Navigera till den monterade virtuella hårddisken och öppna appmappen och bekräfta att paketinnehåll finns.

4. Demontera den virtuella hårddisken.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Konfigurera Infrastruktur för virtuellt skrivbord i Windows

Avsiktligt kan ett enda MSIX-utökat paket (den virtuella hårddisk som du skapade i föregående avsnitt) delas mellan virtuella datorer för flera sessionsvärdar när virtuella hårddiskar är kopplade i skrivskyddat läge.

Innan du börjar kontrollerar du att nätverksresursen uppfyller följande krav:

- Aktien är SMB-kompatibel.
- De virtuella datorer som ingår i sessionsvärdpoolen har NTFS-behörigheter till resursen.

### <a name="set-up-an-msix-app-attach-share"></a>Konfigurera en MSIX-app bifoga resurs 

Skapa en nätverksresurs i Windows Virtual Desktop-miljön och flytta paketet dit.

>[!NOTE]
> Det bästa sättet att skapa MSIX-nätverksresurser är att konfigurera nätverksresursen med NTFS skrivskyddade behörigheter.

## <a name="install-certificates"></a>Installera certifikat

Så här installerar du ett certifikat som inte är offentligt eller som är självsignerat:

1. Högerklicka på paketet och välj **Egenskaper**.
2. Välj fliken **Digitala signaturer** i fönstret som visas. Det bör bara finnas ett objekt i listan på fliken, som visas i följande bild. Markera det objektet om du vill markera objektet och välj sedan **Detaljer**.
3. När fönstret för information om digital signatur visas väljer du fliken **Allmänt** och väljer sedan **Installera certifikat**.
4. När installationsprogrammet öppnas väljer du **lokal dator** som lagringsplats och väljer sedan **Nästa**.
5. Om installationsprogrammet frågar dig om du vill tillåta att appen gör ändringar på enheten väljer du **Ja**.
6. Välj **Placera alla certifikat i följande butik**och välj sedan **Bläddra**.
7. När fönstret välj certifikatarkiv visas väljer du **Betrodda personer**och väljer sedan **OK**.
8. Välj **Slutför**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Förbereda PowerShell-skript för MSIX-app bifoga

MSIX app bifoga har fyra olika faser som måste utföras i följande ordning:

1. Fas
2. Registrera dig
3. Avregistrera
4. Destage (avstage)

Varje fas skapar ett PowerShell-skript. Exempel skript för varje fas finns [här](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-the-powershell-script"></a>Arrangera PowerShell-skriptet

Innan du uppdaterar PowerShell-skripten kontrollerar du att du har volymen GUID för volymen i den virtuella hårddisken. Så här hämtar du volymen GUID:

1.  Öppna nätverksresursen där den virtuella hårddisken finns i den virtuella datorn där du ska köra skriptet.

2.  Högerklicka på den virtuella hårddisken och välj **Montera**. Detta monterar den virtuella hårddisken på en enhetsbeteckning.

3.  När du har monterat den virtuella hårddisken öppnas **utforskaren.** Fånga den överordnade mappen och uppdatera **variabeln $parentFolder**

    >[!NOTE]
    >Om du inte ser en överordnad mapp betyder det att MSIX inte expanderades korrekt. Gör om föregående avsnitt och försök igen.

4.  Öppna den överordnade mappen. Om den har expanderats korrekt visas en mapp med samma namn som paketet. Uppdatera **variabeln $packageName** så att den matchar namnet på den här mappen.

    Till exempel `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Öppna en kommandotolk och ange **mountvol**. Det här kommandot visar en lista över volymer och deras GUID.This command will display a list of volumes and their GUIDs. Kopiera GUID för volymen där enhetsbeteckningen matchar den enhet du monterade din virtuella hårddisk till i steg 2.

    I det här exemplet för kommandot mountvol, om du monterade den virtuella hårddisken på enhet `C:\`C, vill du till exempel kopiera värdet ovan:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Uppdatera **$volumeGuid** variabeln med den volym-GUID som du just kopierade.

7. Öppna en PowerShell-fråga för admin och uppdatera följande PowerShell-skript med de variabler som gäller för din miljö.

    ```powershell
    #MSIX app attach staging sample

    #region variables

    $vhdSrc="<path to vhd>"

    $packageName = "<package name>"

    $parentFolder = "<package parent folder>"

    $parentFolder = "\" + $parentFolder + "\"

    $volumeGuid = "<vol guid>"

    $msixJunction = "C:\temp\AppAttach\"

    #endregion

    #region mountvhd

    try

    {

    Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red

    }

    #endregion

    #region makelink

    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"

    if (!(Test-Path $msixJunction))

    {

    md $msixJunction

    }

    $msixJunction = $msixJunction + $packageName

    cmd.exe /c mklink /j $msixJunction $msixDest

    #endregion

    #region stage

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>Registrera PowerShell-skript

Om du vill köra registerskriptet kör du följande PowerShell-cmdletar med platshållarvärdena ersatta med värden som gäller för din miljö.

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path $path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>Avregistrera PowerShell-skript

För det här skriptet ersätter du platshållaren för **$packageName** med namnet på paketet som du testar.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>Avsende PowerShell-skript

För det här skriptet ersätter du platshållaren för **$packageName** med namnet på paketet som du testar.

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Konfigurera simuleringsskript för MSIX-appattennat

När du har skapat skripten kan användarna köra dem manuellt eller konfigurera dem så att de körs automatiskt som start-, inloggnings-, utloggnings- och avstängningsskript. Mer information om dessa typer av skript finns i [Använda start-, avstängnings-, inloggnings- och utloggningsskript i Grupprincip](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Vart och ett av dessa automatiska skript kör en fas av appen bifoga skript:

- Startskriptet kör scenskriptet.
- Inloggningsskriptet kör registerskriptet.
- Loggningsskriptet kör avregistreringsskriptet.
- Avstängningsskriptet kör avstegsskriptet.

## <a name="use-packages-offline"></a>Använda paket offline

Om du använder paket från [Microsoft Store för företag](https://businessstore.microsoft.com/) eller Microsoft Store for [Education](https://educationstore.microsoft.com/) i nätverket eller på enheter som inte är anslutna till internet måste du hämta paketlicenserna från Microsoft Store och installera dem på enheten för att kunna köra appen. Om enheten är online och kan ansluta till Microsoft Store för företag ska de nödvändiga licenserna hämtas automatiskt, men om du är offline måste du konfigurera licenserna manuellt. 

Om du vill installera licensfilerna måste du använda ett PowerShell-skript som anropar MDM_EnterpriseModernAppManagement_StoreLicenses02_01-klassen i WMI Bridge Provider.  

Så här konfigurerar du licenser för offlineanvändning: 

1. Ladda ned appaketet, licenserna och de nödvändiga ramverken från Microsoft Store för företag. Du behöver både kodade och okodade licensfiler. Detaljerade nedladdningsinstruktioner hittar du [här](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Uppdatera följande variabler i skriptet för steg 3:
      1. `$contentID`är ContentID-värdet från den okodade licensfilen (.xml). Du kan öppna licensfilen i en textredigerare som du väljer.
      2. `$licenseBlob`är hela strängen för licensbloben i den kodade licensfilen (.bin). Du kan öppna den kodade licensfilen i en textredigerare som du väljer. 
3. Kör följande skript från en Admin PowerShell-prompt. Ett bra ställe att utföra licensinstallation är i slutet av [mellanlagringsskriptet](#stage-the-powershell-script) som också måste köras från en admin-prompt.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession 

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />' 

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param) 


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}  
```

## <a name="next-steps"></a>Nästa steg

Den här funktionen stöds för närvarande inte, men du kan ställa frågor till communityn på [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Du kan också lämna feedback för Windows Virtual Desktop i [feedbackhubben](https://aka.ms/MRSFeedbackHub)för Windows Virtual Desktop eller lämna feedback för MSIX-appen och förpackningsverktyget i [MSIX-appen som bifogar feedbackhubben](https://aka.ms/msixappattachfeedback) och [återkopplingshubben för MSIX-förpackningsverktyget](https://aka.ms/msixtoolfeedback).
