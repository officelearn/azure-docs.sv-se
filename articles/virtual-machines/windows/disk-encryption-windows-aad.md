---
title: Azure Disk Encryption med Azure AD för Virtuella Windows-datorer (tidigare version)
description: Den här artikeln innehåller instruktioner om hur du aktiverar Microsoft Azure Disk Encryption för virtuella datorer med Windows IaaS.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8677fa2620c1edc646dcffe120938f03fd13a0e5
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085629"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption med Azure AD för Virtuella Windows-datorer (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure AD-programparameter för att aktivera VM-diskkryptering. Med den nya versionen behöver du inte längre ange Azure AD-autentiseringsuppgifter under aktivera krypteringssteget. Alla nya virtuella datorer måste krypteras utan Azure AD-programparametrar med den nya versionen. Information om hur du visar instruktioner för att aktivera VM-diskkryptering med den nya versionen finns i [Azure Disk Encryption for Windows VMS](disk-encryption-windows.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att underhållas med AAD-syntaxen.**


Du kan aktivera många diskkrypteringsscenarier och stegen kan variera beroende på scenariot. Följande avsnitt beskriver scenarierna mer i detalj för virtuella datorer med Windows IaaS. Innan du kan använda diskkryptering måste [förutsättningarna för Azure Disk Encryption slutföras.](disk-encryption-overview-aad.md) 


