---
title: Aktivera Azure Disk Encryption för Windows virtuella IaaS-datorer | Microsoft Docs
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure Disk Encryption för Windows virtuella IaaS-datorer.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 09/19/2018
ms.openlocfilehash: 04077c9acbd9556a66e8337ab8f415de86df1d5a
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498206"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Aktivera Azure Disk Encryption för Windows virtuella IaaS-datorer 

Du kan aktivera många diskkryptering scenarier och stegen kan variera beroende på scenario. Följande avsnitt beskriver scenarier i större detalj för Windows virtuella IaaS-datorer. Innan du kan använda diskkryptering, den [som krävs för Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites.md) måste slutföras. 

Ta en [ögonblicksbild](../virtual-machines/windows/snapshot-copy-managed-disk.md) och/eller säkerhetskopierar innan diskar krypteras. Säkerhetskopior Se till att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. Du kan använda cmdleten Set-AzureRmVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Mer information om hur du säkerhetskopiera och återställa krypterade virtuella datorer finns i den [Azure Backup](../backup/backup-azure-vms-encryption.md) artikeln. 

>[!WARNING]
> Azure Disk Encryption måste Key Vault och de virtuella datorerna för att finnas i samma region. Skapa och använda ett Nyckelvalv som är i samma region som den virtuella datorn måste vara krypterade. 


## <a name="bkmk_RunningWinVM"></a> Aktivera kryptering på befintliga eller som kör Windows virtuella IaaS-datorer
I det här scenariot kan du aktivera kryptering med hjälp av en mall, PowerShell-cmdletar eller CLI-kommandon. I följande avsnitt beskrivs i detalj hur du aktiverar Azure Disk Encryption. Om du behöver information för databasschema för tillägget för virtuell dator finns i den [Azure Disk Encryption för Windows-tillägget](../virtual-machines/extensions/azure-disk-enc-windows.md) artikeln.

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzureRmVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Kommandot Set-AzureRmVMDiskEncryptionExtension misslyckas mot virtuella datorer med hanterade diskar baserade tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Aktivera kryptering på befintliga eller köra virtuella datorer med Azure PowerShell 
Använd den [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) cmdlet för att aktivera kryptering på en aktiv IaaS virtuell dator i Azure. 

-  **Kryptera en aktiv virtuell dator:** skriptet nedan initierar dina variabler och kör cmdleten Set-AzureRmVMDiskEncryptionExtension. Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav. Ersätt MySecureRg och MySecureVM MySecureVault med dina värden.

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Kryptera en aktiv virtuell dator med hjälp av KEK:** 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** för att kontrollera statusen för kryptering av en IaaS-VM, använda den [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) cmdlet. 
     ```azurepowershell-interactive
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **Inaktivera diskkryptering:** inaktivera kryptering med den [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet. Inaktivera data-diskkryptering på virtuella Windows-datorn när både Operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Aktivera kryptering på befintliga eller köra virtuella datorer med Azure CLI
Använd den [az vm encryption aktivera](/cli/azure/vm/encryption#az-vm-encryption-enable) kommando för att aktivera kryptering på en aktiv IaaS virtuell dator i Azure.

-  **Kryptera en aktiv virtuell dator:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Kryptera en aktiv virtuell dator med hjälp av KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** för att kontrollera statusen för kryptering av en IaaS-VM, använda den [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) kommando. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Inaktivera kryptering:** inaktivera kryptering med den [az vm encryption inaktivera](/cli/azure/vm/encryption#az-vm-encryption-disable) kommando. Inaktivera data-diskkryptering på virtuella Windows-datorn när både Operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzureRmVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Det här kommandot kan utföras mot virtuella datorer med hanterade diskar baserat förrän en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 

### <a name="bkmk_RunningWinVMwRM"> </a>Med hjälp av Resource Manager-mall
Du kan aktivera diskkryptering på befintliga eller kör IaaS Windows-datorer i Azure med hjälp av den [Resource Manager-mall för att kryptera en virtuell dator som körs Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. Klicka på Azure snabbstarts-mall **distribuera till Azure**.

2. Välj prenumeration, resursgrupp, plats, inställningar, juridiska villkor och avtal. Klicka på **köp** att aktivera kryptering på den befintliga eller körs IaaS-VM.

I följande tabell visas Resource Manager-mallens parametrar för befintliga eller köra virtuella datorer:

| Parameter | Beskrivning |
| --- | --- |
| vmName | Namnet på den virtuella datorn att köra krypteringsåtgärden. |
| keyVaultName | Namnet på nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` eller Azure CLI-kommando ' az keyvault list--resource-group ”MySecureGroup” |Convertfrom-JSON ”|
| keyVaultResourceGroup | Namnet på resursgruppen som innehåller nyckelvalvet|
|  KeyEncryptionKeyURL | URL för nyckelkrypteringsnyckel som används för att kryptera den genererade BitLocker-nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan UseExistingKek. Om du väljer **kek** i listrutan UseExistingKek måste du ange den _keyEncryptionKeyURL_ värde. |
| VolumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS_, _Data_, och _alla_. 
| forceUpdateTag | Skicka in ett unikt värde som en GUID varje gång åtgärden måste vara tvinga kör. |
| resizeOSDisk | Vill du ändra storlek OS-partition för att uppta fullständig OS-VHD innan du delar systemvolym. |
| location | Plats för alla resurser. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Kryptera VM-skalningsuppsättningar
[Azure VM-skalningsuppsättningar](../virtual-machine-scale-sets/overview.md) kan du skapa och hantera grupper med identiska, belastningsutjämnade virtuella datorer. Antal VM-instanser kan automatiskt öka eller minska som svar på efterfrågan eller ett definierat schema. Använda CLI eller Azure PowerShell för att kryptera VM-skalningsuppsättningar.


