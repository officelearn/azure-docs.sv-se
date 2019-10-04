---
title: Azure Disk Encryption exempel skript
description: Den här artikeln är bilagan till Microsoft Azure disk kryptering för virtuella Linux-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 088ca5c20b0681cdd36da1b8a187873399aa32c6
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828463"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption exempel skript 

Den här artikeln innehåller exempel skript för att förbereda förkrypterade virtuella hård diskar och andra uppgifter.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>PowerShell-exempelskript för Azure Disk Encryption 

- **Lista över alla krypterade virtuella datorer i din prenumeration**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Lista över alla disk encryption-hemligheter som används för att kryptera virtuella datorer i ett nyckelvalv** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Använda PowerShell-skriptet Azure Disk Encryption krav
Om du redan är bekant med kraven för Azure Disk Encryption kan du använda den [PowerShell-skript för Azure Disk Encryption krav](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Ett exempel på hur du använder det här PowerShell-skript finns i den [kryptera en virtuell dator-Snabbstart](disk-encryption-powershell-quickstart.md). Du kan ta bort kommentarerna från en del av skriptet, med början på rad 211, att kryptera alla diskar för befintliga virtuella datorer i en befintlig resursgrupp. 

I följande tabell visas vilka parametrar som kan användas i PowerShell-skriptet: 


|Parameter|Beskrivning|Erforderlig?|
|------|------|------|
|$resourceGroupName| Namnet på resursgruppen som Nyckelvalvet tillhör.  En ny resursgrupp med det här namnet kommer att skapas om det inte finns.| True|
|$keyVaultName|Namnet på Nyckelvalvet där krypteringsnycklarna nycklar är placeras. Ett nytt valv med det här namnet kommer att skapas om det inte finns.| True|
|$location|Platsen för Nyckelvalvet. Kontrollera att Nyckelvalvet och virtuella datorer som ska krypteras finns på samma plats. Hämta en innehållsplatslista med `Get-AzLocation`.|True|
|$subscriptionId|Identifierare för Azure-prenumeration som ska användas.  Du kan hämta ditt prenumerations-ID med `Get-AzSubscription`.|True|
|$aadAppName|Namnet på Azure AD-programmet som ska användas för att skriva hemligheter KeyVault. Om det inte redan finns ett program med det namnet skapas ett nytt. Om den här appen finns redan, ange du aadClientSecret parameter till skriptet.|False|
|$aadClientSecret|Klienthemlighet för Azure AD-programmet som du skapade tidigare.|False|
|$keyEncryptionKeyName|Namnet på valfri nyckelkrypteringsnyckel i KeyVault. En ny nyckel med det här namnet kommer att skapas om det inte finns.|False|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Kryptera eller dekryptera virtuella datorer utan en Azure AD-app

- [Aktivera disk kryptering på en befintlig eller virtuell Linux-dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Inaktivera kryptering på en som kör Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Inaktivera kryptering tillåts endast på datavolymer för virtuella Linux-datorer.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Kryptera eller dekryptera virtuella datorer med en Azure AD-app (tidigare version) 
 
- [Aktivera disk kryptering på en befintlig eller virtuell Linux-dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Inaktivera kryptering på en som kör Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Inaktivera kryptering tillåts endast på datavolymer för virtuella Linux-datorer. 


- [Skapa en ny krypterade hanterad disk från en förkrypterade VHD-/ lagringsblob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Skapar en ny krypterade hanterad disk som en förkrypterade virtuell Hårddisk och dess motsvarande krypteringsinställningar





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Kryptera en OS-enhet på en virtuell Linux-dator som körs

### <a name="prerequisites-for-os-disk-encryption"></a>Krav för OS-diskkryptering

* Den virtuella datorn måste använda en distribution som är kompatibel med disk kryptering för operativ system som anges i [Azure Disk Encryption operativ system som stöds](disk-encryption-overview.md#supported-vm-sizes) 
* Den virtuella datorn måste skapas från Marketplace-avbildning i Azure Resource Manager.
* Azure virtuell dator med minst 4 GB RAM (rekommenderas är 7 GB).
* (För RHEL och CentOS) Inaktivera SELinux. Om du vill inaktivera SELinux, se ”4.4.2. Inaktivera SELinux ”i den [SELinux användar- och Administrator's Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) på den virtuella datorn.
* Starta om den virtuella datorn på minst en gång när du har inaktiverat SELinux.

### <a name="steps"></a>Steg
1. Skapa en virtuell dator med någon av de distributioner som angavs tidigare.

   OS-diskkryptering stöds för 7.2 CentOS, via en särskild avbildning. Om du vill använda den här bilden anger du ”7.2n” som SKU: N när du skapar den virtuella datorn:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Konfigurera den virtuella datorn efter dina behov. Om du planerar att kryptera alla (OS + data) enheter finns i dataenheter måste vara angivna och monteras från/etc/fstab.

   > [!NOTE]
   > Använd UUID =... Ange dataenheter för i/etc/fstab istället för att ange blockera enhetens namn (till exempel/dev/sdb1). Ordningen på enheter ändras på den virtuella datorn under krypteringen. Om den virtuella datorn är beroende av en viss ordning av blockenheterna, misslyckas den att montera dem efter kryptering.

3. Logga ut från SSH-sessioner.

4. För att kryptera Operativsystemet, ange volumeType som **alla** eller **OS** när du aktiverar kryptering.

   > [!NOTE]
   > Alla Användarutrymmet processer som inte körs som `systemd` tjänster ska avslutas med en `SIGKILL`. Starta om den virtuella datorn. När du aktiverar OS-diskkryptering på en aktiv virtuell dator kan du planera på stilleståndstid på virtuella datorer.

5. Övervaka förloppet för kryptering med jämna mellanrum med hjälp av anvisningarna i den [nästa avsnitt](#monitoring-os-encryption-progress).

6. När get-AzVmDiskEncryptionStatus visar "VMRestartPending" startar du om den virtuella datorn antingen genom att logga in på den eller med hjälp av portalen, PowerShell eller CLI.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Innan du startar om rekommenderar vi att du sparar [startdiagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) för den virtuella datorn.

## <a name="monitoring-os-encryption-progress"></a>Övervaka förloppet för OS-kryptering
Du kan övervaka förloppet för OS-kryptering på tre sätt:

* Använd den `Get-AzVmDiskEncryptionStatus` cmdlet och inspektera fältet ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  När den virtuella datorn når ”OS diskkryptering igång”, det tar cirka 40 till 50 minuter på en Premium-lagring säkerhetskopieras VM.

  Grund av [utfärda #388](https://github.com/Azure/WALinuxAgent/issues/388) i WALinuxAgent, `OsVolumeEncrypted` och `DataVolumesEncrypted` visas som `Unknown` i vissa distributioner. Med WALinuxAgent version 2.1.5 och senare, det här problemet löses automatiskt. Om du ser `Unknown` utdata och du kan kontrollera status för diskkryptering med hjälp av Azure Resource Explorer.

  Gå till [Azure Resource Explorer](https://resources.azure.com/), och expandera sedan den här hierarkin i panelen för val av vänster:

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

  Rulla ned för att se krypteringsstatus för dina enheter i InstanceView.

  ![Instansvy för virtuell dator](./media/disk-encryption/vm-instanceview.png)

* Titta på [startdiagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Meddelanden från tillägget ADE ska föregås `[AzureDiskEncryption]`.

* Logga in på den virtuella datorn via SSH och hämta tilläggets logg från:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Vi rekommenderar att du inte logga in på den virtuella datorn när OS-kryptering pågår. Kopiera loggarna bara när de två metoderna har misslyckats.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Förbereda en förkrypterad virtuell Linux-hårddisk
Inför förkrypterade virtuella hårddiskar kan variera beroende på vilken distribution. Exempel på förberedelse av Ubuntu 16, openSUSE 13,2 och CentOS 7 är tillgängliga. 

### <a name="ubuntu-16"></a>Ubuntu 16
Konfigurera kryptering under installationen av distributionsplatsen genom att göra följande:

1. Välj **konfigurera krypterade volymer** när du partitionera diskarna.

   ![Ubuntu 16.04 konfigurera – konfigurera krypterade volymer](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Skapa en separat startenheten som inte får vara krypterade. Kryptera din rotenhet.

   ![Ubuntu 16.04-installation - Välj enheter att kryptera](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Ange en lösenfras. Det här är det lösenord som du laddade upp till nyckelvalvet.

   ![Ubuntu 16.04 konfigurera – ange lösenfras](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Slut partitionering.

   ![Ubuntu 16.04 konfigurera – Slutför partitionering](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. När du startar den virtuella datorn och ange en lösenfras, använder du den lösenfras som du angav i steg 3.

   ![Ubuntu 16.04 konfigurera – ange lösenfras vid start](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Förbereda den virtuella datorn för att ladda upp till Azure med hjälp av [instruktionerna](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

Konfigurera krypteringen ska fungera med Azure genom att göra följande:

1. Skapa en fil under /usr/local/sbin/azure_crypt_key.sh, med innehållet i skriptet nedan. Uppmärksam på KeyFileName, eftersom det är namnet på lösenfras-filen som används av Azure.

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

2. Ändra crypt-konfigurationen i */etc/crypttab*. Det bör se ut så här:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Lägg till körrättigheter i skriptet:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Redigera */etc/initramfs-tools/modules* genom att lägga till rader:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Kör `update-initramfs -u -k all` att uppdatera initramfs att göra den `keyscript` träder i kraft.

7. Nu kan du avetablera den virtuella datorn.

   ![Ubuntu 16.04-installation - update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Fortsätt till nästa steg och överföra en virtuell Hårddisk till Azure.

### <a name="opensuse-132"></a>openSUSE 13.2
För att konfigurera kryptering under installationen av distributionsplatsen, gör du följande:
1. När du partitionera diskarna väljer **kryptera volymen grupp**, och sedan ange ett lösenord. Det här är det lösenord som du överföra till ditt nyckelvalv.

   ![openSUSE 13.2-installation - krypterar volym-grupp](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Starta den virtuella datorn med ditt lösenord.

   ![openSUSE 13.2 konfigurera – ange lösenfras vid start](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Förbereda den virtuella datorn för att ladda upp till Azure genom att följa instruktionerna i [Förbered en SLES- eller openSUSE-dator för Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

För att konfigurera krypteringen ska fungera med Azure, gör du följande:
1. Redigera /etc/dracut.conf och Lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Kommentera ut följande rader i slutet av filen /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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
4. Redigera /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och lägger till dem i ”# öppna LUKS enhet”:

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
5. Kör `/usr/sbin/dracut -f -v` att uppdatera initrd.

6. Nu kan du avetablera den virtuella datorn och överföra en virtuell Hårddisk till Azure.

### <a name="centos-7"></a>CentOS 7
För att konfigurera kryptering under installationen av distributionsplatsen, gör du följande:
1. Välj **kryptera Mina data** när du partitionera diskar.

   ![CentOS 7 konfigurera - Installation-mål](./media/disk-encryption/centos-encrypt-fig1.png)

2. Se till att **Encrypt** har valts för rotpartitionen.

   ![CentOS 7 konfigurera - Välj kryptera för rotpartitionen](./media/disk-encryption/centos-encrypt-fig2.png)

3. Ange en lösenfras. Det här är det lösenord som du överföra till ditt nyckelvalv.

   ![CentOS 7-installation - ange lösenfras](./media/disk-encryption/centos-encrypt-fig3.png)

4. När du startar den virtuella datorn och ange en lösenfras, använder du den lösenfras som du angav i steg 3.

   ![CentOS 7 konfigurera – ange lösenfras på Start](./media/disk-encryption/centos-encrypt-fig4.png)

5. Förbereda den virtuella datorn för att ladda upp till Azure med hjälp av anvisningarna i ”CentOS 7.0 +” [Förbered en CentOS-baserad virtuell dator för Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

6. Nu kan du avetablera den virtuella datorn och överföra en virtuell Hårddisk till Azure.

För att konfigurera krypteringen ska fungera med Azure, gör du följande:

1. Redigera /etc/dracut.conf och Lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Kommentera ut följande rader i slutet av filen /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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
4. Redigera /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och Lägg till nedanstående efter ”# öppna LUKS enhet”:
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
5. Kör den ”/ usr/sbin/dracut - f - v” att uppdatera initrd.

    ![CentOS 7-installation - kör /usr/sbin/dracut -f - v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Ladda upp krypterad virtuell hård disk till ett Azure Storage-konto
När DM-crypt-kryptering har Aktiver ATS måste den lokala krypterade virtuella hård disken överföras till ditt lagrings konto.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Överför hemligheten för den förkrypterade virtuella datorn till ditt nyckel valv
När du krypterar med hjälp av Azure AD-app (tidigare version), måste diskkryptering hemligheten som du fick tidigare laddas upp som en hemlighet i nyckelvalvet. Nyckelvalvet måste ha diskkryptering och behörigheter som har aktiverats för din Azure AD-klient.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Disk krypterings hemligheten är inte krypterad med en KEK
Använd [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret)för att ställa in hemligheten i nyckel valvet. Lösen frasen kodas som en Base64-sträng och överförs sedan till nyckel valvet. Kontrollera dessutom att följande taggar är inställda när du skapar hemligheten i nyckelvalvet.

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


Använd den `$secretUrl` i nästa steg för [koppla OS-disk utan att använda KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Disk krypterings hemlighet krypterad med en KEK
Innan du laddar upp hemligheten till nyckelvalvet kryptera du om du vill det med hjälp av en nyckelkrypteringsnyckel. Använd radbyte [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) att kryptera hemligheten med den viktiga krypteringsnyckeln först. Utdata från åtgärden radbyte är en base64-URL-kodad sträng som du kan sedan överföra som en hemlighet med hjälp av den [ `Set-AzKeyVaultSecret` ](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet.

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

Använd `$KeyEncryptionKey` och `$secretUrl` i nästa steg för [koppla OS-disken med hjälp av KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Ange en hemlig URL när du ansluter en OS-disk

###  <a name="without-using-a-kek"></a>Utan att använda en KEK
När du kopplar OS-disken, måste du skicka `$secretUrl`. URL: en har genererats i avsnittet ”diskkryptering hemlighet som inte är krypterade med en KEK”.
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
När du ansluter OS-disken, skicka `$KeyEncryptionKey` och `$secretUrl`. URL: en har genererats i avsnittet ”diskkrypteringshemlighet krypterade med en KEK”.
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
