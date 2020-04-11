---
title: Azure Disk Encryption-scenarier på virtuella Windows-datorer
description: Den här artikeln innehåller instruktioner om hur du aktiverar Microsoft Azure Disk Encryption för Virtuella Windows-datorer för olika scenarier
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: f7b6e667df95d9279ad5c44caa4ba33a17909935
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113146"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Azure Disk Encryption-scenarier på virtuella Windows-datorer

Azure Disk Encryption för virtuella Datorer i Windows använder bitlockerfunktionen i Windows för att tillhandahålla fullständig diskkryptering av OS-disken och datadisken. Dessutom ger det kryptering av den tillfälliga resursdisken när parametern VolumeType är Allt.

Azure Disk Encryption är [integrerad med Azure Key Vault](disk-encryption-key-vault.md) för att hjälpa dig att kontrollera och hantera diskkrypteringsnycklar och hemligheter. En översikt över tjänsten finns i [Azure Disk Encryption för virtuella datorer i Windows](disk-encryption-overview.md).

Du kan bara använda diskkryptering på virtuella datorer med [vm-storlekar och operativsystem som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems). Du måste också uppfylla följande förutsättningar:

- [Krav på nätverk](disk-encryption-overview.md#networking-requirements)
- [Krav på grupprincip](disk-encryption-overview.md#group-policy-requirements)
- [Lagringskrav för krypteringsnyckel](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera din virtuella dator. Mer information finns i [Azure Disk Encryption med Azure AD (tidigare version).](disk-encryption-overview-aad.md) 
>
> - Du bör [ta en ögonblicksbild](snapshot-copy-managed-disk.md) och/eller skapa en säkerhetskopia innan diskarna krypteras. Säkerhetskopior säkerställer att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. När en säkerhetskopia har gjorts kan du använda [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Mer information om hur du säkerhetskopierar och återställer krypterade virtuella datorer finns i [Säkerhetskopiera och återställa krypterad Azure VM](../../backup/backup-azure-vms-encryption.md). 
>
> - Om du krypterar eller inaktiverar kryptering kan en virtuell dator startas om.

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och ansluta till Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Aktivera kryptering på en befintlig eller körad Windows VM
I det här fallet kan du aktivera kryptering med hjälp av Resource Manager-mallen, PowerShell-cmdlets eller CLI-kommandon. Om du behöver schemainformation för tillägget för den virtuella datorn läser du artikeln [Azure Disk Encryption for Windows-tillägg.](../extensions/azure-disk-enc-windows.md)

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Aktivera kryptering på befintliga eller krävda virtuella datorer med Azure PowerShell 
Använd [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell IaaS-dator i Azure. 

-  **Kryptera en virtuell dator som körs:** Skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Resursgruppen, den virtuella datorn och nyckelvalvet borde redan ha skapats som förutsättningar. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden.

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
- **Kryptera en virtuell dator som körs med KEK:** 

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
   > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI till KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatus för en virtuell IaaS-dator använder du cmdleten [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera diskkryptering:** Om du vill inaktivera krypteringen använder du cmdleten [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Att inaktivera kryptering av datadiskar på Windows-datorer när både operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Aktivera kryptering på befintliga eller krävd virtuella datorer med Azure CLI
Använd kommandot [az vm-kryptering aktivera](/cli/azure/vm/encryption#az-vm-encryption-enable) för att aktivera kryptering på en virtuell IaaS-dator i Azure.

- **Kryptera en virtuell dator som körs:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Kryptera en virtuell dator som körs med KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI till KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatusen för en virtuell virtuell dator iAAS använder du kommandot [az vm-krypteringsvisning.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du kommandot [az vm-kryptering inaktivera.](/cli/azure/vm/encryption#az-vm-encryption-disable) Att inaktivera kryptering av datadiskar på Windows-datorer när både operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Använda mallen Resurshanteraren

Du kan aktivera diskkryptering på befintliga eller körande virtuella IaaS Windows-datorer i Azure genom att använda [resource manager-mallen för att kryptera en windows-virtuell dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. Klicka på Distribuera till **Azure**i snabbstartsmallen i Azure .

2. Välj prenumeration, resursgrupp, plats, inställningar, juridiska villkor och avtal. Klicka på **Köp** om du vill aktivera kryptering på den befintliga eller körande virtuella virtuella datorn för IaaS.

I följande tabell visas mallparametrarna för Resurshanteraren för befintliga eller pågående virtuella datorer:

| Parameter | Beskrivning |
| --- | --- |
| vmName | Namn på den virtuella datorn för att köra krypteringsåtgärden. |
| keyVaultName | Namn på nyckelvalvet som BitLocker-nyckeln ska överföras till. Du kan hämta den med `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` kommandot cmdlet eller Azure CLI`az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Namn på resursgruppen som innehåller nyckelvalvet|
|  keyEncryptionKeyURL | URL:en för nyckelkrypteringsnyckeln i&lt;formatet https://&gt;keyvault-name&lt;.vault.azure.net/key/&gt;nyckelnamn . Om du inte vill använda en KEK lämnar du det här fältet tomt. |
| volumeType (volumeType) | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS,_ _Data_och _Alla_. 
| forceUpdateTag | Skicka in ett unikt värde som ett GUID varje gång operationen måste tvångskörning. |
| ändra storlek påOSDisk | Bör OS-partitionen storleksas för att uppta full OS VHD innan du delar systemvolymen. |
| location | Plats för alla resurser. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nya virtuella IaaS-datorer som skapats från kundkrypterade virtuella hårddiskar och krypteringsnycklar

I det här fallet kan du skapa en ny virtuell dator från en förkrypterad virtuell hårddisk och tillhörande krypteringsnycklar med PowerShell-cmdlets eller CLI-kommandon. 

Använd instruktionerna i [Förbereda en förkrypterad Windows VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). När avbildningen har skapats kan du använda stegen i nästa avsnitt för att skapa en krypterad Azure VM.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Kryptera virtuella datorer med förkrypterade virtuella hårddiskar med Azure PowerShell
Du kan aktivera diskkryptering på din krypterade virtuella hårddisk med hjälp av PowerShell cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Exemplet nedan ger dig några vanliga parametrar. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan [lägga till en ny disk i en Windows VM med PowerShell](attach-disk-ps.md)eller [via Azure-portalen](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När powershell-data för att kryptera en ny disk för virtuella Windows-datorer kan en ny sekvensversion anges. Sekvensversionen måste vara unik. Skriptet nedan genererar ett GUID för sekvensversionen. I vissa fall kan en nyligen tillagd datadisk krypteras automatiskt av Azure Disk Encryption-tillägget. Automatisk kryptering inträffar vanligtvis när den virtuella datorn startas om när den nya disken är online. Detta beror vanligtvis på att "Alla" angavs för volymtypen när diskkryptering tidigare kördes på den virtuella datorn. Om automatisk kryptering sker på en nyligen tillagd datadisk rekommenderar vi att du kör cmdleten Set-AzVmDiskEncryptionExtension igen med ny sekvensversion. Om den nya datadisken är automatiskt krypterad och du inte vill krypteras, dekryptera alla enheter först och sedan kryptera med en ny sekvensversion som anger OS för volymtypen. 
  
 

-  **Kryptera en virtuell dator som körs:** Skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Resursgruppen, den virtuella datorn och nyckelvalvet borde redan ha skapats som förutsättningar. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden. I det här exemplet används "Alla" för parametern -VolumeType, som innehåller både OS- och Datavolymer. Om du bara vill kryptera OS-volymen använder du "OS" för parametern -VolumeType. 

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
- **Kryptera en virtuell dator som körs med KEK:** I det här exemplet används "Alla" för parametern -VolumeType, som innehåller både OS- och Datavolymer. Om du bara vill kryptera OS-volymen använder du "OS" för parametern -VolumeType.

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
    > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI till KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
 Azure CLI-kommandot tillhandahåller automatiskt en ny sekvensversion åt dig när du kör kommandot för att aktivera kryptering. I exemplet används "Alla" för parametern för volymtyp. Du kan behöva ändra parametern för volymtyp till OPERATIVSYSTEMET om du bara krypterar OS-disken. I motsats till Powershell-syntaxen kräver CLI inte att användaren tillhandahåller en unik sekvensversion när kryptering aktiveras. CLI genererar och använder automatiskt sitt eget unika sekvensversionsvärde.   

-  **Kryptera en virtuell dator som körs:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Kryptera en virtuell dator som körs med KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Inaktivera kryptering
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI eller med en Resource Manager-mall. Att inaktivera kryptering av datadiskar på Windows-datorer när både operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

- **Inaktivera diskkryptering med Azure PowerShell:** Om du vill inaktivera krypteringen använder du cmdleten [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du kommandot [az vm-kryptering inaktivera.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** 

    1. Klicka på **Distribuera till Azure** från inaktivera [diskkryptering på windows VM-mallen.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
    2. Välj prenumeration, resursgrupp, plats, virtuell dator, volymtyp, juridiska termer och avtal.
    3.  Klicka på **Köp** om du vill inaktivera diskkryptering på en windows-virtuell dator som körs. 

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Azure Disk Encryption fungerar inte för följande scenarier, funktioner och teknik:

- Kryptera grundläggande virtuell nivå eller virtuella datorer som skapats via den klassiska metoden för att skapa virtuella datorer.
- Kryptera virtuella datorer som konfigurerats med programvarubaserade RAID-system.
- Kryptera virtuella datorer som konfigurerats med S2D (Storage Spaces Direct) eller Windows Server före 2016 som konfigurerats med Windows Storage Spaces.
- Integration med ett lokalt nyckelhanteringssystem.
- Azure-filer (delat filsystem).
- NFS (Network File System).
- Dynamiska volymer.
- Windows Server-behållare, som skapar dynamiska volymer för varje behållare.
- Efemära OPERATIVSYSTEM diskar.
- Kryptering av delade/distribuerade filsystem som (men inte begränsat till) DFS, GFS, DRDB och CephFS.
- Flytta en krypterad virtuella datorer till en annan prenumeration.
- Gen2 virtuella datorer (se: [Stöd för virtuella generationer på Azure )](generation-2.md#generation-1-vs-generation-2-capabilities)
- Virtuella datorer i Lsv2-serien (se: [Lsv2-serien](../lsv2-series.md))

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Disk-kryptering](disk-encryption-overview.md)
- [Exempelskript för Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Felsökning för Azure Disk Encryption](disk-encryption-troubleshooting.md)