### <a name="register-for-disk-encryption-preview-using-azure-powershell"></a>Registrera dig för disk encryption förhandsversionen via Azure Powershell

Azure-diskkryptering för VM-skalningsuppsättningar förhandsversion måste du registrera din prenumeration med [registrera-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature). Du behöver bara utföra följande steg första gången du använder förhandsgranskningsfunktionen disk kryptering:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Det kan ta upp till 10 minuter för registreringsbegäran att spridas. Du kan kontrollera status för enhetsregistreringen med [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). När den `RegistrationState` rapporter *registrerad*, registrera den *Mirosoft.Compute* provider med [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Kryptera VM-skalningsuppsättningar med Azure PowerShell

Använd den [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension) cmdlet för att aktivera kryptering på en Windows VM-skalningsuppsättning. Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav.

-  **Kryptera en VM-skalningsuppsättning**:
    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

- **Hämta krypteringsstatus för en VM-skalningsuppsättning:** används den [Get-AzureRmVmssVMDiskEncryption](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption) cmdlet.
    
    ```azurepowershell-interactive
    get-AzureRmVmssVMDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

- **Inaktivera kryptering på en VM-skalningsuppsättning**: Använd den [Disable-AzureRmVmssDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption) cmdlet. 

    ```azurepowershell-interactive
    Disable-AzureRmVmssDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

### <a name="register-for-disk-encryption-preview-using-azure-cli"></a>Registrera dig för förhandsversion av disk encryption med Azure CLI

Azure-diskkryptering för VM-skalningsuppsättningar förhandsversion måste du registrera din prenumeration med [az funktionen registrera](/cli/azure/feature#az_feature_register). Du behöver bara utföra följande steg första gången du använder förhandsgranskningsfunktionen disk kryptering:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

Det kan ta upp till 10 minuter för registreringsbegäran att spridas. Du kan kontrollera status för enhetsregistreringen med [az funktionen show](/cli/azure/feature#az_feature_show). När den `State` rapporter *registrerad*, registrera den *Mirosoft.Compute* provider med [az provider register](/cli/azure/provider#az_provider_register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```



###  <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Kryptera VM-skalningsuppsättningar med Azure CLI

Använd den [az vmss-kryptering aktiverar](/cli/azure/vmss/encryption#az-vmss-encryption-enable) att aktivera kryptering på en Windows VM-skalningsuppsättning. Om du anger uppgraderingsprincipen för skalningsuppsättningen att manuellt starta kryptering med [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav.

-  **Kryptera en VM-skalningsuppsättning**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Kryptera en aktiv virtuell dator skalningsuppsättning med KEK du omsluter nyckeln**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
- **Hämta krypteringsstatus för en VM-skalningsuppsättning:** Använd [az vmss kryptering show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MySecureRG" --name "MySecureVmss"
    ```

- **Inaktivera kryptering på en VM-skalningsuppsättning**: Använd [az vmss kryptering inaktivera](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MySecureRG" --name "MySecureVmss"
    ```

## <a name="bkmk_VHDpre"> </a>Nya virtuella IaaS-datorer skapas från kund-krypterad VHD och kryptering nycklar
Du kan aktivera kryptering med hjälp av PowerShell-cmdletar eller CLI-kommandona i det här scenariot. 

Följ instruktionerna i tillägget för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan använda du stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbered en förkrypterade Windows virtuell Hårddisk](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Förbereda en förkrypterade Linux-VHD](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzureRmVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Kommandot Set-AzureRmVMDiskEncryptionExtension misslyckas mot virtuella datorer med hanterade diskar baserade tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 


### <a name="bkmk_VHDprePSH"> </a> Kryptera virtuella datorer med förkrypterade virtuella hårddiskar med Azure PowerShell
Du kan aktivera diskkryptering på en krypterad virtuell Hårddisk med hjälp av PowerShell-cmdleten [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). I exemplet nedan visas några vanliga parametrar. 

```azurepowershell-interactive
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan [lägga till en ny disk till en Windows virtuell dator med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md), eller [via Azure portal](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder Powershell för att kryptera en ny disk för virtuella Windows-datorer, måste en ny sekvens-version anges. Sekvens-versionen måste vara unikt. Skriptet nedan genererar ett GUID för sekvens-versionen. I vissa fall kan kan en nyligen tillagd datadisk vara krypterad automatiskt med Azure Disk Encryption-tillägget. Om detta inträffar rekommenderar vi använder cmdleten Set-AzureRmVmDiskEncryptionExtension igen med ny sekvens.
 

-  **Kryptera en aktiv virtuell dator:** skriptet nedan initierar dina variabler och kör cmdleten Set-AzureRmVMDiskEncryptionExtension. Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav. Ersätt MySecureRg och MySecureVM MySecureVault med dina värden. Det här exemplet använder ”alla” för parametern - VolumeType som innehåller både OS- och datavolymer. Om du bara vill kryptera operativsystemvolymen använda ”OS” för parametern - VolumeType. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Kryptera en aktiv virtuell dator med hjälp av KEK:** det här exemplet används ”alla” för parametern - VolumeType som innehåller både OS- och datavolymer. Om du bara vill kryptera operativsystemvolymen använda ”OS” för parametern - VolumeType.

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
 Azure CLI-kommando får automatiskt en ny sekvens-version för dig när du kör kommandot för att aktivera kryptering. I exemplet används ”alla” för parametern volymtyp. Du kan behöva ändra volymtyp parametern till operativsystem om du krypterar endast OS-disken. Till skillnad från Powershell-syntax kräver CLI inte att användaren anger en unik teckensekvens version när du aktiverar kryptering. CLI genererar automatiskt och använder en egen unik teckensekvens värde.   

-  **Kryptera en aktiv virtuell dator:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Kryptera en aktiv virtuell dator med hjälp av KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Inaktivera kryptering
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI, eller med en Resource Manager-mall. Inaktivera data-diskkryptering på virtuella Windows-datorn när både Operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

- **Inaktivera disk encryption med Azure PowerShell:** inaktivera kryptering med den [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Inaktivera kryptering med Azure CLI:** inaktivera kryptering med den [az vm encryption inaktivera](/cli/azure/vm/encryption#az-vm-encryption-disable) kommando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type "all"
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** 

    1. Klicka på **distribuera till Azure** från den [inaktiverar diskkryptering på Windows virtuell dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) mall.
    2. Välj prenumeration, resursgrupp, plats, VM, volymtyp, juridiska villkor och avtal.
    3.  Klicka på **köp** att inaktivera diskkryptering på en aktiv virtuell Windows-dator. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Aktivera Azure Disk Encryption för Linux](azure-security-disk-encryption-linux.md)
