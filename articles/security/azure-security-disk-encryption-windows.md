---
title: Aktivera Azure Disk Encryption för Windows virtuella IaaS-datorer
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure Disk Encryption för Windows virtuella IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: d4cf454a654124468fd31e6412530416da381acf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884905"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Aktivera Azure Disk Encryption för Windows virtuella IaaS-datorer

Du kan aktivera många diskkryptering scenarier och stegen kan variera beroende på scenario. Följande avsnitt beskriver scenarier i större detalj för Windows virtuella IaaS-datorer. Innan du kan använda diskkryptering, den [som krävs för Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites.md) måste slutföras. 

Ta en [ögonblicksbild](../virtual-machines/windows/snapshot-copy-managed-disk.md) och/eller säkerhetskopierar innan diskar krypteras. Säkerhetskopior Se till att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. Du kan använda cmdleten Set-AzVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Mer information om hur du säkerhetskopiera och återställa krypterade virtuella datorer finns i den [Azure Backup](../backup/backup-azure-vms-encryption.md) artikeln. 

>[!WARNING]
> - Om du tidigare har använt [Azure Disk Encryption med Azure AD-app](azure-security-disk-encryption-prerequisites-aad.md) för att kryptera den här virtuella datorn, måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Du kan inte använda [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) på den här krypterade virtuella datorn eftersom det är inte ett scenario som stöds, betydelse växla från AAD-programmet för det här krypterade virtuella datorer stöds inte ännu. 
> - Azure Disk Encryption måste Key Vault och de virtuella datorerna för att finnas i samma region. Skapa och använda ett Nyckelvalv som är i samma region som den virtuella datorn måste vara krypterade. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningWinVM"></a> Aktivera kryptering på befintliga eller som kör Windows virtuella IaaS-datorer
I det här scenariot kan du aktivera kryptering med hjälp av en mall, PowerShell-cmdletar eller CLI-kommandon. I följande avsnitt beskrivs i detalj hur du aktiverar Azure Disk Encryption. Om du behöver information för databasschema för tillägget för virtuell dator finns i den [Azure Disk Encryption för Windows-tillägget](../virtual-machines/extensions/azure-disk-enc-windows.md) artikeln.

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Kommandot Set-AzVMDiskEncryptionExtension misslyckas mot virtuella datorer med hanterade diskar baserade tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Aktivera kryptering på befintliga eller köra virtuella datorer med Azure PowerShell 
Använd den [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet för att aktivera kryptering på en aktiv IaaS virtuell dator i Azure. 

-  **Kryptera en aktiv virtuell dator:** Skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Kryptera en aktiv virtuell dator med hjälp av KEK:** 

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera statusen för kryptering av en IaaS-VM, använda den [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera diskkryptering:** Om du vill inaktivera kryptering, använda den [inaktivera AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. Inaktivera data-diskkryptering på virtuella Windows-datorn när både Operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Aktivera kryptering på befintliga eller köra virtuella datorer med Azure CLI
Använd den [az vm encryption aktivera](/cli/azure/vm/encryption#az-vm-encryption-enable) kommando för att aktivera kryptering på en aktiv IaaS virtuell dator i Azure.

- **Kryptera en aktiv virtuell dator:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Kryptera en aktiv virtuell dator med hjälp av KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera statusen för kryptering av en IaaS-VM, använda den [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) kommando. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Inaktivera kryptering med den [az vm encryption inaktivera](/cli/azure/vm/encryption#az-vm-encryption-disable) kommando. Inaktivera data-diskkryptering på virtuella Windows-datorn när både Operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
 
  > [!NOTE]
  >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Det här kommandot kan utföras mot virtuella datorer med hanterade diskar baserat förrän en säkerhetskopia har gjorts och den här parametern har angetts. 
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
| keyVaultName | Namnet på nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` eller Azure CLI-kommando `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Namnet på resursgruppen som innehåller nyckelvalvet|
|  KeyEncryptionKeyURL | URL för nyckelkrypteringsnyckel som används för att kryptera den genererade BitLocker-nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan UseExistingKek. Om du väljer **kek** i listrutan UseExistingKek måste du ange den _keyEncryptionKeyURL_ värde. |
| VolumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS_, _Data_, och _alla_. 
| forceUpdateTag | Skicka in ett unikt värde som en GUID varje gång åtgärden måste vara tvinga kör. |
| resizeOSDisk | Vill du ändra storlek OS-partition för att uppta fullständig OS-VHD innan du delar systemvolym. |
| location | Plats för alla resurser. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Kryptera VM-skalningsuppsättningar

[Azure VM-skalningsuppsättningar](../virtual-machine-scale-sets/overview.md) kan du skapa och hantera grupper med identiska, belastningsutjämnade virtuella datorer. Antal VM-instanser kan automatiskt öka eller minska som svar på efterfrågan eller ett definierat schema. Använda CLI eller Azure PowerShell för att kryptera VM-skalningsuppsättningar.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Kryptera VM-skalningsuppsättningar med Azure PowerShell

Använd den [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) cmdlet för att aktivera kryptering på en Windows VM-skalningsuppsättning. Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav.

-  **Kryptera en VM-skalningsuppsättning**:
    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGame -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     Set-AzRmVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGame -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

   >[!NOTE]
   > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Hämta krypteringsstatus för en VM-skalningsuppsättning:** Använd den [Get-AzVmssDiskEncryption](/powershell/module/az.compute/get-azvmssdiskencryption) cmdlet.
    
    ```azurepowershell-interactive
    get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Inaktivera kryptering på en VM-skalningsuppsättning**: Använd den [inaktivera AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) cmdlet. 

    ```azurepowershell-interactive
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Kryptera VM-skalningsuppsättningar med Azure CLI

Använd den [az vmss-kryptering aktiverar](/cli/azure/vmss/encryption#az-vmss-encryption-enable) att aktivera kryptering på en Windows VM-skalningsuppsättning. Om du anger uppgraderingsprincipen för skalningsuppsättningen att manuellt starta kryptering med [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav.

-  **Kryptera en VM-skalningsuppsättning**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Kryptera en aktiv virtuell dator skalningsuppsättning med KEK du omsluter nyckeln**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
     
   >[!NOTE]
   > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Hämta krypteringsstatus för en VM-skalningsuppsättning:** Använd [az vmss kryptering show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Inaktivera kryptering på en VM-skalningsuppsättning**: Använd [az vmss kryptering inaktivera](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Anger om Azure Resource Manager-mallar för Windows VM-skalningsuppsättning

Om du vill kryptera eller dekryptera Windows VM-skalningsuppsättning genom att använda Azure Resource Manager-mallar och anvisningarna nedan:

- [Aktivera kryptering på en Windows VM-skalningsuppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Distribuera en VM-skalningsuppsättning med virtuella Windows-datorer med en jumpbox och aktivera kryptering på Windows VM scale Sets](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)
- [Inaktivera kryptering på en Windows VM scale Sets](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. Klicka på **Distribuera till Azure**.
     2. Fyll i de obligatoriska fälten och samtycker till villkoren.
     3. Klicka på **köp** att distribuera mallen.

## <a name="bkmk_VHDpre"> </a> Nya virtuella IaaS-datorer skapas från kund-krypterad VHD och kryptering nycklar

Du kan aktivera kryptering med hjälp av PowerShell-cmdletar eller CLI-kommandona i det här scenariot. 

Följ instruktionerna i tillägget för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan använda du stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbered en förkrypterade Windows virtuell Hårddisk](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Förbereda en förkrypterade Linux-VHD](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Kommandot Set-AzVMDiskEncryptionExtension misslyckas mot virtuella datorer med hanterade diskar baserade tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 


### <a name="bkmk_VHDprePSH"> </a> Kryptera virtuella datorer med förkrypterade virtuella hårddiskar med Azure PowerShell
Du kan aktivera diskkryptering på en krypterad virtuell Hårddisk med hjälp av PowerShell-cmdleten [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). I exemplet nedan visas några vanliga parametrar. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan [lägga till en ny disk till en Windows virtuell dator med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md), eller [via Azure portal](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder Powershell för att kryptera en ny disk för virtuella Windows-datorer, måste en ny sekvens-version anges. Sekvens-versionen måste vara unikt. Skriptet nedan genererar ett GUID för sekvens-versionen. I vissa fall kan kan en nyligen tillagd datadisk vara krypterad automatiskt med Azure Disk Encryption-tillägget. Automatisk kryptering uppstår vanligen när den virtuella datorn startas om när den nya disken är online. Detta beror vanligtvis på att ”alla” har angetts för typ av volym när diskkryptering körde tidigare på den virtuella datorn. Om automatisk kryptering sker i en nyligen tillagd datadisk, rekommenderar vi använder cmdleten Set-AzVmDiskEncryptionExtension igen med ny sekvens. Om din nya datadisk är auto krypterad och du inte vill ska krypteras, dekryptera alla enheter först och sedan kryptera med en ny sekvens version att Operativsystemet för typ av volym. 
  
 

-  **Kryptera en aktiv virtuell dator:** Skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden. Det här exemplet använder ”alla” för parametern - VolumeType som innehåller både OS- och datavolymer. Om du bara vill kryptera operativsystemvolymen använda ”OS” för parametern - VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Kryptera en aktiv virtuell dator med hjälp av KEK:** Det här exemplet använder ”alla” för parametern - VolumeType som innehåller både OS- och datavolymer. Om du bara vill kryptera operativsystemvolymen använda ”OS” för parametern - VolumeType.

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
 Azure CLI-kommando får automatiskt en ny sekvens-version för dig när du kör kommandot för att aktivera kryptering. I exemplet används ”alla” för parametern volymtyp. Du kan behöva ändra volymtyp parametern till operativsystem om du krypterar endast OS-disken. Till skillnad från Powershell-syntax kräver CLI inte att användaren anger en unik teckensekvens version när du aktiverar kryptering. CLI genererar automatiskt och använder en egen unik teckensekvens värde.   

-  **Kryptera en aktiv virtuell dator:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Kryptera en aktiv virtuell dator med hjälp av KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Inaktivera kryptering
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI, eller med en Resource Manager-mall. Inaktivera data-diskkryptering på virtuella Windows-datorn när både Operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

- **Inaktivera disk encryption med Azure PowerShell:** Om du vill inaktivera kryptering, använda den [inaktivera AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Inaktivera kryptering med Azure CLI:** Inaktivera kryptering med den [az vm encryption inaktivera](/cli/azure/vm/encryption#az-vm-encryption-disable) kommando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** 

    1. Klicka på **distribuera till Azure** från den [inaktiverar diskkryptering på Windows virtuell dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) mall.
    2. Välj prenumeration, resursgrupp, plats, VM, volymtyp, juridiska villkor och avtal.
    3.  Klicka på **köp** att inaktivera diskkryptering på en aktiv virtuell Windows-dator. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Aktivera Azure Disk Encryption för Linux](azure-security-disk-encryption-linux.md)
