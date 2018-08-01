---
title: Aktivera Azure Disk Encryption för Windows virtuella IaaS-datorer | Microsoft Docs
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure Disk Encryption för Windows virtuella IaaS-datorer.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 8b3905c8-844f-4ec7-ad95-b386e9843053
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 772ab272603d0f8e0badf899c439592b20b7c8a3
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391748"
---
#  <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Aktivera Azure Disk Encryption för Windows virtuella Iaas-datorer 

Du kan aktivera många diskkryptering scenarier och stegen kan variera beroende på scenario. Följande avsnitt beskriver scenarier i större detalj för Windows virtuella IaaS-datorer. Innan du kan använda diskkryptering, den [som krävs för Azure Disk Encryption](/articles/security/azure-security-disk-encryption-prerequisites.md) måste slutföras. 

Ta en [ögonblicksbild](../virtual-machines/windows/snapshot-copy-managed-disk.md) och/eller säkerhetskopierar innan diskar krypteras. Säkerhetskopieringar kan du kontrollera att ett återställningsalternativ är möjligt med ett oväntat fel under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. Du kan använda cmdleten Set-AzureRmVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Mer information om hur du säkerhetskopiera och återställa krypterade virtuella datorer finns i den [Azure Backup](../backup/backup-azure-vms-encryption.md) artikeln. 

>[!WARNING]
>För att se till att kryptering hemligheterna inte går över regionala gränser, måste Azure Disk Encryption Key Vault och de virtuella datorerna för att finnas i samma region. Skapa och använda ett Nyckelvalv som är i samma region som den virtuella datorn måste vara krypterade. 


## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Aktivera kryptering på den nya IaaS-datorer som skapats från Marketplace
Du kan aktivera diskkryptering på nya virtuella IaaS Windows-dator från Marketplace i Azure med hjälp av Resource Manager-mall. Mallen skapar en ny krypterade Windows virtuell dator med Windows Server 2012 galleriet bilden.

1. På den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image), klickar du på **distribuera till Azure**.

2. Välj prenumeration, resursgrupp, resursgruppens plats, parametrar, juridiska villkor och avtal. Klicka på **köp** att distribuera en ny IaaS-VM där kryptering är aktiverat.

3. När du har distribuerat mallen Kontrollera krypteringsstatus VM med önskad metod:
     - Kontrollera med Azure CLI med hjälp av den [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) kommando. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Kontrollera med Azure PowerShell med hjälp av den [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) cmdlet. 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     -  Välj den virtuella datorn, och klicka sedan på **diskar** under den **inställningar** rubrik för att kontrollera status för datakryptering i portalen. I diagrammet under **kryptering**, ser du om den är aktiverad. 
           ![Azure Portal – Disk kryptering aktiverat](./media/azure-security-disk-encryption/disk-encryption-fig2.png) i följande tabell visas Resource Manager-mallens parametrar för nya virtuella datorer från Marketplace-scenario med hjälp av Azure AD-klient-ID:

| Parameter | Beskrivning | 
| --- | --- |
| adminUserName | Administratörsanvändarnamn för den virtuella datorn. |
| adminPassword | Administratörslösenord för den virtuella datorn. |
| newStorageAccountName | Namnet på lagringskontot för att lagra OS- och virtuella hårddiskar. |
| vmSize | Storleken på den virtuella datorn. För närvarande stöds endast Standard A, D och G-serien. |
| virtualNetworkName | Namnet på det virtuella nätverk som VM NIC ska tillhöra. |
| subnetName | Namnet på undernätet i det virtuella nätverket som VM NIC ska tillhöra. |
| AADClientID | Klient-ID för Azure AD-program som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| AADClientSecret | Klienthemlighet för Azure AD-program som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| KeyVaultURL | URL för nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName).VaultURI` eller Azure CLI `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| KeyEncryptionKeyURL | URL för nyckelkrypteringsnyckel som används för att kryptera den genererade BitLocker-nyckeln (valfritt). </br> </br>KeyEncryptionKeyURL är en valfri parameter. Du kan använda din egen KEK att ytterligare skydda krypteringsnyckeln för tjänstdata (hemlig lösenfras) i ditt nyckelvalv. |
| keyVaultResourceGroup | Resursgrupp för nyckelvalvet. |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på. |


