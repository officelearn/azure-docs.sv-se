---
title: Exempelskript för Azure Disk Encryption
description: Den här artikeln är bilagan till virtuella virtuella linux-datorer för Microsoft Azure Disk Encryption for Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: c98da4b41da183f56d80fad1e8c01706d1cfcf23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970515"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Exempelskript för Azure Disk Encryption 

Den här artikeln innehåller exempelskript för att förbereda förkrypterade virtuella hårddiskar och andra uppgifter.

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Exempel på PowerShell-skript för Azure Disk Encryption 

- **Lista alla krypterade virtuella datorer i din prenumeration**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Lista alla diskkrypteringshemligheter som används för att kryptera virtuella datorer i ett nyckelvalv** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Använda Azure Disk Encryption förutsättningar PowerShell-skript
Om du redan är bekant med förutsättningarna för Azure Disk Encryption kan du använda [Azure Disk Encryption förutsättningar PowerShell-skriptet](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Ett exempel på hur du använder det här PowerShell-skriptet finns i [Snabbstarten kryptera en virtuell dator](disk-encryption-powershell-quickstart.md). Du kan ta bort kommentarerna från ett avsnitt i skriptet, med början på rad 211, för att kryptera alla diskar för befintliga virtuella datorer i en befintlig resursgrupp. 

I följande tabell visas vilka parametrar som kan användas i PowerShell-skriptet: 


|Parameter|Beskrivning|Obligatoriska?|
|------|------|------|
|$resourceGroupName| Namnet på den resursgrupp som KeyVault tillhör.  En ny resursgrupp med det här namnet skapas om det inte finns någon.| True|
|$keyVaultName|Namn på keyvault där krypteringsnycklar ska placeras. Ett nytt valv med det här namnet skapas om det inte finns något nytt valv.| True|
|$location|Plats för KeyVault. Kontrollera att KeyVault och virtuella datorer som ska krypteras finns på samma plats. Hämta en innehållsplatslista med `Get-AzLocation`.|True|
|$subscriptionId|Identifierare för Azure-prenumerationen som ska användas.  Du kan hämta ditt prenumerations-ID med `Get-AzSubscription`.|True|
|$aadAppName|Namnet på Azure AD-programmet som ska användas för att skriva hemligheter till KeyVault. Om det inte redan finns ett program med det namnet skapas ett nytt. Om den här appen redan finns skickar du parametern aadClientSecret till skriptet.|False|
|$aadClientSecret|Klienthemligheten för Azure AD-programmet som skapades tidigare.|False|
|$keyEncryptionKeyName|Namn på valfri nyckelkrypteringsnyckel i KeyVault. En ny nyckel med det här namnet skapas om det inte finns någon.|False|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Kryptera eller dekryptera virtuella datorer utan en Azure AD-app

- [Aktivera diskkryptering på en befintlig eller körande Linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Inaktivera kryptering på en virtuell Linux-dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Inaktivera kryptering är endast tillåtet på datavolymer för virtuella Linux-datorer.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Kryptera eller dekryptera virtuella datorer med en Azure AD-app (tidigare version) 
 
- [Aktivera diskkryptering på en befintlig eller körande Linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Inaktivera kryptering på en virtuell Linux-dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Inaktivera kryptering är endast tillåtet på datavolymer för virtuella Linux-datorer. 


- [Skapa en ny krypterad hanterad disk från en förkrypterad VHD/storage-blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Skapar en ny krypterad hanterad disk som en förkrypterad virtuell hårddisk och motsvarande krypteringsinställningar





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Kryptera en OS-enhet på en virtuell Linux-dator som körs

### <a name="prerequisites-for-os-disk-encryption"></a>Förutsättningar för OS-diskkryptering

* Den virtuella datorn måste använda en distribution som är kompatibel med OS-diskkryptering enligt listan i [operativsystemen för Azure Disk Encryption-stödda](disk-encryption-overview.md#supported-vm-sizes) 
* Den virtuella datorn måste skapas från Marketplace-avbildningen i Azure Resource Manager.
* Azure VM med minst 4 GB RAM (rekommenderad storlek är 7 GB).
* (För RHEL och CentOS) Inaktivera SELinux. Om du vill inaktivera SELinux finns i "4.4.2. Inaktivera SELinux" i [SELinux-användar- och administratörshandboken](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) på den virtuella datorn.
* När du har inaktiverat SELinux startar du om den virtuella datorn minst en gång.

### <a name="steps"></a>Steg
1. Skapa en virtuell dator med hjälp av någon av de distributioner som angetts tidigare.

   För CentOS 7.2 stöds OS-diskkryptering via en speciell avbildning. Om du vill använda den här avbildningen anger du "7.2n" som SKU när du skapar den virtuella datorn:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Konfigurera den virtuella datorn efter dina behov. Om du ska kryptera alla (OS + data) enheter, dataenheter måste anges och monteras från / etc / fstab.

   > [!NOTE]
   > Använd UUID=... för att ange dataenheter i /etc/fstab i stället för att ange namnet på blockenheten (till exempel /dev/sdb1). Under kryptering ändras ordningen på enheter på den virtuella datorn. Om den virtuella datorn är beroende av en viss ordning på blockeranordningar kan den inte montera dem efter kryptering.

3. Logga ut från SSH-sessionerna.

4. Om du vill kryptera operativsystemet anger du volumeType som **alla** eller **operativsystemet** när du aktiverar kryptering.

   > [!NOTE]
   > Alla processer för användarutrymme som `systemd` inte körs som `SIGKILL`tjänster ska dödas med en . Starta om den virtuella datorn. När du aktiverar OS-diskkryptering på en virtuell dator som körs planerar du driftstopp för virtuella datorer.

5. Övervaka regelbundet krypteringens förlopp med hjälp av instruktionerna i [nästa avsnitt](#monitoring-os-encryption-progress).

6. När Get-AzVmDiskEncryptionStatus visar "VMRestartPending" startar du om den virtuella datorn antingen genom att logga in på den eller genom att använda portalen, PowerShell eller CLI.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Innan du startar om rekommenderar vi att du sparar [startdiagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) för den virtuella datorn.

## <a name="monitoring-os-encryption-progress"></a>Övervaka os-krypteringsstatus
Du kan övervaka os-krypteringsstatus på tre sätt:

* Använd `Get-AzVmDiskEncryptionStatus` cmdleten och inspektera fältet ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  När den virtuella datorn når "OS-diskkryptering startad" tar det cirka 40 till 50 minuter på en Premium-lagringsbaserad virtuell dator.

  På grund av [utfärdande #388](https://github.com/Azure/WALinuxAgent/issues/388) i `OsVolumeEncrypted` WALinuxAgent, och `DataVolumesEncrypted` visas som `Unknown` i vissa distributioner. Med WALinuxAgent version 2.1.5 och senare åtgärdas det här problemet automatiskt. Om du `Unknown` ser i utdata kan du verifiera diskkrypteringsstatus med hjälp av Azure Resource Explorer.

  Gå till [Azure Resource Explorer](https://resources.azure.com/)och expandera sedan den här hierarkin i urvalspanelen till vänster:

  ~~~~
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ~~~~                

  Bläddra nedåt i InstanceView för att se krypteringsstatus för dina enheter.

  ![Vyn VM-instans](./media/disk-encryption/vm-instanceview.png)

* Titta på [boot diagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Meddelanden från ADE-tillägget ska föregås `[AzureDiskEncryption]`av .

* Logga in på den virtuella datorn via SSH och hämta tilläggsloggen från:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Vi rekommenderar att du inte loggar in på den virtuella datorn medan OS-kryptering pågår. Kopiera loggarna endast när de andra två metoderna har misslyckats.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Förbereda en förkrypterad Virtuell Linux-hårddisk
Förberedelserna för förkrypterade virtuella hårddiskar kan variera beroende på distributionen. Exempel på att förbereda Ubuntu 16, openSUSE 13.2 och CentOS 7 finns tillgängliga. 

### <a name="ubuntu-16"></a>Ubuntu 16 (På andra sätt)
Konfigurera kryptering under distributionsinstallationen genom att göra följande steg:

1. Välj **Konfigurera krypterade volymer** när du partitionerar diskarna.

   ![Installation av Ubuntu 16.04 – Konfigurera krypterade volymer](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Skapa en separat startenhet som inte får krypteras. Kryptera din rotenhet.

   ![Installation av Ubuntu 16.04 - Välj enheter att kryptera](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Ange en lösenfras. Det här är lösenfrasen som du laddade upp till nyckelvalvet.

   ![Installation av Ubuntu 16.04 – tillhandahålla lösenfras](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Slutför partitioneringen.

   ![Installation av Ubuntu 16.04 - Slutför partitionering](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. När du startar den virtuella datorn och blir tillfrågad om en lösenfras använder du lösenfrasen som du angav i steg 3.

   ![Installation av Ubuntu 16.04 - Ange lösenfras vid uppstart](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Förbered den virtuella datorn för överföring till Azure med hjälp av [dessa instruktioner](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

Konfigurera kryptering så att den fungerar med Azure genom att göra följande steg:

1. Skapa en fil under /usr/local/sbin/azure_crypt_key.sh, med innehållet i följande skript. Var uppmärksam på KeyFileName, eftersom det är lösenfrasfilnamnet som används av Azure.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Ändra kryptan config i */ etc / crypttab*. Det bör se ut så här:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Lägg till körbara behörigheter i skriptet:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Redigera */etc/initramfs-verktyg/moduler* genom att lägga till rader:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Kör `update-initramfs -u -k all` för att uppdatera initramfsen så att effekten `keyscript` börjar gälla.

7. Nu kan du avetablera den virtuella datorn.

   ![Ubuntu 16.04 Installation – uppdatering av initramferna](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Fortsätt till nästa steg och ladda upp din virtuella hårddisk till Azure.

### <a name="opensuse-132"></a>openSUSE 13.2
Så här konfigurerar du kryptering under distributionsinstallationen:
1. När du partitionerar diskarna väljer du **Kryptera volymgrupp**och anger sedan ett lösenord. Det här är lösenordet som du laddar upp till nyckelvalvet.

   ![openSUSE 13.2 Setup - Kryptera volymgrupp](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Starta den virtuella datorn med ditt lösenord.

   ![openSUSE 13.2 Setup - Ge lösenfras vid uppstart](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Förbered den virtuella datorn för överföring till Azure genom att följa instruktionerna i [Förbereda en SLES- eller openSUSE-virtuell dator för Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

Så här konfigurerar du kryptering för att fungera med Azure:
1. Redigera /etc/dracut.conf och lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Kommentera ut dessa rader i slutet av filen / usr / lib / dracut /modules.d/90crypt/module-setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Lägg till följande rad i början av filen /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   Och ändra alla förekomster av:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   till:
   ```bash
    if [ 1 ]; then
   ```
4. Redigera /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och bifoga den till "# Öppna LUKS-enhet":

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Kör `/usr/sbin/dracut -f -v` för att uppdatera initrd.

6. Nu kan du avetablera den virtuella datorn och ladda upp din virtuella hårddisk till Azure.

### <a name="centos-7-and-rhel-81"></a>CentOS 7 och RHEL 8,1

Så här konfigurerar du kryptering under distributionsinstallationen:
1. Välj **Kryptera mina data** när du partitionerar diskar.

   ![CentOS 7 Setup -Installationsmål](./media/disk-encryption/centos-encrypt-fig1.png)

2. Kontrollera att **Kryptera** är markerat för rotpartition.

   ![CentOS 7 Setup -Välj kryptera för rotpartition](./media/disk-encryption/centos-encrypt-fig2.png)

3. Ange en lösenfras. Det här är lösenfrasen som du laddar upp till nyckelvalvet.

   ![CentOS 7 Setup - ge lösenfras](./media/disk-encryption/centos-encrypt-fig3.png)

4. När du startar den virtuella datorn och blir tillfrågad om en lösenfras använder du lösenfrasen som du angav i steg 3.

   ![CentOS 7 Setup - Ange lösenfras vid uppstart](./media/disk-encryption/centos-encrypt-fig4.png)

5. Förbered den virtuella datorn för överföring till Azure med hjälp av "CentOS 7.0+"-instruktionerna i [Förbereda en CentOS-baserad virtuell dator för Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

6. Nu kan du avetablera den virtuella datorn och ladda upp din virtuella hårddisk till Azure.

Så här konfigurerar du kryptering för att fungera med Azure:

1. Redigera /etc/dracut.conf och lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Kommentera ut dessa rader i slutet av filen / usr / lib / dracut /modules.d/90crypt/module-setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Lägg till följande rad i början av filen /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   Och ändra alla förekomster av:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   till
   ```bash
    if [ 1 ]; then
   ```
4. Redigera /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och bifoga följande efter "# Open LUKS device":
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Kör "/usr/sbin/dracut -f -v" för att uppdatera initrd.

    ![CentOS 7 Setup - kör /usr/sbin/dracut -f -v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Ladda upp krypterad virtuell hårddisk till ett Azure-lagringskonto
När DM-Crypt-kryptering har aktiverats måste den lokala krypterade virtuella hårddisken laddas upp till ditt lagringskonto.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Ladda upp hemligheten för den förkrypterade virtuella datorn till ditt nyckelvalv
När du krypterar med en Azure AD-app (tidigare version) måste diskkrypteringshemligheten som du tidigare fått överföras som en hemlighet i nyckelvalvet. Nyckelvalvet måste ha diskkryptering och behörigheter aktiverade för din Azure AD-klient.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Diskkrypteringshemlighet inte krypterad med en KEK
Om du vill konfigurera hemligheten i nyckelvalvet använder du [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Lösenfrasen kodas som en base64-sträng och överförs sedan till nyckelvalvet. Kontrollera dessutom att följande taggar ställs in när du skapar hemligheten i nyckelvalvet.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Använd `$secretUrl` i nästa steg för [att ansluta OS-disken utan att använda KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Diskkrypteringshemlighet krypterad med en KEK
Innan du laddar upp hemligheten till nyckelvalvet kan du kryptera den med hjälp av en nyckelkrypteringsnyckel. Använd wrap [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) för att först kryptera hemligheten med hjälp av nyckelkrypteringsnyckeln. Utdata för den här figursättningen är en base64 URL-kodad sträng, [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) som du sedan kan ladda upp som en hemlighet med hjälp av cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Använd `$KeyEncryptionKey` `$secretUrl` och i nästa steg för [att koppla OS-disken med KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Ange en hemlig URL när du bifogar en OS-disk

###  <a name="without-using-a-kek"></a>Utan att använda en KEK
När du ansluter OS-disken måste du `$secretUrl`passera . WEBBADRESSEN genererades i avsnittet "Diskkrypteringshemlighet som inte är krypterad med en KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Använda en KEK
När du ansluter OS-disken passerar du `$KeyEncryptionKey` och `$secretUrl`. WEBBADRESSEN genererades i avsnittet "Diskkrypteringshemlig krypterad med en KEK".The URL was generated in the "Disk encryption secret encrypted with a KEK" section.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