>[!IMPORTANT]
> - Du bör [ta en ögonblicksbild](snapshot-copy-managed-disk.md) och/eller skapa en säkerhetskopia innan diskarna krypteras. Säkerhetskopior säkerställer att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. När en säkerhetskopia har gjorts kan du använda [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Mer information om hur du säkerhetskopierar och återställer krypterade virtuella datorer finns i [Säkerhetskopiera och återställa krypterad Azure VM](../../backup/backup-azure-vms-encryption.md). 
>
> - Om du krypterar eller inaktiverar kryptering kan en virtuell dator startas om.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Aktivera kryptering på nya virtuella IaaS-datorer som skapats från Marketplace
Du kan aktivera diskkryptering på ny IaaS Windows VM från Marketplace i Azure med hjälp av en Resource Manager-mall. Mallen skapar en ny krypterad Windows-vm med hjälp av galleriavbildningen i Windows Server 2012.

1. Klicka på **Distribuera till Azure**i [mallen Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

2. Välj prenumeration, resursgrupp, resursgruppsplats, parametrar, juridiska termer och avtal. Klicka på **Köp** om du vill distribuera en ny IaaS-vm där kryptering är aktiverad.

3. När du har distribuerat mallen kontrollerar du vm-krypteringsstatusen med den metod du föredrar:
     - Verifiera med Azure CLI med kommandot [az vm-kryptering.](/cli/azure/vm/encryption#az-vm-encryption-show) 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Verifiera med Azure PowerShell med hjälp av cmdleten [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Välj den virtuella datorn och klicka sedan på **Diskar** under rubriken **Inställningar** för att verifiera krypteringsstatus i portalen. I diagrammet under **Kryptering**ser du om det är aktiverat. 
           ![Azure portal - Diskkryptering aktiverad](../media/disk-encryption/disk-encryption-fig2.png)

I följande tabell visas mallparametrarna för Resource Manager för nya virtuella datorer från Marketplace-scenariot med Hjälp av Azure AD-klient-ID:

| Parameter | Beskrivning | 
| --- | --- |
| adminUserName | Administratörsanvändarnamn för den virtuella datorn. |
| adminPassword | Administratörsanvändarlösenord för den virtuella datorn. |
| newStorageAccountName | Namn på lagringskontot för att lagra OS och virtuella data-hårddiskar. |
| vmSize | Storleken på den virtuella datorn. För närvarande stöds endast Standard A- och D-serien. |
| virtualNetworkName | Namn på det virtuella nätverk som vm-nätverkskortet ska tillhöra. |
| undernätNamn | Namnet på det undernät i det virtuella nätverk som vm-nätverkskortet ska tillhöra. |
| AADClientID | Klient-ID för Azure AD-programmet som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| AADClientSecret | Klienthemligheten för Azure AD-programmet som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| keyVaultURL | URL för nyckelvalvet som BitLocker-tangenten ska överföras till. Du kan få den med `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` hjälp av cmdlet eller Azure CLI`az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | URL för nyckelkrypteringsnyckeln som används för att kryptera den genererade BitLocker-nyckeln (valfritt). </br> </br>KeyEncryptionKeyURL är en valfri parameter. Du kan ta med din egen KEK för att ytterligare skydda datakrypteringsnyckeln (Lösenfrashemlighet) i ditt nyckelvalv. |
| keyVaultResourceGroup | Resursgruppen för nyckelvalvet. |
| vmName | Namn på den virtuella dator som krypteringsåtgärden ska utföras på. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a>Aktivera kryptering på befintliga eller körande virtuella IaaS Windows-datorer
I det här fallet kan du aktivera kryptering med hjälp av en mall, PowerShell-cmdlets eller CLI-kommandon. I följande avsnitt beskrivs mer i detalj hur du aktiverar Azure Disk Encryption. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a>Aktivera kryptering på befintliga eller krävda virtuella datorer med Azure PowerShell 
Använd [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell IaaS-dator i Azure. Information om hur du aktiverar kryptering med Azure Disk-kryptering med PowerShell-cmdletar finns i blogginläggen [Explore Azure Disk Encryption with Azure PowerShell - Part 1](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) och Explore Azure Disk Encryption with Azure [PowerShell - Part 2](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:** Skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Resursgruppen, VM, nyckelvalv, AAD-app och klienthemlighet borde redan ha skapats som förutsättningar. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID och My-AAD-client-secret med dina värden.
     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Kryptera en virtuell dator som körs med KEK för att slå in klienthemligheten:** Med Azure Disk Encryption kan du ange en befintlig nyckel i nyckelvalvet för att radbrytas diskkrypteringshemligheter som genererades när kryptering aktiverades. När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption den nyckeln för att radbrytas krypteringshemligheterna innan du skriver till Key Vault. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI till KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatus för en virtuell IaaS-dator använder du cmdleten [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera diskkryptering:** Om du vill inaktivera krypteringen använder du cmdleten [Disable-AzureMMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Aktivera kryptering på befintliga eller krävd virtuella datorer med Azure CLI
Använd kommandot [az vm-kryptering aktivera](/cli/azure/vm/encryption#az-vm-encryption-enable) för att aktivera kryptering på en virtuell IaaS-dator i Azure.

- **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Kryptera en virtuell dator som körs med KEK för att slå in klienthemligheten:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI till KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatusen för en virtuell virtuell dator iAAS använder du kommandot [az vm-krypteringsvisning.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du kommandot [az vm-kryptering inaktivera.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Använda mallen Resurshanteraren
Du kan aktivera diskkryptering på befintliga eller körande virtuella IaaS Windows-datorer i Azure genom att använda [resource manager-mallen för att kryptera en windows-virtuell dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. Klicka på Distribuera till **Azure**i snabbstartsmallen i Azure .

2. Välj prenumeration, resursgrupp, resursgruppsplats, parametrar, juridiska termer och avtal. Klicka på **Köp** om du vill aktivera kryptering på den befintliga eller körande virtuella virtuella datorn för IaaS.

I följande tabell visas resource manager-mallparametrarna för befintliga eller virtuella datorer som använder ett Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Azure AD-programmet som har behörighet att skriva hemligheter till nyckelvalvet. |
| AADClientSecret | Klienthemligheten för Azure AD-programmet som har behörighet att skriva hemligheter till nyckelvalvet. |
| keyVaultName | Namn på nyckelvalvet som BitLocker-nyckeln ska överföras till. Du kan hämta den med `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` kommandot cmdlet eller Azure CLI`az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | URL för nyckelkrypteringsnyckeln som används för att kryptera den genererade BitLocker-nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan AnvändExistingKek. Om du väljer **kek** i listrutan AnvändExistingKek måste du ange _keyEncryptionKeyURL-värdet._ |
| volumeType (volumeType) | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS,_ _Data_och _Alla_. |
| sequenceVersion (sequenceVersion) | Sekvensversion av BitLocker-åtgärden. Öka det här versionsnumret varje gång en diskkrypteringsåtgärd utförs på samma virtuella dator. |
| vmName | Namn på den virtuella dator som krypteringsåtgärden ska utföras på. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Nya virtuella IaaS-datorer som skapats från kundkrypterade virtuella hårddiskar och krypteringsnycklar
I det här fallet kan du aktivera kryptering med hjälp av Resource Manager-mallen, PowerShell-cmdlets eller CLI-kommandon. I följande avsnitt beskrivs resurshanterarens mall och CLI-kommandon mer i detalj. 

Använd instruktionerna i bilagan för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan du använda stegen i nästa avsnitt för att skapa en krypterad Azure VM.

* [Förbereda en förkrypterad Windows VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"> </a> Kryptera virtuella datorer med förkrypterade virtuella hårddiskar med Azure PowerShell
Du kan aktivera diskkryptering på din krypterade virtuella hårddisk med hjälp av PowerShell cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Exemplet nedan ger dig några vanliga parametrar. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan [lägga till en ny disk i en Windows VM med PowerShell](attach-disk-ps.md)eller [via Azure-portalen](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När powershell-data för att kryptera en ny disk för virtuella Windows-datorer kan en ny sekvensversion anges. Sekvensversionen måste vara unik. Skriptet nedan genererar ett GUID för sekvensversionen. I vissa fall kan en nyligen tillagd datadisk krypteras automatiskt av Azure Disk Encryption-tillägget. Automatisk kryptering inträffar vanligtvis när den virtuella datorn startas om när den nya disken är online. Detta beror vanligtvis på att "Alla" angavs för volymtypen när diskkryptering tidigare kördes på den virtuella datorn. Om automatisk kryptering sker på en nyligen tillagd datadisk rekommenderar vi att du kör cmdleten Set-AzVmDiskEncryptionExtension igen med ny sekvensversion. Om den nya datadisken är automatiskt krypterad och du inte vill krypteras, dekryptera alla enheter först och sedan kryptera med en ny sekvensversion som anger OS för volymtypen. 
 

-  **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:** Skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Resursgruppen, VM, nyckelvalv, AAD-app och klienthemlighet borde redan ha skapats som förutsättningar. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID och My-AAD-client-secret med dina värden. I det här exemplet används "Alla" för parametern -VolumeType, som innehåller både OS- och Datavolymer. Om du bara vill kryptera OS-volymen använder du "OS" för parametern -VolumeType. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **Kryptera en virtuell dator som körs med KEK för att slå in klienthemligheten:** Med Azure Disk Encryption kan du ange en befintlig nyckel i nyckelvalvet för att radbrytas diskkrypteringshemligheter som genererades när kryptering aktiverades. När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption den nyckeln för att radbrytas krypteringshemligheterna innan du skriver till Key Vault. I det här exemplet används "Alla" för parametern -VolumeType, som innehåller både OS- och Datavolymer. Om du bara vill kryptera OS-volymen använder du "OS" för parametern -VolumeType. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI till KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
  Azure CLI-kommandot tillhandahåller automatiskt en ny sekvensversion åt dig när du kör kommandot för att aktivera kryptering. Godtagbara värden för parametern volume-yype är Alla, OS och Data. Du kan behöva ändra parametern för volymtyp till operativsystem eller data om du bara krypterar en typ av disk för den virtuella datorn. Exemplen använder "Alla" för parametern för volymtyp. 

-  **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Kryptera en virtuell dator som körs med KEK för att slå in klienthemligheten:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Aktivera kryptering med Azure AD-klientcertifikatbaserad autentisering.
Du kan använda klientcertifikatautentisering med eller utan KEK. Innan du använder PowerShell-skripten bör du redan ha certifikatet överfört till nyckelvalvet och distribueras till den virtuella datorn. Om du använder KEK också, bör KEK redan finns. Mer information finns i avsnittet [Certifikatbaserad autentisering för Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) i artikel om förutsättningar.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Aktivera kryptering med certifikatbaserad autentisering med Azure PowerShell

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Aktivera kryptering med certifikatbaserad autentisering och en KEK med Azure PowerShell

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Inaktivera kryptering
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI eller med en Resource Manager-mall. 

- **Inaktivera diskkryptering med Azure PowerShell:** Om du vill inaktivera krypteringen använder du cmdleten [Disable-AzureMMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du kommandot [az vm-kryptering inaktivera.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** 

    1. Klicka på **Distribuera till Azure** från inaktivera [diskkryptering på windows VM-mallen.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)
    2. Välj prenumeration, resursgrupp, plats, virtuell dator, juridiska termer och avtal.
    3.  Klicka på **Köp** om du vill inaktivera diskkryptering på en windows-virtuell dator som körs. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt över Azure Disk-kryptering](disk-encryption-overview.md)
