---
title: Windows Virtual Desktop MSIX-appen Attach-Azure
description: Så här konfigurerar du MSIX-appen Anslut för Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: helohr
ms.openlocfilehash: c5d6c671890f5e036d3f4cce6e880230c01048ed
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483827"
---
# <a name="set-up-msix-app-attach"></a>Konfigurera MSIX-appbifogning

> [!IMPORTANT]
> MSIX app Attach är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Det här avsnittet beskriver hur du konfigurerar MSIX app Attach i en Windows Virtual Desktop-miljö.

## <a name="requirements"></a>Krav

Innan du börjar måste du konfigurera MSIX app Attach:

- Åtkomst till Windows Insider-portalen för att hämta versionen av Windows 10 med stöd för MSIX-appen bifoga API: er.
- En fungerande distribution av virtuella Windows-datorer. Mer information finns i [skapa en klient i det virtuella Windows-skrivbordet](tenant-setup-azure-active-directory.md).
- MSIX packnings verktyg
- En nätverks resurs i distributionen av virtuella Windows-datorer där MSIX-paketet ska lagras

## <a name="get-the-os-image"></a>Hämta operativ system avbildningen

Först måste du hämta den OS-avbildning som du ska använda för MSIX-appen. Hämta operativ system avbildningen:

1. Öppna [Windows Insider-portalen](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) och logga in.

     >[!NOTE]
     >Du måste vara medlem i Windows Insider-programmet för att få åtkomst till Windows Insider-portalen. Om du vill veta mer om Windows Insider program kan du läsa vår [Windows Insider-dokumentation](https://docs.microsoft.com/windows-insider/at-home/).

2. Rulla ned till avsnittet **Välj utgåva** och välj **Windows 10 Insider Preview Enterprise (snabb) – build xxxxx**.

3. Välj **Bekräfta**och välj sedan det språk som du vill använda och välj sedan **Bekräfta** igen.
    
     >[!NOTE]
     >För tillfället är engelska det enda språk som har testats med funktionen. Du kan välja andra språk, men de visas kanske inte som de ska.
    
4. När nedladdnings länken skapas väljer du den **64-bitars hämtningen** och sparar den på den lokala hård disken.

## <a name="prepare-the-vhd-image-for-azure"></a>Förbered VHD-avbildningen för Azure 

Innan du börjar måste du skapa en huvud-VHD-avbildning. Om du inte har skapat din huvud hård disk avbildning ännu går du till [förbereda och anpassa en huvud-VHD-avbildning](set-up-customize-master-image.md) och följer anvisningarna där. 

När du har skapat din huvud hård disk avbildning måste du inaktivera automatiska uppdateringar för MSIX app attaching Applications. Om du vill inaktivera automatiska uppdateringar måste du köra följande kommandon i en upphöjd kommando tolk:

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

Förbered sedan VM VHD för Azure och överför den resulterande VHD-disken till Azure. Mer information finns i [förbereda och anpassa en huvud-VHD-avbildning](set-up-customize-master-image.md).

När du har laddat upp den virtuella hård disken till Azure skapar du en adresspool som baseras på den här nya avbildningen genom att följa anvisningarna i guiden [skapa en värddator med hjälp av Azure Marketplace](create-host-pools-azure-marketplace.md) -kursen.

## <a name="prepare-the-application-for-msix-app-attach"></a>Förbereda programmet för MSIX-appen Attach 

Om du redan har ett MSIX-paket kan du gå vidare till [Konfigurera infrastrukturen för virtuella Windows-datorer](#configure-windows-virtual-desktop-infrastructure). Om du vill testa äldre program följer du instruktionerna i [skapa ett MSIX-paket från ett Skriv bords installations program på en virtuell dator](https://docs.microsoft.com/windows/msix/packaging-tool/create-app-package-msi-vm) för att konvertera det äldre programmet till ett MSIX-paket.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Generera ett VHD-eller VHDX-paket för MSIX

Paket är i VHD-eller VHDX-format för att optimera prestanda. MSIX kräver att VHD-eller VHDX-paket fungerar korrekt.

Så här skapar du ett VHD-eller VHDX-paket för MSIX:

1. [Ladda ned msixmgr-verktyget](https://aka.ms/msixmgr) och spara zip-mappen i en mapp i en VM-session.

2. Zippa upp mappen msixmgr-verktyget. zip.

3. Lägg till käll MSIX-paketet i samma mapp där du zippade msixmgr-verktyget.

4. Kör följande cmdlet i PowerShell för att skapa en virtuell hård disk:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Se till att storleken på den virtuella hård disken är tillräckligt stor för att rymma den utökade MSIX. *

5. Kör följande cmdlet för att montera den nyligen skapade virtuella hård disken:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Kör denna cmdlet för att initiera den virtuella hård disken:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Kör denna cmdlet för att skapa en ny partition:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Kör denna cmdlet för att formatera partitionen:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Skapa en överordnad mapp på den monterade virtuella hård disken. Det här steget är obligatoriskt eftersom MSIX-appen ansluter kräver en överordnad mapp. Du kan namnge den överordnade mappen oavsett vad du vill.

### <a name="expand-msix"></a>Expandera MSIX

Därefter måste du expandera MSIX-avbildningen genom att packa upp den. Packa upp MSIX-avbildningen:

1. Öppna en kommando tolk som administratör och navigera till den mapp där du laddade ned och zippa upp msixmgr-verktyget.

2. Kör följande cmdlet för att packa upp MSIX i den virtuella hård disk som du skapade och monterade i föregående avsnitt.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Följande meddelande ska visas när uppackning är slutförd:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Om du använder paket från Microsoft Store för företag (eller utbildning) i nätverket, eller på enheter som inte är anslutna till Internet, måste du skaffa paket licenser från butiken och installera dem för att köra appen. Se [använda paket offline](#use-packages-offline).

3. Navigera till den monterade virtuella hård disken och öppna mappen app och bekräfta att paket innehållet finns.

4. Demontera den virtuella hård disken.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Konfigurera infrastrukturen för virtuella Windows-datorer

Enligt design kan ett enda MSIX-expanderat paket (den virtuella hård disk som du skapade i föregående avsnitt) delas mellan flera virtuella datorers VM-datorer som de virtuella hård diskarna är i skrivskyddat läge.

Innan du börjar kontrollerar du att nätverks resursen uppfyller följande krav:

- Resursen är SMB-kompatibel.
- De virtuella datorerna som ingår i sessionen är NTFS-behörigheter till resursen.

### <a name="set-up-an-msix-app-attach-share"></a>Konfigurera en MSIX app Attach-resurs 

I din Windows Virtual Desktop-miljö skapar du en nätverks resurs och flyttar paketet dit.

>[!NOTE]
> Den bästa metoden för att skapa MSIX nätverks resurser är att konfigurera nätverks resursen med skrivskyddad NTFS-behörighet.

## <a name="install-certificates"></a>Installera certifikat

Om din app använder ett certifikat som inte är offentligt betrott eller själv signerat, så gör du så här för att installera det:

1. Högerklicka på paketet och välj **Egenskaper**.
2. I fönstret som visas väljer du fliken **digitala signaturer** . Det får bara finnas ett objekt i listan på fliken, som du ser i följande bild. Markera objektet för att markera objektet och välj sedan * * de
3. När fönstret digital signal information visas väljer du fliken **Allmänt** och väljer sedan **Installera certifikat**.
4. När installations programmet öppnas väljer du **lokal dator** som lagrings plats och väljer sedan **Nästa**.
5. Om du tillfrågas om du vill tillåta att appen gör ändringar på enheten väljer du **Ja**.
6. Välj **Placera alla certifikat i följande Arkiv**och välj **Bläddra**.
7. När fönstret Välj certifikat Arkiv visas väljer du **Betrodda personer**och väljer sedan **OK**.
8. Välj **Slutför**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Förbereda PowerShell-skript för MSIX app Attach

MSIX app Attach har fyra distinkta faser som måste utföras i följande ordning:

1. Skede
2. Registrera dig
3. Avregistrera
4. Destage

Varje fas skapar ett PowerShell-skript. Exempel skript för varje fas finns [här](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-the-powershell-script"></a>Mellanlagra PowerShell-skriptet

Innan du uppdaterar PowerShell-skripten ser du till att du har volymens GUID för volymen på den virtuella hård disken. Så här hämtar du volymens GUID:

1.  Öppna nätverks resursen där den virtuella hård disken finns i den virtuella datorn där du kör skriptet.

2.  Högerklicka på den virtuella hård disken och välj **montera**. Detta kommer att montera den virtuella hård disken till en enhets beteckning.

3.  När du har monterat den virtuella hård disken **öppnas fönstret Utforskaren** . Avbilda den överordnade mappen och uppdatera **\$parentFolder** -variabeln

    >[!NOTE]
    >Om du inte ser en överordnad mapp betyder det att MSIX inte expanderades korrekt. Gör om föregående avsnitt och försök igen.

4.  Öppna den överordnade mappen. Om den är korrekt expanderad visas en mapp med samma namn som paketet. Uppdatera variabeln **\$PackageName** så att den matchar namnet på den här mappen.

    Till exempel `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Öppna en kommando tolk och ange **mountvol**. Det här kommandot visar en lista över volymer och deras GUID. Kopiera GUID för volymen där enhets beteckningen matchar den enhet som du monterade din virtuella hård disk till i steg 2.

    Exempel: i det här exemplet på utdata för kommandot mountvol om du har monterat din virtuella hård disk till enhet C, vill du kopiera värdet ovan `C:\`:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Uppdatera variabeln **\$volumeGuid** med volym-GUID som du nyss kopierade.

7. Öppna en admin PowerShell-prompt och uppdatera följande PowerShell-skript med de variabler som gäller för din miljö.

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

    Mount-Diskimage -ImagePath \$vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + \$vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + \$vhdSrc + " has failed!") -BackgroundColor Red

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
    $_.ToString() -eq 'System.Threading.Tasks.Task\`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress\`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the
    pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>Registrera PowerShell-skript

Kör register skriptet genom att köra följande PowerShell-cmdletar med plats hållarnas värden ersatta med värden som gäller för din miljö.

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path \$path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>Avregistrera PowerShell-skript

I det här skriptet ersätter du plats hållaren för **\$PackageName** med namnet på det paket som du testar.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>Destage PowerShell-skript

I det här skriptet ersätter du plats hållaren för **\$PackageName** med namnet på det paket som du testar.

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

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Konfigurera simulerings skript för MSIX-appens kopplings agent

När du har skapat skripten kan användarna köra dem manuellt eller ställa in dem för att köra automatiskt som start, inloggning, utloggning och avstängnings skript. Mer information om dessa typer av skript finns i [använda skript för start, avstängning, inloggning och utloggning i Grupprincip](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)).

Vart och ett av dessa automatiska skript kör en fas i appen bifoga skript:

- Start skriptet kör Stadium skriptet.
- Inloggnings skriptet kör register skriptet.
- Utloggnings skriptet kör Avregistrerings skriptet.
- Avstängnings skriptet kör destage-skriptet.

## <a name="use-packages-offline"></a>Använda paket offline

Om du använder paket från [Microsoft Store för företag](https://businessstore.microsoft.com/) eller [Microsoft Store för utbildning](https://educationstore.microsoft.com/) i nätverket eller på enheter som inte är anslutna till Internet, måste du hämta paket licenser från Microsoft Store och installera dem på enheten för att kunna köra appen. Om enheten är online och kan ansluta till Microsoft Store för företag, bör de licenser som krävs hämtas automatiskt, men om du är offline måste du konfigurera licenserna manuellt. 

Om du vill installera licensfiler måste du använda ett PowerShell-skript som anropar MDM_EnterpriseModernAppManagement_StoreLicenses02_01-klassen i WMI-providern.  

Så här konfigurerar du licenserna för offline-användning: 

1. Ladda ned programpaketet, licenserna och de ramverk som krävs från Microsoft Store för företag. Du behöver både kodade och kodade licensfiler. Detaljerade instruktioner för hämtning hittar du [här](https://docs.microsoft.com/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Uppdatera följande variabler i skriptet för steg 3:
      1. `$contentID` är ContentID-värdet från den kodade licens filen (. xml). Du kan öppna licens filen i valfri text redigerare.
      2. `$licenseBlob` är hela strängen för licens-bloben i den kodade licens filen (. bin). Du kan öppna den kodade licens filen i valfri text redigerare. 
3. Kör följande skript från en admin PowerShell-prompt. En bra plats för att utföra licens installationen är i slutet av det [mellanlagrings skript](#stage-the-powershell-script) som måste köras från en administratörs prompt.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{‘ContentID’_in_unencoded_license_file}"

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

Den här funktionen stöds inte för närvarande, men du kan ställa frågor till communityn på den [virtuella Windows-datorn TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Du kan också lämna feedback för virtuella Windows-datorer i [hubben för Windows Virtual Desktop feedback](https://aka.ms/MRSFeedbackHub)eller lämna feedback för MSIX-appen och packnings verktyget i [MSIX-appen bifoga feedback Hub](https://aka.ms/msixappattachfeedback) och [MSIX paket verktyg feedback Hub](https://aka.ms/msixtoolfeedback).
