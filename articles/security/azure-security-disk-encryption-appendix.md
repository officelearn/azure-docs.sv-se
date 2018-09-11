---
title: Azure Disk Encryption för Windows och Linux IaaS-datorer | Microsoft Docs
description: Den här artikeln är tillägget för Microsoft Azure Disk Encryption för Windows och Linux IaaS-datorer.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 09/10/2018
ms.openlocfilehash: 2f932ff39495916c4a9fb55714c73383e06c72e1
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44346851"
---
# <a name="appendix-for-azure-disk-encryption"></a>Tillägg för Azure Disk Encryption 
Den här artikeln är ett tillägg till [Azure Disk Encryption för virtuella IaaS-datorer](azure-security-disk-encryption-overview.md). Se till att läsa Azure Disk Encryption för virtuella IaaS-datorer artiklar först för att förstå kontexten. Den här artikeln beskriver hur du förbereder förkrypterade virtuella hårddiskar och andra uppgifter.

## <a name="connect-to-your-subscription"></a>Ansluta till din prenumeration
Innan du börjar bör du granska den [krav](azure-security-disk-encryption-prerequisites.md) artikeln. När alla krav har uppfyllts kan du ansluta till din prenumeration genom att köra följande cmdlets:

### <a name="bkmk_ConnectPSH"></a> Ansluta till din prenumeration med PowerShell

1. Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:

     ```powershell
     Connect-AzureRmAccount 
     ```
2. Om du har flera prenumerationer och vill ange jag ska använda, skriver du följande för att visa prenumerationerna för ditt konto:
     
     ```powershell
     Get-AzureRmSubscription
     ```
3. Om du vill ange den prenumeration som du vill använda, skriver du:
 
     ```powershell
      Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. Om du vill kontrollera att den prenumeration som har konfigurerats är korrekt, skriver du:
     
     ```powershell
     Get-AzureRmSubscription
     ```
5. Om det behövs kan ansluta till Azure AD med [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```
6. För att bekräfta Azure Disk Encryption-cmdletar har installerats, skriver du:
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. Granska [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps) och [AzureAD](/powershell/module/azuread), om det behövs.

### <a name="bkmk_ConnectCLI"></a> Ansluta till din prenumeration med Azure CLI