## <a name="bkmk_RunningWinVM"></a> Aktivera kryptering på befintliga eller som kör Windows virtuella IaaS-datorer
I det här scenariot kan du aktivera kryptering med hjälp av en mall, PowerShell-cmdletar eller CLI-kommandon. I följande avsnitt beskrivs i detalj hur du aktiverar Azure Disk Encryption. 

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzureRmVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Kommandot Set-AzureRmVMDiskEncryptionExtension misslyckas mot virtuella datorer med hanterade diskar baserade tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Aktivera kryptering på befintliga eller köra virtuella datorer med Azure PowerShell 
Använd den [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) cmdlet för att aktivera kryptering på en aktiv IaaS virtuell dator i Azure. Information om hur du aktiverar kryptering med Azure Disk Encryption med PowerShell-cmdlets finns i blogginläggen [utforska Azure Disk Encryption med Azure PowerShell – del 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) och [utforska Azure Disk Encryption med Azure PowerShell – del 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Kryptera en aktiv virtuell dator med hjälp av en klienthemlighet:** skriptet nedan initierar dina variabler och kör cmdleten Set-AzureRmVMDiskEncryptionExtension. Resursgrupp, virtuell dator, nyckelvalvet, AAD-appar och klienthemlighet bör redan har skapats som krav. Ersätt MySecureRg, MySecureVM, MySecureVault, min-AAD-klient-ID och min-AAD-client-secret med dina värden.
     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Kryptera en aktiv virtuell dator med hjälp av KEK du omsluter klienthemlighet:** Azure Disk Encryption kan du ange en befintlig nyckel i ditt nyckelvalv du omsluter disk encryption hemligheter som genererades vid aktivering av kryptering. När du anger en nyckelkrypteringsnyckel använder Azure Disk Encryption nyckeln för att omsluta kryptering hemligheter innan du skriver till Key Vault. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** för att kontrollera statusen för kryptering av en IaaS-VM, använda den [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) cmdlet. 
     ```azurepowershell-interactive
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **Inaktivera diskkryptering:** inaktivera kryptering med den [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Aktivera kryptering på befintliga eller köra virtuella datorer med Azure CLI
Använd den [az vm encryption aktivera](/cli/azure/vm/encryption#az-vm-encryption-enable) kommando för att aktivera kryptering på en aktiv IaaS virtuell dator i Azure.

-  **Kryptera en aktiv virtuell dator med hjälp av en klienthemlighet:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Kryptera en aktiv virtuell dator med hjälp av KEK du omsluter klienthemlighet:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** för att kontrollera statusen för kryptering av en IaaS-VM, använda den [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) kommando. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Inaktivera kryptering:** inaktivera kryptering med den [az vm encryption inaktivera](/cli/azure/vm/encryption#az-vm-encryption-disable) kommando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzureRmVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Det här kommandot kan utföras mot virtuella datorer med hanterade diskar baserat förrän en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 

### <a name="bkmk_RunningWinVMwRM"> </a>Med hjälp av Resource Manager-mall
Du kan aktivera diskkryptering på befintliga eller kör IaaS Windows-datorer i Azure med hjälp av den [Resource Manager-mall för att kryptera en virtuell dator som körs Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. Klicka på Azure snabbstarts-mall **distribuera till Azure**.

2. Välj prenumeration, resursgrupp, resursgruppens plats, parametrar, juridiska villkor och avtal. Klicka på **köp** att aktivera kryptering på den befintliga eller körs IaaS-VM.

I följande tabell visas Resource Manager-mallens parametrar för befintliga eller som kör virtuella datorer som använder en Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Azure AD-program som har behörighet att skriva hemligheter i key vault. |
| AADClientSecret | Klienthemlighet för Azure AD-program som har behörighet att skriva hemligheter i key vault. |
| keyVaultName | Namnet på nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` eller Azure CLI-kommando ' az keyvault list--resource-group ”MySecureGroup” |Convertfrom-JSON ”|
|  KeyEncryptionKeyURL | URL för nyckelkrypteringsnyckel som används för att kryptera den genererade BitLocker-nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan UseExistingKek. Om du väljer **kek** i listrutan UseExistingKek måste du ange den _keyEncryptionKeyURL_ värde. |
| VolumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS_, _Data_, och _alla_. |
| SequenceVersion | Sekvens-versionen av BitLocker-åtgärden. Öka det här versionsnumret varje gång en diskkryptering åtgärd utförs på samma virtuella dator. |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på. |


## <a name="bkmk_VHDpre"> </a>Nya virtuella IaaS-datorer skapas från kund-krypterad VHD och kryptering nycklar
Du kan aktivera kryptering med hjälp av Resource Manager-mall, PowerShell-cmdletar eller CLI-kommandona i det här scenariot. I följande avsnitt beskrivs mer detaljerat Resource Manager-mall och CLI-kommandon. 

Följ instruktionerna i tillägget för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan använda du stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbered en förkrypterade Windows virtuell Hårddisk](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Förbereda en förkrypterade Linux-VHD](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzureRmVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Kommandot Set-AzureRmVMDiskEncryptionExtension misslyckas mot virtuella datorer med hanterade diskar baserade tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 



### <a name="bkmk_VHDprePSH"> </a> Kryptera virtuella datorer med förkrypterade virtuella hårddiskar med Azure PowerShell
Du kan aktivera diskkryptering på en krypterad virtuell Hårddisk med hjälp av PowerShell-cmdleten [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). I exemplet nedan visas några vanliga parametrar. 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```


### <a name="bkmk_VHDpreRM"> </a>Kryptera virtuella IaaS-datorer med förkrypterade virtuella hårddiskar med en Resource Manager-mall 
Du kan aktivera diskkryptering på en krypterad virtuell Hårddisk med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Klicka på Azure snabbstarts-mall **distribuera till Azure**.

2. Välj prenumeration, resursgrupp, resursgruppens plats, parametrar, juridiska villkor och avtal. Klicka på **skapa** att aktivera kryptering på den nya IaaS-VM.

I följande tabell visas Resource Manager-mallens parametrar för en krypterad virtuell Hårddisk:

| Parameter | Beskrivning |
| --- | --- |
| newStorageAccountName | Namnet på lagringskontot för att lagra den krypterade OS-VHD. Det här lagringskontot ska redan har skapats i samma resursgrupp och samma plats som den virtuella datorn. |
| osVhdUri | URI för OS-VHD: N från storage-kontot. |
| osType | Produkttyp av operativsystem (Windows/Linux). |
| virtualNetworkName | Namnet på det virtuella nätverk som VM NIC ska tillhöra. Namnet bör redan har skapats i samma resursgrupp och samma plats som den virtuella datorn. |
| subnetName | Namnet på undernätet i det virtuella nätverket som VM NIC ska tillhöra. |
| vmSize | Storleken på den virtuella datorn. För närvarande stöds endast Standard A, D och G-serien. |
| keyVaultResourceID | ResourceID som identifierar resursen nyckelvalv i Azure Resource Manager. Du kan hämta den med hjälp av PowerShell-cmdleten `(Get-AzureRmKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` eller med hjälp av Azure CLI-kommando `az keyvault show --name "MySecureVault" --query id`|
| keyVaultSecretUrl | URL för disk-krypteringsnyckeln som har ställts in i nyckelvalvet. |
| keyVaultKekUrl | URL för nyckelkrypteringsnyckel för att kryptera den genererade diskkryptering nyckeln. |
| vmName | Namnet på den virtuella IaaS-datorer. |

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan [lägga till en ny disk till en Windows virtuell dator med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md), eller [via Azure portal](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder Powershell för att kryptera en ny disk för virtuella Windows-datorer, måste en ny sekvens-version anges. Sekvens-versionen måste vara unikt. Skriptet nedan genererar ett GUID för sekvens-versionen. I vissa fall kan en nyligen tillagd datadisk vara krypterad automatiskt med Azure Disk Encryption-tillägget. Om detta inträffar rekommenderar vi använder cmdleten Set-AzureRmVmDiskEncryptionExtension igen med ny sekvens.
 

-  **Kryptera en aktiv virtuell dator med hjälp av en klienthemlighet:** skriptet nedan initierar dina variabler och kör cmdleten Set-AzureRmVMDiskEncryptionExtension. Resursgrupp, virtuell dator, nyckelvalvet, AAD-appar och klienthemlighet bör redan har skapats som krav. Ersätt MySecureRg, MySecureVM, MySecureVault, min-AAD-klient-ID och min-AAD-client-secret med dina värden. Parametern - VolumeType anges till datadiskar och inte OS-disken. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId  –SequenceVersion $sequenceVersion;
    ```
- **Kryptera en aktiv virtuell dator med hjälp av KEK du omsluter klienthemlighet:** Azure Disk Encryption kan du ange en befintlig nyckel i ditt nyckelvalv du omsluter disk encryption hemligheter som genererades vid aktivering av kryptering. När du anger en nyckelkrypteringsnyckel använder Azure Disk Encryption nyckeln för att omsluta kryptering hemligheter innan du skriver till Key Vault. Du kan behöva lägga till parametern - VolumeType om du krypterar datadiskar och inte OS-disken. 

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
 Azure CLI-kommando får automatiskt en ny sekvens-version för dig när du kör kommandot för att aktivera kryptering. 
-  **Kryptera en aktiv virtuell dator med hjälp av en klienthemlighet:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Kryptera en aktiv virtuell dator med hjälp av KEK du omsluter klienthemlighet:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault"--key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Aktivera kryptering med hjälp av Azure AD baserat autentisering av klientcertifikat.
Du kan använda autentisering med klientcertifikat med eller utan KEK. Skripten kräver att [som krävs för Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) har slutförts. Innan du använder PowerShell-skript, bör du redan har certifikatet laddas upp till nyckelvalvet och distribueras till den virtuella datorn. Om du använder KEK för, ska KEK redan finnas. Mer information finns i den [certifikatbaserad autentisering för Azure AD](azure-security-disk-encryption-prerequisites.md#bkmk_Cert) i artikeln krav.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Aktivera kryptering med certifikatbaserad autentisering med Azure PowerShell

```powershell
## Fill in 'MySecureRg', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$rgName = 'MySecureRg';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = ‘MySecureVM’;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Aktivera kryptering med certifikatbaserad autentisering och en KEK med Azure PowerShell

```powershell
# Fill in 'MySecureRg', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$rgName = 'MySecureRg';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Inaktivera kryptering
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI, eller med en Resource Manager-mall. 

- **Inaktivera disk encryption med Azure PowerShell:** inaktivera kryptering med den [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

- **Inaktivera kryptering med Azure CLI:** inaktivera kryptering med den [az vm encryption inaktivera](/cli/azure/vm/encryption#az-vm-encryption-disable) kommando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** 

    1. Klicka på **distribuera till Azure** från den [inaktiverar diskkryptering på Windows virtuell dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) mall.
    2. Välj prenumeration, resursgrupp, plats, VM, juridiska villkor och avtal.
    3.  Klicka på **köp** att inaktivera diskkryptering på en aktiv virtuell Windows-dator. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Aktivera Azure Disk Encryption för Linux](azure-security-disk-encryption-linux.md)