---
title: Azure Disk Encryption scenarier på virtuella Linux-datorer
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure disk kryptering för virtuella Linux-datorer för olika scenarier
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: e7f8c029c5ceff0865b060cf8edc794454d6c282
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828473"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption scenarier på virtuella Linux-datorer

Azure Disk Encryption använder DM-crypt-funktionen i Linux för att tillhandahålla volym kryptering för operativ system och data diskar för virtuella Azure-datorer (VM) och är integrerat med Azure Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklar och hemligheter. En översikt över tjänsten finns i [Azure Disk Encryption för virtuella Linux-datorer](disk-encryption-overview.md).

Det finns många disk krypterings scenarier och stegen kan variera beroende på scenariot. I följande avsnitt beskrivs scenarierna i större detalj för virtuella Linux-datorer.

Du kan bara använda disk kryptering för virtuella datorer med [stödda VM-storlekar och operativ system](disk-encryption-overview.md#supported-vms-and-operating-systems). Du måste också uppfylla följande krav:

- [Ytterligare krav för virtuella datorer](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Nätverks krav](disk-encryption-overview.md#networking-requirements)
- [Lagrings krav för krypterings nyckel](disk-encryption-overview.md#encryption-key-storage-requirements)

I samtliga fall bör du [ta en ögonblicks bild](snapshot-copy-managed-disk.md) och/eller skapa en säkerhets kopia innan diskarna krypteras. Säkerhetskopior Se till att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. När du har gjort en säkerhets kopia kan du använda [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att kryptera hanterade diskar genom att ange parametern-skipVmBackup. Mer information om hur du säkerhetskopiera och återställa krypterade virtuella datorer finns i den [Azure Backup](../../backup/backup-azure-vms-encryption.md) artikeln. 

>[!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Mer information finns i [Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-overview-aad.md) . 
>
> - När du krypterar Linux OS-volymer bör den virtuella datorn anses vara otillgänglig. Vi rekommenderar starkt att du undviker SSH-inloggningar medan krypteringen pågår för att undvika problem som blockerar eventuella öppna filer som behöver nås under krypterings processen. Om du vill kontrol lera förloppet använder du PowerShell-cmdleten [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) eller [VM-kryptering Visa](/cli/azure/vm/encryption#az-vm-encryption-show) CLI-kommando. Den här processen kan förväntas ta några timmar för en 30 GB OS-volym, plus ytterligare tid för kryptering av data volymer. Krypterings tiden för data volymer är proportionell mot storlek och kvantitet för data volymerna om inte alternativet Kryptera format alla används. 
> - Inaktivera kryptering på den virtuella Linux-datorer stöds bara för datavolymer. Det stöds inte på data eller operativsystemvolymer om operativsystemvolymen har krypterats.  

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och Anslut till Azure

Azure Disk Encryption kan aktive ras och hanteras via [Azure CLI](/cli/azure) och [Azure PowerShell](/azure/new-azureps-module-az). Om du vill göra det måste du installera verktygen lokalt och ansluta till din Azure-prenumeration.

### <a name="azure-cli"></a>Azure CLI

Den [Azure CLI 2.0](/cli/azure) är ett kommandoradsverktyg för att hantera Azure-resurser. CLI är utformat för att fråga efter data, stöd för långvariga åtgärder som icke-blockerande processer och göra skript enkelt flexibelt. Du kan installera det lokalt genom att följa stegen i [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill [Logga in på ditt Azure-konto med Azure CLI](/cli/azure/authenticate-azure-cli)använder du kommandot [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Om du vill välja en klient att logga in under Använd:
    
```azurecli
az login --tenant <tenant>
```

Om du har flera prenumerationer och vill ange en kan hämta din prenumerationslista med [az kontolista](/cli/azure/account#az-account-list) och ange med [az-kontogrupper](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Mer information finns i [Kom igång med Azure CLI 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Modulen Azure PowerShell AZ](/azure/new-azureps-module-az) innehåller en uppsättning cmdletar som använder [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) -modellen för att hantera dina Azure-resurser. Du kan använda den i din webbläsare med [Azure Cloud Shell](../../cloud-shell/overview.md), eller så kan du installera den på din lokala dator med hjälp av anvisningarna i [installera modulen Azure PowerShell](/powershell/azure/install-az-ps). 

Om du redan har installerat lokalt kan du kontrollera att du använder den senaste versionen av Azure PowerShell SDK-version för att konfigurera Azure Disk Encryption. Ladda ned den senaste versionen av [Azure PowerShell version](https://github.com/Azure/azure-powershell/releases).

Om du vill [Logga in på ditt Azure-konto med Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)använder du cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Om du har flera prenumerationer och vill ange en använder du cmdleten [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) för att lista dem, följt av cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Genom att köra cmdleten [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verifierar du att rätt prenumeration har valts.

Om du vill bekräfta att Azure Disk Encryption-cmdletarna är installerade använder du cmdleten [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Mer information finns i [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Aktivera kryptering på en befintlig eller virtuell Linux-dator
I det här scenariot kan du aktivera kryptering med hjälp av Resource Manager-mall, PowerShell-cmdletar eller CLI-kommandon. Om du behöver information för databasschema för tillägget för virtuell dator finns i den [Azure Disk Encryption for Linux-tillägget](../extensions/azure-disk-enc-linux.md) artikeln.

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicks bild av den hanterade disken kan hämtas från portalen eller via [Azure Backup](../../backup/backup-azure-vms-encryption.md). Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. När en säkerhets kopia har gjorts kan cmdleten Set-AzVMDiskEncryptionExtension användas för att kryptera Managed disks genom att ange parametern-skipVmBackup. Kommandot Set-AzVMDiskEncryptionExtension fungerar inte mot hanterade diskbaserade virtuella datorer förrän en säkerhets kopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Aktivera kryptering på en befintlig eller virtuell Linux-dator med Azure CLI 

Du kan aktivera disk kryptering på din krypterade virtuella hård disk genom att installera och använda kommando rads verktyget för [Azure CLI](/cli/azure/?view=azure-cli-latest) . Du kan använda det i webbläsaren med [Azure Cloud Shell](../../cloud-shell/overview.md) eller installera det på din lokala dator och använda det i PowerShell-sessioner. Använd följande CLI-kommandon om du vill aktivera kryptering på befintliga eller virtuella Linux-datorer i Azure:

Använd kommandot [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) för att aktivera kryptering på en virtuell dator som körs i Azure.

- **Kryptera en aktiv virtuell dator:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Kryptera en aktiv virtuell dator med hjälp av KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrol lera att diskarna är krypterade:** Om du vill kontrol lera krypterings statusen för en virtuell dator använder du kommandot [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du kommandot [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Inaktivera kryptering tillåts endast på datavolymer för virtuella Linux-datorer.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Aktivera kryptering på en befintlig eller virtuell Linux-dator med PowerShell
Använd cmdleten [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell dator som körs i Azure. Ta en [ögonblicks bild](snapshot-copy-managed-disk.md) och/eller säkerhetskopiera den virtuella datorn med [Azure Backup](../../backup/backup-azure-vms-encryption.md) innan diskarna krypteras. Parametern-skipVmBackup har redan angetts i PowerShell-skripten för att kryptera en virtuell Linux-dator som körs.

-  **Kryptera en aktiv virtuell dator:** Skriptet nedan initierar variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension. Resurs gruppen, den virtuella datorn och nyckel valvet har skapats som krav. Ersätt MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden. Ändra parametern-VolumeType för att ange vilka diskar som du ska kryptera.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Kryptera en aktiv virtuell dator med hjälp av KEK:** Du kan behöva lägga till parametern - VolumeType om du krypterar datadiskar och inte OS-disken. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Kontrol lera att diskarna är krypterade:** Om du vill kontrol lera krypterings statusen för en virtuell dator använder du cmdleten [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera disk kryptering:** Om du vill inaktivera krypteringen använder du cmdleten [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Inaktivera kryptering tillåts endast på datavolymer för virtuella Linux-datorer.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Aktivera kryptering på en befintlig eller virtuell Linux-dator med en mall

Du kan aktivera disk kryptering på en befintlig eller köra en virtuell Linux-dator i Azure med hjälp av [Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Klicka på **distribuera till Azure** på Azure-snabbstartsmall.

2. Välj prenumeration, resursgrupp, resursgruppens plats, parametrar, juridiska villkor och avtal. Klicka på **skapa** för att aktivera kryptering på den befintliga eller aktiva virtuella datorn.

I följande tabell visas Resource Manager-mallens parametrar för befintliga eller köra virtuella datorer:

| Parameter | Beskrivning |
| --- | --- |
| vmName | Namnet på den virtuella datorn att köra krypteringsåtgärden. |
| keyVaultName | Namnet på nyckel valvet som krypterings nyckeln ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` eller Azure CLI-kommandot `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`.|
| keyVaultResourceGroup | Namnet på den resurs grupp som innehåller nyckel valvet. |
|  KeyEncryptionKeyURL | URL till den nyckel krypterings nyckel som används för att kryptera krypterings nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan UseExistingKek. Om du väljer **kek** i listrutan UseExistingKek måste du ange den _keyEncryptionKeyURL_ värde. |
| VolumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS_, _Data_, och _alla_. 
| forceUpdateTag | Skicka in ett unikt värde som en GUID varje gång åtgärden måste vara tvinga kör. |
| resizeOSDisk | Vill du ändra storlek OS-partition för att uppta fullständig OS-VHD innan du delar systemvolym. |
| location | Plats för alla resurser. |


## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Använda funktionen EncryptFormatAll för data diskar på virtuella Linux-datorer

Den **EncryptFormatAll** parametern minskar tid för Linux datadiskar måste vara krypterade. Partitioner som uppfyller vissa villkor formateras (med det aktuella fil systemet) och monteras sedan tillbaka till den plats där det var innan kommando körningen. Om du vill exkludera en datadisk som uppfyller villkoren kan demontera du den innan du kör kommandot.

 När du har kört det här kommandot kommer alla enheter som monterats tidigare att formateras och krypterings lagret startas ovanpå den nu tomma enheten. När det här alternativet är markerat krypteras också tillfälliga resursdisk som är ansluten till den virtuella datorn. Om den tillfälliga enheten återställs den formateras om och omkrypteras med hjälp av Azure Disk Encryption-lösningen vid nästa tillfälle för den virtuella datorn. När resurs disken krypteras kommer [Microsoft Azure Linux-agenten](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) inte att kunna hantera resurs disken och aktivera växlings filen, men du kan konfigurera växlings filen manuellt.

>[!WARNING]
> EncryptFormatAll bör inte användas när det nödvändiga data på en virtuell dators datavolymer. Du kan undanta diskar från kryptering av demontera dem. Du bör först prova EncryptFormatAll först på en virtuell testdator, Förstå funktionsparametern och dess de innan du försöker på den Virtuella produktionsdatorn. Alternativet EncryptFormatAll formaterar datadisken och alla data på den kommer att gå förlorade. Kontrollera att du vill utesluta diskar är korrekt demonterats innan du fortsätter. </br></br>
 >Om du konfigurerar den här parametern vid uppdatering av krypteringsinställningar, kan det leda till en omstart innan du själva krypteringen. I det här fallet vill du också ta bort disken som du inte vill formaterade från fstab-filen. På samma sätt bör du lägga till den partition som du vill kryptera-formaterad till fstab-filen innan du påbörjar krypteringsåtgärden. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll kriterier
Parametern går dock alla partitioner och krypterar dem så länge de uppfyller **alla** av följande villkor: 
- Är inte en rot-OS-Start-partition
- Krypteras inte redan
- Är inte en BEK volym
- Är inte en RAID-volym
- Är inte en LVM-volym
- Är monterad

Kryptera diskar som utgör RAID- eller LVM volymen i stället för RAID- eller LVM volymen.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Använda EncryptFormatAll-parametern med Azure CLI
Använd kommandot [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) för att aktivera kryptering på en virtuell dator som körs i Azure.

-  **Kryptera en aktiv virtuell dator med hjälp av EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Använda parametern EncryptFormatAll med en PowerShell-cmdlet
Använd cmdleten [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) med parametern EncryptFormatAll. 

**Kryptera en aktiv virtuell dator med hjälp av EncryptFormatAll:** Skriptet nedan initierar till exempel variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension med parametern EncryptFormatAll. Resurs gruppen, den virtuella datorn och nyckel valvet har skapats som krav. Ersätt MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Använd parametern EncryptFormatAll med Logical Volume Manager (LVM) 
Vi rekommenderar en LVM-på-crypt-installation. Ersätt enhetens sökväg och monteringspunkter med det passar ditt användningsområde för alla i följande exempel. Den här konfigurationen kan du göra på följande sätt:

- Lägga till datadiskar som kommer utgör den virtuella datorn.
- Formatera, montera och lägga till diskarna i fstab-filen.

    1. Formatera den nytillagda disken. Vi använder symlinks som genereras av Azure här. Med hjälp av symlinks undviker problem som rör enhetsnamn ändras. Mer information finns i den [felsöka enhetsnamn problem](troubleshoot-device-names-problems.md) artikeln.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    1. Montera diskarna.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    1. Lägg till i fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    1. Kör PowerShell-cmdleten Set-AzVMDiskEncryptionExtension med-EncryptFormatAll för att kryptera diskarna.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Konfigurera LVM ovanpå dessa nya diskar. Observera enheterna som är krypterade är upplåst när den virtuella datorn har slutförts startar. LVM-montering måste därför också fördröjas senare.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nya virtuella datorer som skapats från kund-krypterade VHD-och krypterings nycklar
Du kan aktivera kryptering med hjälp av PowerShell-cmdletar eller CLI-kommandona i det här scenariot. 

Följ anvisningarna i Azure Disk Encryption samma skript för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan använda du stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbereda en förkrypterade Linux-VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. När en säkerhets kopia har gjorts kan cmdleten Set-AzVMDiskEncryptionExtension användas för att kryptera Managed disks genom att ange parametern-skipVmBackup. Kommandot Set-AzVMDiskEncryptionExtension fungerar inte mot hanterade diskbaserade virtuella datorer förrän en säkerhets kopia har gjorts och den här parametern har angetts. 
>
> Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Använd Azure PowerShell för att kryptera virtuella datorer med förkrypterade virtuella hård diskar 
Du kan aktivera disk kryptering på din krypterade virtuella hård disk med PowerShell-cmdleten [set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). I exemplet nedan visas några vanliga parametrar. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk

Du kan lägga till en ny disk med [az vm disk attach](add-disk.md), eller [via Azure portal](attach-disk-portal.md). Innan du kan kryptera måste du först montera den nyligen anslutna disken. Du måste begära kryptering av data-enheten eftersom enheten inte att använda medan kryptering pågår. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI

 Om den virtuella datorn tidigare har krypterats med "alla", ska parametern--volym typ vara kvar. Alla innehåller både operativsystem och datadiskar. Om den virtuella datorn tidigare har krypterats med en volymtyp av typen "OS", ska parametern--type ändras till "alla" så att både operativ systemet och den nya datadisken tas med. Om den virtuella datorn har krypterats med endast volymtyp ”data”, kan det finnas ”Data” som visas nedan. Lägga till och koppla en ny datadisk till en virtuell dator är inte tillräckligt med förberedelse för kryptering. Den nyligen anslutna disken måste också vara formaterad och korrekt monterade i den virtuella datorn innan du aktiverar krypteringen. I Linux måste disken monteras i/etc/fstab med en [beständiga block enhetsnamn](troubleshoot-device-names-problems.md).  

Till skillnad från Powershell-syntax kräver CLI inte att användaren anger en unik teckensekvens version när du aktiverar kryptering. CLI genererar automatiskt och använder en egen unik teckensekvens värde.

-  **Kryptera datavolymer med en aktiv virtuell dator:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Kryptera datavolymer med en aktiv virtuell dator med hjälp av KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder Powershell för att kryptera en ny disk för Linux, måste en ny sekvens-version anges. Sekvens-versionen måste vara unikt. Skriptet nedan genererar ett GUID för sekvens-versionen. Ta en [ögonblicks bild](snapshot-copy-managed-disk.md) och/eller säkerhetskopiera den virtuella datorn med [Azure Backup](../../backup/backup-azure-vms-encryption.md) innan diskarna krypteras. Parametern-skipVmBackup har redan angetts i PowerShell-skripten för att kryptera en nyligen tillagd datadisk.
 

-  **Kryptera datavolymer med en aktiv virtuell dator:** Skriptet nedan initierar variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension. Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav. Ersätt MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden. Godkända värden för parametern - VolumeType är alla, OS- och Data. Om den virtuella datorn tidigare har krypterats med volym typen "OS" eller "alla", ska parametern-VolumeType ändras till "alla" så att både operativ systemet och den nya datadisken tas med.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Kryptera datavolymer med en aktiv virtuell dator med hjälp av KEK:** Godkända värden för parametern - VolumeType är alla, OS- och Data. Om den virtuella datorn har tidigare har krypterats med en volymtyp av ”OS” eller ”alla”, ska sedan parametern - VolumeType ändras till alla så att både Operativsystemet och den nya datadisken inkluderas.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[KVresource-Group-name]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Inaktivera kryptering för virtuella Linux-datorer
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI, eller med en Resource Manager-mall. 

>[!IMPORTANT]
>Inaktivera kryptering med Azure Disk Encryption på virtuella Linux-datorer stöds bara för datavolymer. Det stöds inte på data eller operativsystemvolymer om operativsystemvolymen har krypterats.  

- **Inaktivera disk kryptering med Azure PowerShell:** Om du vill inaktivera krypteringen använder du cmdleten [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du kommandot [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** Använd [inaktivera kryptering på en virtuell Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) -mall för virtuella datorer för att inaktivera kryptering.
     1. Klicka på **Distribuera till Azure**.
     2. Välj prenumeration, resursgrupp, plats, VM, juridiska villkor och avtal.

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Azure Disk Encryption fungerar inte för följande scenarier, funktioner och teknik för Linux:

- Kryptering av virtuella datorer på Basic-nivå eller virtuella datorer som skapats via den klassiska skapande metoden för virtuella datorer.
- Inaktivera kryptering på en operativ system enhet eller data enhet på en virtuell Linux-dator när operativ system enheten är krypterad.
- Krypterar OS-enheten för skalnings uppsättningar för virtuella Linux-datorer.
- Kryptera anpassade avbildningar på virtuella Linux-datorer.
- Integrering med ett lokalt nyckel hanterings system.
- Azure Files (delade filsystem).
- Network File System (NFS).
- Dynamiska volymer.
- Tillfälliga OS-diskar.
- Kryptering av delade/distribuerade fil system som (men inte begränsat till): DFS, GFS, DRDB och CephFS.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Disk Encryption](disk-encryption-overview.md)
- [Azure Disk Encryption exempel skript](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption fel sökning](disk-encryption-troubleshooting.md)