1. Logga in på Azure med [az-inloggning](/cli/azure/authenticate-azure-cli#interactive-log-in). 
     
     ```azurecli
     az login
     ```

2. Om du vill välja en klient att logga in under Använd:
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. Om du har flera prenumerationer och vill ange en kan hämta din prenumerationslista med [az kontolista](/cli/azure/account#az-account-list) och ange med [az-kontogrupper](/cli/azure/account#az-account-set).
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Kontrollera den installerade versionen.
     
     ```azurecli
        az --version
     ``` 

5. Granska [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) om det behövs. 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>PowerShell-exempelskript för Azure Disk Encryption 

- **Lista över alla krypterade virtuella datorer i din prenumeration**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzureRmVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Lista över alla disk encryption-hemligheter som används för att kryptera virtuella datorer i ett nyckelvalv** 

     ```azurepowershell-interactive
     Get-AzureKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="bkmk_prereq-script"></a> Med hjälp av PowerShell-skript för Azure Disk Encryption krav
Om du redan är bekant med kraven för Azure Disk Encryption kan du använda den [PowerShell-skript för Azure Disk Encryption krav](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Ett exempel på hur du använder det här PowerShell-skript finns i den [kryptera en virtuell dator-Snabbstart](quick-encrypt-vm-powershell.md). Du kan ta bort kommentarerna från en del av skriptet, med början på rad 211, att kryptera alla diskar för befintliga virtuella datorer i en befintlig resursgrupp. 

I följande tabell visas vilka parametrar som kan användas i PowerShell-skriptet: 


|Parameter|Beskrivning|Är obligatoriskt|
|------|------|------|
|$resourceGroupName| Namnet på resursgruppen som Nyckelvalvet tillhör.  En ny resursgrupp med det här namnet kommer att skapas om det inte finns.| True|
|$keyVaultName|Namnet på Nyckelvalvet där krypteringsnycklarna nycklar är placeras. Ett nytt valv med det här namnet kommer att skapas om det inte finns.| True|
|$location|Platsen för Nyckelvalvet. Kontrollera att Nyckelvalvet och virtuella datorer som ska krypteras finns på samma plats. Hämta en innehållsplatslista med `Get-AzureRMLocation`.|True|
|$subscriptionId|Identifierare för Azure-prenumeration som ska användas.  Du kan hämta ditt prenumerations-ID med `Get-AzureRMSubscription`.|True|
|$aadAppName|Namnet på Azure AD-programmet som ska användas för att skriva hemligheter KeyVault. Om det inte redan finns ett program med det namnet skapas ett nytt. Om den här appen finns redan, ange du aadClientSecret parameter till skriptet.|False|
|$aadClientSecret|Klienthemlighet för Azure AD-programmet som du skapade tidigare.|False|
|$keyEncryptionKeyName|Namnet på valfri nyckelkrypteringsnyckel i KeyVault. En ny nyckel med det här namnet kommer att skapas om det inte finns.|False|


## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

<!--   - [Create a key vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) -->

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Kryptera eller dekryptera virtuella datorer utan en Azure AD-app


- [Aktivera diskkryptering på befintliga eller som kör Windows virtuella IaaS-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)
- [Inaktivera diskkryptering på befintliga eller som kör Windows virtuella IaaS-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
- [Aktivera diskkryptering på en befintlig eller körs IaaS Linux virtuell dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
 -  [Inaktivera kryptering på en som kör Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Inaktivera kryptering tillåts endast på datavolymer för virtuella Linux-datorer.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Kryptera eller dekryptera virtuella datorer med en Azure AD-app (tidigare version) 
 
- [Aktivera diskkryptering på befintliga eller som kör Windows virtuella IaaS-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [Aktivera diskkryptering på en befintlig eller körs IaaS Linux virtuell dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    

- [Inaktivera diskkryptering på som kör Windows IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [Inaktivera kryptering på en som kör Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Inaktivera kryptering tillåts endast på datavolymer för virtuella Linux-datorer. 

- [Aktivera diskkryptering på nya virtuella IaaS Windows-dator från Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - Den här mallen skapar en ny krypterade Windows virtuell dator som använder Windows Server 2012 galleriet bilden.

- [Skapa en ny krypterade Windows IaaS Managed Disk virtuell dator från galleriet bilden](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - Den här mallen skapar en ny krypterade Windows virtuell dator med hanterade diskar som använder galleriavbildning för Windows Server 2012.

- [Distribution av RHEL 7.2 med fullständig diskkryptering med hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)
    - Den här mallen skapar en fullständigt krypterade RHEL 7.2 VM i Azure med hanterade diskar. Den innehåller en krypterad 30 GB OS-enhet och en krypterad 200 GB matris (RAID-0) monterad på /mnt/raidencrypted. Se den [vanliga frågor och svar](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) artikeln för Linux-server-distributioner som stöds. 

- [Distribution av RHEL 7.2 med fullständig diskkryptering med ohanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel-unmanaged)
    - Den här mallen skapar en fullständigt krypterade RHEL 7.2 VM i Azure med en krypterad 30 GB OS-enhet och en krypterad 200 GB-matris (RAID-0) som är monterad på /mnt/raidencrypted. Se den [vanliga frågor och svar](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) artikeln för Linux-server-distributioner som stöds. 

- [Aktivera diskkryptering på en förkrypterade VHD för Windows eller Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)

- [Skapa en ny krypterade hanterad disk från en förkrypterade VHD-/ lagringsblob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Skapar en ny krypterade hanterad disk som en förkrypterade virtuell Hårddisk och dess motsvarande krypteringsinställningar

- [Aktivera diskkryptering på en Windows virtuell dator som körs med hjälp av en Azure AD Klientcertifikatets tumavtryck](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-aad-client-cert)
    
- [Aktivera diskkryptering på en som kör Linux VM-skalningsuppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Aktivera diskkryptering på en aktiv Windows VM-skalningsuppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

 - [Distribuera en virtuell dator skala ange av virtuella Linux-datorer med en jumpbox och aktiverar kryptering på Linux VMSS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

 - [Distribuera en virtuell dator skala ange av Windows virtuella datorer med en jumpbox och aktiverar kryptering på Windows VMSS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Inaktivera diskkryptering på en som kör Linux VM-skalningsuppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Inaktivera diskkryptering på en aktiv Windows VM-skalningsuppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

## <a name="bkmk_preWin"></a> Förbered en förkrypterade Windows virtuell Hårddisk
Avsnitten som följer är nödvändiga för att förbereda en förkrypterade Windows virtuell Hårddisk för distribution som en krypterad virtuell Hårddisk i Azure IaaS. Använd informationen för att förbereda och starta en ny Windows VM (VHD) på Azure Site Recovery eller Azure. Mer information om hur du förbereder och ladda upp en virtuell Hårddisk finns i [överföra en generaliserad virtuell Hårddisk och använda den för att skapa nya virtuella datorer i Azure](../virtual-machines/windows/upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Uppdatera grupprincipen för att tillåta utan TPM för OS-skydd
Konfigurera grupprincipinställningen BitLocker **BitLocker-diskkryptering**, som du hittar **lokal datorprincip** > **Datorkonfiguration**  >  **Administrationsmallar** > **Windows-komponenter**. Ändra inställningen till **operativsystemsenheter** > **kräver ytterligare autentisering vid start** > **Tillåt BitLocker utan en kompatibel TPM**, enligt följande bild:

![Microsoft Antimalware i Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Installera komponenter för BitLocker-funktion
För Windows Server 2012 och senare, använder du följande kommando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

För Windows Server 2008 R2, använder du följande kommando:

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Förbereda operativsystemvolymen för BitLocker med hjälp av `bdehdcfg`
Om du vill komprimera OS-partition och förbereda datorn för BitLocker, köra den [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment#using-bitlocker-to-encrypt-volumescommand) om det behövs:

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>Skydda systemvolymen med hjälp av BitLocker
Använd den [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) kommando för att aktivera kryptering på startvolymen med hjälp av en extern nyckelskydd. Placera även den externa nyckeln (.bek-fil) på extern enhet eller volym. Kryptering är aktiverat på system-/ startvolymen efter nästa omstart.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Förbered den virtuella datorn med en separat data/resurs VHD för att hämta den externa nyckeln med hjälp av BitLocker.

## <a name="bkmk_LinuxRunning"></a> Kryptera en OS-enhet på en som kör Linux VM

### <a name="prerequisites-for-os-disk-encryption"></a>Krav för OS-diskkryptering

* Den virtuella datorn måste använda en distributionsplats som är kompatibel med OS-diskkryptering som anges i den [Azure Disk Encryption vanliga frågor och svar](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 
* Den virtuella datorn måste skapas från Marketplace-avbildning i Azure Resource Manager.
* Azure virtuell dator med minst 4 GB RAM (rekommenderas är 7 GB).
* (För RHEL och CentOS) Inaktivera SELinux. Om du vill inaktivera SELinux, se ”4.4.2. Inaktivera SELinux ”i den [SELinux användar- och Administrator's Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) på den virtuella datorn.
* Starta om den virtuella datorn på minst en gång när du har inaktiverat SELinux.

### <a name="steps"></a>Steg
1. Skapa en virtuell dator med någon av de distributioner som angavs tidigare.

 OS-diskkryptering stöds för 7.2 CentOS, via en särskild avbildning. Om du vill använda den här bilden anger du ”7.2n” som SKU: N när du skapar den virtuella datorn:

 ```powershell
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Konfigurera den virtuella datorn efter dina behov. Om du planerar att kryptera alla (OS + data) enheter finns i dataenheter måste vara angivna och monteras från/etc/fstab.

 > [!NOTE]
 > Använd UUID =... Ange dataenheter för i/etc/fstab istället för att ange blockera enhetens namn (till exempel/dev/sdb1). Ordningen på enheter ändras på den virtuella datorn under krypteringen. Om den virtuella datorn är beroende av en viss ordning av blockenheterna, misslyckas den att montera dem efter kryptering.

3. Logga ut från SSH-sessioner.

4. För att kryptera Operativsystemet, ange volumeType som **alla** eller **OS** när du aktiverar kryptering.

 > [!NOTE]
 > Alla Användarutrymmet processer som inte körs som `systemd` tjänster ska avslutas med en `SIGKILL`. Starta om den virtuella datorn. När du aktiverar OS-diskkryptering på en aktiv virtuell dator kan du planera på stilleståndstid på virtuella datorer.

5. Övervaka förloppet för kryptering med jämna mellanrum med hjälp av anvisningarna i den [nästa avsnitt](#monitoring-os-encryption-progress).

6. När Get-AzureRmVmDiskEncryptionStatus visar ”VMRestartPending”, startar du om den virtuella datorn genom att logga in till den eller med hjälp av portalen, PowerShell eller CLI.
    ```powershell
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Innan du startar om rekommenderar vi att du sparar [startdiagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) för den virtuella datorn.

## <a name="monitoring-os-encryption-progress"></a>Övervaka förloppet för OS-kryptering
Du kan övervaka förloppet för OS-kryptering på tre sätt:

* Använd den `Get-AzureRmVmDiskEncryptionStatus` cmdlet och inspektera fältet ProgressMessage:
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

 ![Instansvy för virtuell dator](./media/azure-security-disk-encryption/vm-instanceview.png)

* Titta på [startdiagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Meddelanden från tillägget ADE ska föregås `[AzureDiskEncryption]`.

* Logga in på den virtuella datorn via SSH och hämta tilläggets logg från:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Vi rekommenderar att du inte logga in på den virtuella datorn när OS-kryptering pågår. Kopiera loggarna bara när de två metoderna har misslyckats.

## <a name="bkmk_preLinux"></a> Förbereda en förkrypterade Linux-VHD
Inför förkrypterade virtuella hårddiskar kan variera beroende på vilken distribution. Exempel på förbereda [Ubuntu 16](#bkmk_Ubuntu), [openSUSE 13.2](#bkmk_openSUSE), och [CentOS 7](#bkmk_CentOS) är tillgängliga. 

### <a name="bkmk_Ubuntu"></a> Ubuntu 16
Konfigurera kryptering under installationen av distributionsplatsen genom att göra följande:

1. Välj **konfigurera krypterade volymer** när du partitionera diskarna.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Skapa en separat startenheten som inte får vara krypterade. Kryptera din rotenhet.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Ange en lösenfras. Det här är det lösenord som du laddade upp till nyckelvalvet.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Slut partitionering.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. När du startar den virtuella datorn och ange en lösenfras, använder du den lösenfras som du angav i steg 3.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Förbereda den virtuella datorn för att ladda upp till Azure med hjälp av [instruktionerna](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

Konfigurera krypteringen ska fungera med Azure genom att göra följande:

1. Skapa en fil under /usr/local/sbin/azure_crypt_key.sh, med innehållet i skriptet nedan. Uppmärksam på KeyFileName, eftersom det är namnet på lösenfras-filen som används av Azure.

    ```
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

3. Om du redigerar *azure_crypt_key.sh* i Windows och du har kopierat det till Linux, kör `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

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

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Fortsätt till nästa steg och överföra en virtuell Hårddisk till Azure.

### <a name="bkmk_openSUSE"></a>  openSUSE 13.2
För att konfigurera kryptering under installationen av distributionsplatsen, gör du följande:
1. När du partitionera diskarna väljer **kryptera volymen grupp**, och sedan ange ett lösenord. Det här är det lösenord som du överföra till ditt nyckelvalv.

 ![Konfigurera openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Starta den virtuella datorn med ditt lösenord.

 ![Konfigurera openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Förbereda den virtuella datorn för att ladda upp till Azure genom att följa instruktionerna i [Förbered en SLES- eller openSUSE-dator för Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

För att konfigurera krypteringen ska fungera med Azure, gör du följande:
1. Redigera /etc/dracut.conf och Lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Kommentera ut följande rader i slutet av filen /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
 ```
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
 ```
    DRACUT_SYSTEMD=0
 ```
Och ändra alla förekomster av:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
till:
```
    if [ 1 ]; then
```
4. Redigera /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och lägger till dem i ”# öppna LUKS enhet”:

    ```
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

### <a name="bkmk_CentOS"></a> CentOS 7
För att konfigurera kryptering under installationen av distributionsplatsen, gör du följande:
1. Välj **kryptera Mina data** när du partitionera diskar.

 ![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Se till att **Encrypt** har valts för rotpartitionen.

 ![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Ange en lösenfras. Det här är det lösenord som du överföra till ditt nyckelvalv.

 ![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. När du startar den virtuella datorn och ange en lösenfras, använder du den lösenfras som du angav i steg 3.

 ![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Förbereda den virtuella datorn för att ladda upp till Azure med hjälp av anvisningarna i ”CentOS 7.0 +” [Förbered en CentOS-baserad virtuell dator för Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

6. Nu kan du avetablera den virtuella datorn och överföra en virtuell Hårddisk till Azure.

För att konfigurera krypteringen ska fungera med Azure, gör du följande:

1. Redigera /etc/dracut.conf och Lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Kommentera ut följande rader i slutet av filen /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
```
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
```
    DRACUT_SYSTEMD=0
```
Och ändra alla förekomster av:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
till
```
    if [ 1 ]; then
```
4. Redigera /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och Lägg till nedanstående efter ”# öppna LUKS enhet”:
    ```
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

![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a> Ladda upp krypterade VHD till ett Azure storage-konto
När BitLocker-kryptering eller DM-Crypt kryptering har aktiverats, måste den lokala krypterade virtuella Hårddisken som ska överföras till ditt lagringskonto.
```powershell
    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a> Ladda upp hemligheten för den förkrypterade virtuella datorn till ditt nyckelvalv
När du krypterar med hjälp av Azure AD-app (tidigare version), måste diskkryptering hemligheten som du fick tidigare laddas upp som en hemlighet i nyckelvalvet. Nyckelvalvet måste ha diskkryptering och behörigheter som har aktiverats för din Azure AD-klient.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a> Diskkrypteringshemlighet som inte är krypterade med en KEK
Om du vill konfigurera hemlighet i nyckelvalvet, använda [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Om du har en Windows-dator kan filen bek kodas som en base64-sträng och sedan överförs till ditt nyckelvalv med hjälp av den `Set-AzureKeyVaultSecret` cmdlet. För Linux, lösenfrasen kodas som en base64-sträng och sedan överförs till nyckelvalvet. Kontrollera dessutom att följande taggar är inställda när du skapar hemligheten i nyckelvalvet.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Använd den `$secretUrl` i nästa steg för [koppla OS-disk utan att använda KEK](#bkmk_URLnoKEK).

### <a name="bkmk_SecretKEK"></a> Diskkrypteringshemlighet som krypterats med en KEK
Innan du laddar upp hemligheten till nyckelvalvet kryptera du om du vill det med hjälp av en nyckelkrypteringsnyckel. Använd radbyte [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) att kryptera hemligheten med den viktiga krypteringsnyckeln först. Utdata från åtgärden radbyte är en base64-URL-kodad sträng som du kan sedan överföra som en hemlighet med hjälp av den [ `Set-AzureKeyVaultSecret` ](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

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

Använd `$KeyEncryptionKey` och `$secretUrl` i nästa steg för [koppla OS-disken med hjälp av KEK](#BKMK_URLKEK).

##  <a name="bkmk_SecretURL"></a> Ange en hemlig URL när du kopplar en OS-disk

###  <a name="bkmk_URLnoKEK"></a>Utan att använda en KEK
När du kopplar OS-disken, måste du skicka `$secretUrl`. URL: en har genererats i avsnittet ”diskkryptering hemlighet som inte är krypterade med en KEK”.
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="bkmk_URLKEK"></a>Med hjälp av en KEK
När du ansluter OS-disken, skicka `$KeyEncryptionKey` och `$secretUrl`. URL: en har genererats i avsnittet ”diskkrypteringshemlighet krypterade med en KEK”.
```powershell
    Set-AzureRmVMOSDisk `
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
