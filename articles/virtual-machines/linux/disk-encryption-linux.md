---
title: Azure Disk Encryption-scenarier på virtuella Linux-datorer
description: Den här artikeln innehåller instruktioner om hur du aktiverar Microsoft Azure Disk Encryption for Linux-virtuella datorer för olika scenarier
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b7df3c07518a9211bd1abf785e3f4954c41ebffc
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529453"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption-scenarier på virtuella Linux-datorer

Azure Disk Encryption använder DM-Crypt-funktionen i Linux för att tillhandahålla volymkryptering för operativsystem och datadiskar för virtuella Azure-datorer (VMs) och är integrerad med Azure Key Vault för att hjälpa dig att kontrollera och hantera diskkrypteringsnycklar och hemligheter. En översikt över tjänsten finns i [Azure Disk Encryption for Linux VMs](disk-encryption-overview.md).

Det finns många diskkrypteringsscenarier och stegen kan variera beroende på scenariot. Följande avsnitt täcker scenarierna mer i detalj för virtuella Linux-datorer.

Du kan bara använda diskkryptering på virtuella datorer med [vm-storlekar och operativsystem som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems). Du måste också uppfylla följande förutsättningar:

- [Ytterligare krav för virtuella datorer](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Krav på nätverk](disk-encryption-overview.md#networking-requirements)
- [Lagringskrav för krypteringsnyckel](disk-encryption-overview.md#encryption-key-storage-requirements)

I samtliga fall bör du [ta en ögonblicksbild](snapshot-copy-managed-disk.md) och/eller skapa en säkerhetskopia innan diskarna krypteras. Säkerhetskopior säkerställer att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. När en säkerhetskopia har gjorts kan du använda [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Mer information om hur du säkerhetskopierar och återställer krypterade virtuella datorer finns i artikeln [Kring Azure Backup.](../../backup/backup-azure-vms-encryption.md) 

>[!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera din virtuella dator. Mer information finns i [Azure Disk Encryption med Azure AD (tidigare version).](disk-encryption-overview-aad.md) 
>
> - När du krypterar Linux OS-volymer bör den virtuella datorn anses vara otillgänglig. Vi rekommenderar starkt att undvika SSH-inloggningar medan krypteringen pågår för att undvika problem som blockerar öppna filer som måste nås under krypteringsprocessen. Om du vill kontrollera förloppet använder du cmdleten [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell eller att kommandot [VM-kryptering visar](/cli/azure/vm/encryption#az-vm-encryption-show) CLI. Den här processen kan förväntas ta några timmar för en 30 GB OS-volym, plus ytterligare tid för kryptering av datavolymer. Krypteringstiden för datavolym kommer att stå i proportion till datavolymernas storlek och kvantitet om inte alternativet kryptera format används. 
> - Inaktivera kryptering på virtuella Linux-datorer stöds endast för datavolymer. Det stöds inte på data- eller OS-volymer om OS-volymen har krypterats.  

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och ansluta till Azure

Azure Disk Encryption kan aktiveras och hanteras via [Azure CLI](/cli/azure) och [Azure PowerShell](/powershell/azure/new-azureps-module-az). För att göra det måste du installera verktygen lokalt och ansluta till din Azure-prenumeration.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) är ett kommandoradsverktyg för hantering av Azure-resurser. CLI är utformad för att flexibelt fråga data, stödja tidskrävande åtgärder som icke-blockerande processer och göra skripting enkelt. Du kan installera den lokalt genom att följa stegen i [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

 

Om du vill [logga in på ditt Azure-konto med Azure CLI](/cli/azure/authenticate-azure-cli)använder du kommandot az [login.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli
az login
```

Om du vill välja en klient att logga in under använder du:
    
```azurecli
az login --tenant <tenant>
```

Om du har flera prenumerationer och vill ange en specifik prenumerationslista med [az-kontolista](/cli/azure/account#az-account-list) och ange med [az-kontouppsättning](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Mer information finns i [Komma igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az-modulen](/powershell/azure/new-azureps-module-az) innehåller en uppsättning cmdlets som använder [Azure Resource Manager-modellen](../../azure-resource-manager/management/overview.md) för att hantera dina Azure-resurser. Du kan använda den i din webbläsare med [Azure Cloud Shell](../../cloud-shell/overview.md)eller installera den på din lokala dator med hjälp av instruktionerna i Installera Azure [PowerShell-modulen](/powershell/azure/install-az-ps). 

Om du redan har den installerad lokalt kontrollerar du att du använder den senaste versionen av Azure PowerShell SDK-versionen för att konfigurera Azure Disk Encryption. Hämta den senaste versionen av [Azure PowerShell-versionen](https://github.com/Azure/azure-powershell/releases).

Om du vill [logga in på ditt Azure-konto med Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)använder du cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```powershell
Connect-AzAccount
```

Om du har flera prenumerationer och vill ange en använder du cmdleten [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) för att lista dem, följt av cmdleten [Set-AzContext:](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Om du kör cmdleten [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verifieras att rätt prenumeration har valts.

Om du vill bekräfta att azure diskkrypterings-cmdlets är installerade använder du cmdleten [Get-command:](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)
     
```powershell
Get-command *diskencryption*
```
Mer information finns i [Komma igång med Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Aktivera kryptering på en befintlig eller körande Virtuell Linux-dator
I det här fallet kan du aktivera kryptering med hjälp av Resource Manager-mallen, PowerShell-cmdlets eller CLI-kommandon. Om du behöver schemainformation för tillägget för den virtuella datorn läser du artikeln [Azure Disk Encryption for Linux-tillägg.](../extensions/azure-disk-enc-linux.md)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbild och/eller säkerhetskopiera en hanterad diskbaserad VM-instans utanför och innan Azure Disk Encryption aktiveras. En ögonblicksbild av den hanterade disken kan tas från portalen eller via [Azure Backup](../../backup/backup-azure-vms-encryption.md). Säkerhetskopior säkerställer att ett återställningsalternativ är möjligt vid oväntade fel under krypteringen. När en säkerhetskopia har gjorts kan cmdlet set-AzVMDiskEncryptionExtension användas för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Set-AzVMDiskEncryptionExtension-kommandot misslyckas mot hanterade diskbaserade virtuella datorer tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Om du krypterar eller inaktiverar kryptering kan den virtuella datorn startas om. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Aktivera kryptering på en befintlig eller körande Linux-virtuell dator med Azure CLI 

Du kan aktivera diskkryptering på din krypterade virtuella hårddisk genom att installera och använda kommandoradsverktyget [i Azure CLI.](/cli/azure/?view=azure-cli-latest) Du kan använda det i webbläsaren med [Azure Cloud Shell](../../cloud-shell/overview.md) eller installera det på din lokala dator och använda det i PowerShell-sessioner. Om du vill aktivera kryptering på befintliga eller köra Linux-virtuella datorer i Azure använder du följande CLI-kommandon:

Använd kommandot [az vm-kryptering aktivera](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) för att aktivera kryptering på en virtuell dator som körs i Azure.

- **Kryptera en virtuell dator som körs:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Kryptera en virtuell dator som körs med KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI till KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatus för en virtuell dator använder du kommandot [az vm-krypteringsvisning.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du kommandot [az vm-kryptering inaktivera.](/cli/azure/vm/encryption#az-vm-encryption-disable) Inaktivera kryptering är endast tillåtet på datavolymer för virtuella Linux-datorer.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Aktivera kryptering på en befintlig eller körande Virtuell Linux med PowerShell
Använd [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell dator som körs i Azure. Ta en [ögonblicksbild](snapshot-copy-managed-disk.md) och/eller säkerhetskopiera den virtuella datorn med [Azure Backup](../../backup/backup-azure-vms-encryption.md) innan diskarna krypteras. Parametern -skipVmBackup har redan angetts i PowerShell-skript för att kryptera en linux-virtuell dator som körs.

-  **Kryptera en virtuell dator som körs:** Skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Resursgruppen, VM och nyckelvalvet, skapades som förutsättningar. Ersätt MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden. Ändra parametern -VolumeType för att ange vilka diskar du krypterar.

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
- **Kryptera en virtuell dator som körs med KEK:** Du kan behöva lägga till parametern -VolumeType om du krypterar datadiskar och inte OS-disken. 

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
    > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI till KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatus för en virtuell dator använder du cmdleten [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera diskkryptering:** Om du vill inaktivera krypteringen använder du cmdleten [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Inaktivera kryptering är endast tillåtet på datavolymer för virtuella Linux-datorer.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Aktivera kryptering på en befintlig eller körande Virtuell Linux med en mall

Du kan aktivera diskkryptering på en befintlig eller körande Linux-vm i Azure med hjälp av [resource manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Klicka på **Distribuera till Azure** på snabbstartsmallen för Azure.

2. Välj prenumeration, resursgrupp, resursgruppsplats, parametrar, juridiska termer och avtal. Klicka på **Skapa** om du vill aktivera kryptering på den befintliga eller pågående virtuella datorn.

I följande tabell visas mallparametrar för Resurshanteraren för befintliga eller pågående virtuella datorer:

| Parameter | Beskrivning |
| --- | --- |
| vmName | Namn på den virtuella datorn för att köra krypteringsåtgärden. |
| keyVaultName | Namn på nyckelvalvet som krypteringsnyckeln ska överföras till. Du kan hämta den med `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` kommandot cmdlet `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`eller Azure CLI .|
| keyVaultResourceGroup | Namn på resursgruppen som innehåller nyckelvalvet. |
|  keyEncryptionKeyURL | URL för nyckelkrypteringsnyckeln som används för att kryptera krypteringsnyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan AnvändExistingKek. Om du väljer **kek** i listrutan AnvändExistingKek måste du ange _keyEncryptionKeyURL-värdet._ |
| volumeType (volumeType) | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS,_ _Data_och _Alla_. 
| forceUpdateTag | Skicka in ett unikt värde som ett GUID varje gång operationen måste tvångskörning. |
| location | Plats för alla resurser. |

Mer information om hur du konfigurerar diskkrypteringsmallen för Linux VM finns i [Azure Disk Encryption for Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/azure-disk-enc-linux).

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Använd funktionen EncryptFormatAll för datadiskar på virtuella Linux-datorer

Parametern **EncryptFormatAll** minskar tiden för Linux-datadiskar som ska krypteras. Partitioner som uppfyller vissa villkor formateras (med dess nuvarande filsystem), sedan återmonteras tillbaka till där det var innan kommandot körning. Om du vill utesluta en datadisk som uppfyller villkoren kan du avmontera den innan du kör kommandot.

 När du har kört det här kommandot formateras alla enheter som tidigare monterades och krypteringslagret startas ovanpå den nu tomma enheten. När det här alternativet är markerat krypteras även den tillfälliga resursdisken som är kopplad till den virtuella datorn. Om efemära enheten återställs formateras den om och krypteras om för den virtuella datorn av Azure Disk Encryption-lösningen vid nästa tillfälle. När resursdisken har krypterats kan [Microsoft Azure Linux Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) inte hantera resursdisken och aktivera växlingsfilen, men du kan konfigurera växlingsfilen manuellt.

>[!WARNING]
> EncryptFormatAll bör inte användas när det behövs data på en virtuell dators datavolymer. Du kan utesluta diskar från kryptering genom att avmontera dem. Du bör först prova EncryptFormatAll först på en test-VM, förstå funktionsparametern och dess innebörd innan du försöker den på produktionsdatorn. Alternativet EncryptFormatAll formaterar datadisken och alla data på den går förlorade. Innan du fortsätter kontrollerar du att diskar som du vill utesluta har avmonterats korrekt. </br></br>
 >Om du anger den här parametern när du uppdaterar krypteringsinställningarna kan det leda till en omstart före själva krypteringen. I det här fallet vill du också ta bort disken som du inte vill formatera från fstab-filen. På samma sätt bör du lägga till den partition som du vill kryptera-formaterad i fstab-filen innan du påbörjar krypteringsåtgärden. 

### <a name="encryptformatall-criteria"></a>Villkor för EncryptFormatAll
Parametern går om alla partitioner och krypterar dem så länge de uppfyller **alla** kriterier nedan: 
- Är inte en rot/OS/startpartition
- Är inte redan krypterad
- Är inte en BEK-volym
- Är inte en RAID-volym
- Är inte en LVM-volym
- Är monterad

Kryptera diskarna som utgör RAID- eller LVM-volymen i stället för RAID- eller LVM-volymen.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Använda parametern EncryptFormatAll med Azure CLI
Använd kommandot [az vm-kryptering aktivera](/cli/azure/vm/encryption#az-vm-encryption-enable) för att aktivera kryptering på en virtuell dator som körs i Azure.

-  **Kryptera en virtuell dator som körs med EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Använda parametern EncryptFormatAll med en PowerShell-cmdlet
Använd [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) med parametern EncryptFormatAll. 

**Kryptera en virtuell dator som körs med EncryptFormatAll:** Skriptet nedan initierar till exempel variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension med parametern EncryptFormatAll. Resursgruppen, vm och nyckelvalv skapades som förutsättningar. Ersätt MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Använda parametern EncryptFormatAll med Logisk volymhanterare (LVM) 
Vi rekommenderar en LVM-on-crypt setup. För alla följande exempel, byt ut enhetsbanan och monteringspunkterna mot vad som passar ditt användningsfall. Den här inställningen kan göras på följande sätt:

- Lägg till de datadiskar som ska skapa den virtuella datorn.
- Formatera, montera och lägg till dessa diskar i fstab-filen.

    1. Välj en partitionsstandard, skapa en partition som sträcker sig över hela enheten och formatera partitionen. Vi använder symlinks som genereras av Azure här. Om du använder symlänkar undviks problem relaterade till att enhetsnamn ändras. Mer information finns i artikeln [Felsöka enhetsnamnsproblem.](troubleshoot-device-names-problems.md)
    
         ```azurepowershell-interactive
         parted /dev/disk/azure/scsi1/lun0 mklabel gpt
         parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
         
         mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
         ```
    
    1. Montera diskarna.
         
         `mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint`
    
    1. Lägg till fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab`
    
    1. Kör Cmdleten Set-AzVMDiskEncryptionExtension PowerShell med -EncryptFormatAll för att kryptera dessa diskar.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Ställ in LVM ovanpå dessa nya diskar. Observera att de krypterade enheterna låses upp när den virtuella datorn har startats klart. Så, LVM montering måste också senare försenas.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nya virtuella datorer som skapats från kundkrypterade virtuella hårddiskar och krypteringsnycklar
I det här fallet kan du aktivera kryptering med hjälp av PowerShell-cmdlets eller CLI-kommandon. 

Använd instruktionerna i Azure Disk-kryptering samma skript för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan du använda stegen i nästa avsnitt för att skapa en krypterad Azure VM.

* [Förbereda en förkrypterad Virtuell Linux-hårddisk](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbild och/eller säkerhetskopiera en hanterad diskbaserad VM-instans utanför och innan Azure Disk Encryption aktiveras. En ögonblicksbild av den hanterade disken kan tas från portalen eller [Azure Backup](../../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior säkerställer att ett återställningsalternativ är möjligt vid oväntade fel under krypteringen. När en säkerhetskopia har gjorts kan cmdlet set-AzVMDiskEncryptionExtension användas för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Set-AzVMDiskEncryptionExtension-kommandot misslyckas mot hanterade diskbaserade virtuella datorer tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
> Om du krypterar eller inaktiverar kryptering kan den virtuella datorn startas om. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Använda Azure PowerShell för att kryptera virtuella datorer med förkrypterade virtuella hårddiskar 
Du kan aktivera diskkryptering på din krypterade virtuella hårddisk med hjälp av PowerShell cmdlet [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). Exemplet nedan ger dig några vanliga parametrar. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk

Du kan lägga till en ny datadisk med [az vm-disken eller](add-disk.md) [via Azure-portalen](attach-disk-portal.md). Innan du kan kryptera måste du först montera den nyligen anslutna datadisken. Du måste begära kryptering av dataenheten eftersom enheten kommer att vara oersättlig medan kryptering pågår. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI

 Om den virtuella datorn tidigare krypterades med "Alla" ska parametern --volume-type förbli "Alla". Alla innehåller både OS och datadiskar. Om den virtuella datorn tidigare har krypterats med en volymtyp av "OS" ska parametern --volume-type ändras till "Alla" så att både operativsystemet och den nya datadisken inkluderas. Om den virtuella datorn krypterades med endast volymtypen "Data", kan den förbli "Data" som visas nedan. Att lägga till och koppla en ny datadisk till en virtuell dator är inte tillräckligt för förberedelse för kryptering. Den nyligen anslutna disken måste också formateras och monteras korrekt i den virtuella datorn innan kryptering aktiveras. På Linux måste disken monteras i /etc/fstab med ett [beständigt blockenhetsnamn](troubleshoot-device-names-problems.md).  

I motsats till Powershell-syntaxen kräver CLI inte att användaren tillhandahåller en unik sekvensversion när kryptering aktiveras. CLI genererar och använder automatiskt sitt eget unika sekvensversionsvärde.

-  **Kryptera datavolymer för en virtuell dator som körs:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Kryptera datavolymer för en virtuell dator som körs med KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder Powershell för att kryptera en ny disk för Linux måste en ny sekvensversion anges. Sekvensversionen måste vara unik. Skriptet nedan genererar ett GUID för sekvensversionen. Ta en [ögonblicksbild](snapshot-copy-managed-disk.md) och/eller säkerhetskopiera den virtuella datorn med [Azure Backup](../../backup/backup-azure-vms-encryption.md) innan diskarna krypteras. Parametern -skipVmBackup har redan angetts i PowerShell-skripten för att kryptera en nyligen tillagd datadisk.
 

-  **Kryptera datavolymer för en virtuell dator som körs:** Skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Resursgruppen, den virtuella datorn och nyckelvalvet borde redan ha skapats som förutsättningar. Ersätt MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden. Godtagbara värden för parametern -VolumeType är Alla, OS och Data. Om den virtuella datorn tidigare har krypterats med en volymtyp av "OS" eller "Alla", bör parametern -VolumeType ändras till "Alla" så att både operativsystemet och den nya datadisken inkluderas.

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
- **Kryptera datavolymer för en virtuell dator som körs med KEK:** Godtagbara värden för parametern -VolumeType är Alla, OS och Data. Om den virtuella datorn tidigare har krypterats med en volymtyp av "OS" eller "Alla", bör parametern -VolumeType ändras till Alla så att både operativsystemet och den nya datadisken inkluderas.

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
    > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI till KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Inaktivera kryptering för virtuella Linux-datorer
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI eller med en Resource Manager-mall. 

>[!IMPORTANT]
>Inaktivera kryptering med Azure Disk Encryption på virtuella Linux-datorer stöds endast för datavolymer. Det stöds inte på data- eller OS-volymer om OS-volymen har krypterats.  

- **Inaktivera diskkryptering med Azure PowerShell:** Om du vill inaktivera krypteringen använder du cmdleten [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du kommandot [az vm-kryptering inaktivera.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** Använd [inaktivera kryptering på en linux-vm-mall som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) för att inaktivera kryptering.
     1. Klicka på **Distribuera till Azure**.
     2. Välj prenumeration, resursgrupp, plats, virtuell dator, juridiska termer och avtal.

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Azure Disk Encryption fungerar inte för följande Linux-scenarier, funktioner och teknik:

- Kryptera grundläggande virtuell nivå eller virtuella datorer som skapats via den klassiska metoden för att skapa virtuella datorer.
- Inaktivera kryptering på en OS-enhet eller dataenhet för en Virtuell Linux-dator när OS-enheten är krypterad.
- Kryptera OS-enhet för Linux virtuella dator skalning uppsättningar.
- Kryptera anpassade avbildningar på virtuella Linux-datorer.
- Integration med ett lokalt nyckelhanteringssystem.
- Azure-filer (delat filsystem).
- NFS (Network File System).
- Dynamiska volymer.
- Efemära OPERATIVSYSTEM diskar.
- Kryptering av delade/distribuerade filsystem som (men inte begränsat till): DFS, GFS, DRDB och CephFS.
- Flytta en krypterad virtuell dator till en annan prenumeration.
- Kernel Crash Dump (kdump).
- Virtuella datorer i Lsv2-serien (se: [Lsv2-serien](../lsv2-series.md))

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Disk-kryptering](disk-encryption-overview.md)
- [Exempelskript för Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Felsökning för Azure Disk Encryption](disk-encryption-troubleshooting.md)
