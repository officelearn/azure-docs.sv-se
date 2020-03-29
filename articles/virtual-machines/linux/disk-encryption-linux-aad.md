---
title: Azure Disk Kryptering med Azure AD App Linux IaaS virtuella datorer (tidigare utgåva)
description: Den här artikeln innehåller instruktioner om hur du aktiverar Microsoft Azure Disk Encryption för virtuella Linux IaAS-datorer.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: ee365d37a957350fa8a68da0f34149d3210d6238
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970613"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Aktivera Azure Disk Encryption med Azure AD på virtuella Linux-datorer (tidigare version)

Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure Active Directory (Azure AD) programparameter för att aktivera VM-diskkryptering. Med den nya versionen behöver du inte längre ange Azure AD-autentiseringsuppgifter under aktivera krypteringssteget. Alla nya virtuella datorer måste krypteras utan Azure AD-programparametrarna med hjälp av den nya versionen. Instruktioner om hur du aktiverar VM-diskkryptering med den nya versionen finns i [Azure Disk Encryption för Linux VMS](disk-encryption-linux.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att underhållas med AAD-syntaxen.

Du kan aktivera många diskkrypteringsscenarier och stegen kan variera beroende på scenariot. Följande avsnitt täcker scenarierna mer i detalj för virtuella Linux-infrastruktur som en tjänst (IaaS) virtuella datorer. Du kan bara använda diskkryptering på virtuella datorer med [vm-storlekar och operativsystem som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems). Du måste också uppfylla följande förutsättningar:

- [Ytterligare krav för virtuella datorer](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Nätverk och grupprinciper](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Lagringskrav för krypteringsnyckel](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Ta en [ögonblicksbild,](snapshot-copy-managed-disk.md)gör en säkerhetskopia eller båda innan du krypterar diskarna. Säkerhetskopior säkerställer att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. När en säkerhetskopia har gjorts kan du använda cmdleten Set-AzVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Mer information om hur du säkerhetskopierar och återställer krypterade virtuella datorer finns i [Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Om du tidigare använde [Azure Disk Encryption med Azure AD-appen](disk-encryption-overview-aad.md) för att kryptera den här virtuella datorn måste du fortsätta att använda det här alternativet för att kryptera din virtuella dator. Du kan inte använda [Azure Disk Encryption](disk-encryption-overview.md) på den här krypterade virtuella datorn eftersom detta inte är ett scenario som stöds, vilket innebär att det inte stöds att byta från Azure AD-programmet för den här krypterade virtuella datorn ännu.
 > - För att se till att krypteringshemligheterna inte korsar regionala gränser behöver Azure Disk Encryption nyckelvalvet och de virtuella datorerna som ska samlokalgöras i samma region. Skapa och använd ett nyckelvalv som finns i samma region som den virtuella datorn som ska krypteras.
 > - När du krypterar Linux OS-volymer kan processen ta några timmar. Det är normalt att Linux OS-volymer tar längre tid än datavolymer att kryptera.
> - När du krypterar Linux OS-volymer bör den virtuella datorn anses vara otillgänglig. Vi rekommenderar starkt att du undviker SSH-inloggningar medan krypteringen pågår för att undvika att blockera öppna filer som måste nås under krypteringsprocessen. Om du vill kontrollera förloppet använder du kommandona [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) eller [vm-kryptering.](/cli/azure/vm/encryption#az-vm-encryption-show) Du kan förvänta dig att den här processen tar några timmar för en os-volym på 30 GB, plus ytterligare tid för kryptering av datavolymer. Krypteringstiden för datavolym är proportionell mot datavolymernas storlek och kvantitet om inte **alternativet kryptera format alla** används. 
 > - Inaktivera kryptering på virtuella Linux-datorer stöds endast för datavolymer. Det stöds inte på data- eller OS-volymer om OS-volymen har krypterats. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> Aktivera kryptering på en befintlig eller körande IaaS Linux VM

I det här fallet kan du aktivera kryptering med hjälp av Azure Resource Manager-mallen, PowerShell-cmdlets eller Azure CLI-kommandon. 

>[!IMPORTANT]
 >Det är obligatoriskt att ta en ögonblicksbild eller säkerhetskopiera en hanterad diskbaserad VM-instans utanför och innan Azure Disk Encryption aktiveras. Du kan ta en ögonblicksbild av den hanterade disken från Azure-portalen eller använda [Azure Backup](../../backup/backup-azure-vms-encryption.md). Säkerhetskopior säkerställer att ett återställningsalternativ är möjligt vid oväntade fel under krypteringen. När en säkerhetskopia har gjorts använder du cmdleten Set-AzVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Set-AzVMDiskEncryptionExtension-kommandot misslyckas med hanterade diskbaserade virtuella datorer tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Om du krypterar eller inaktiverar kryptering kan den virtuella datorn startas om. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Aktivera kryptering på en befintlig eller körande Linux-virtuell dator med hjälp av Azure CLI 
Du kan aktivera diskkryptering på din krypterade virtuella hårddisk genom att installera och använda kommandoradsverktyget [Azure CLI 2.0.](/cli/azure) Du kan använda det i webbläsaren med [Azure Cloud Shell](../../cloud-shell/overview.md) eller installera det på din lokala dator och använda det i PowerShell-sessioner. Om du vill aktivera kryptering på befintliga eller körande virtuella IaaS Linux-datorer i Azure använder du följande CLI-kommandon:

Använd kommandot [az vm-kryptering aktivera](/cli/azure/vm/encryption#az-vm-encryption-enable) för att aktivera kryptering på en virtuell IaaS-dator i Azure.

-  **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Kryptera en virtuell dator som körs med KEK för att slå in klienthemligheten:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI till KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatusen för en virtuell virtuell dator iAAS använder du kommandot [az vm-krypteringsvisning.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du kommandot [az vm-kryptering inaktivera.](/cli/azure/vm/encryption#az-vm-encryption-disable) Inaktivera kryptering är endast tillåtet på datavolymer för virtuella Linux-datorer.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> Aktivera kryptering på en befintlig eller körande Virtuell Linux med PowerShell
Använd [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell IaaS-dator i Azure. Ta en [ögonblicksbild](snapshot-copy-managed-disk.md) eller gör en säkerhetskopia av den virtuella datorn med [Azure Backup](../../backup/backup-azure-vms-encryption.md) innan diskarna krypteras. Parametern -skipVmBackup har redan angetts i PowerShell-skript för att kryptera en linux-virtuell dator som körs.

- **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:** Följande skript initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Resursgruppen, VM, nyckelvalv, Azure AD-app och klienthemlighet borde redan ha skapats som förutsättningar. Ersätt MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID och My-AAD-client-secret med dina värden. Ändra parametern -VolumeType för att ange vilka diskar du krypterar.

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Kryptera en virtuell dator som körs med KEK för att slå in klienthemligheten:** Med Azure Disk Encryption kan du ange en befintlig nyckel i nyckelvalvet för att radbrytas diskkrypteringshemligheter som genererades när kryptering aktiverades. När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption den nyckeln för att radbrytas krypteringshemligheterna innan du skriver till nyckelvalvet. Ändra parametern -VolumeType för att ange vilka diskar du krypterar. 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI till KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatus för en virtuell IaaS-dator använder du cmdleten [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Inaktivera diskkryptering:** Om du vill inaktivera krypteringen använder du cmdleten [Disable-AzureMMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Inaktivera kryptering är endast tillåtet på datavolymer för virtuella Linux-datorer.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> Aktivera kryptering på en befintlig eller körande IaaS Linux VM med en mall

Du kan aktivera diskkryptering på en befintlig eller körande IaaS Linux VM i Azure med hjälp av [resource manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Välj **Distribuera till Azure** på snabbstartsmallen för Azure.

2. Välj prenumeration, resursgrupp, resursgruppsplats, parametrar, juridiska termer och avtal. Välj **Skapa** för att aktivera kryptering på den befintliga eller körande virtuella datorn för IaaS.

I följande tabell visas Resource Manager-mallparametrar för befintliga eller virtuella datorer som använder ett Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Azure AD-programmet som har behörighet att skriva hemligheter till nyckelvalvet. |
| AADClientSecret | Klienthemligheten för Azure AD-programmet som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| keyVaultName | Namn på nyckelvalvet som nyckeln ska överföras till. Du kan hämta den med `az keyvault show --name "MySecureVault" --query KVresourceGroup`kommandot Azure CLI . |
|  keyEncryptionKeyURL | URL för nyckelkrypteringsnyckeln som används för att kryptera den genererade nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan **AnvändExistingKek.** Om du väljer **kek** i listrutan **AnvändExistingKek** måste du ange _keyEncryptionKeyURL-värdet._ |
| volumeType (volumeType) | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden som stöds är _OS_ eller _Alla_. (Se Linuxdistributioner som stöds och deras versioner för operativsystem och datadiskar i avsnittet om förutsättningar tidigare.) |
| sequenceVersion (sequenceVersion) | Sekvensversion av BitLocker-åtgärden. Öka det här versionsnumret varje gång en diskkrypteringsåtgärd utförs på samma virtuella dator. |
| vmName | Namn på den virtuella dator som krypteringsåtgärden ska utföras på. |
| lösenfras | Skriv en stark lösenfras som datakrypteringsnyckel. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Använda funktionen EncryptFormatAll för datadiskar på virtuella Linux IaaS-datorer
Parametern EncryptFormatAll minskar tiden för Linux-datadiskar som ska krypteras. Partitioner som uppfyller vissa villkor formateras (med deras nuvarande filsystem). Sen återmonteras de tillbaka till där de var innan kommandot avrättning. Om du vill utesluta en datadisk som uppfyller villkoren kan du avmontera den innan du kör kommandot.

 När du har kört det här kommandot formateras alla enheter som tidigare monterades. Sedan börjar krypteringslagret ovanpå den nu tomma enheten. När det här alternativet är markerat krypteras även den tillfälliga resursdisken som är kopplad till den virtuella datorn. Om den tillfälliga enheten återställs formateras den om och krypteras om för den virtuella datorn av Azure Disk Encryption-lösningen vid nästa tillfälle.

>[!WARNING]
> EncryptFormatAll bör inte användas när det behövs data på en virtuell dators datavolymer. Du kan utesluta diskar från kryptering genom att avmontera dem. Prova parametern EncryptFormatAll på en test-VM först för att förstå funktionsparametern och dess implikation innan du provar den på produktionsdatorn. Alternativet EncryptFormatAll formaterar datadisken så att alla data på den går förlorade. Innan du fortsätter kontrollerar du att alla diskar som du vill utesluta har monterats av. </br></br>
 >Om du anger den här parametern medan du uppdaterar krypteringsinställningarna kan det leda till en omstart före den faktiska krypteringen. I det här fallet vill du också ta bort disken som du inte vill formatera från fstab-filen. På samma sätt bör du lägga till den partition som du vill kryptera-formaterad i fstab-filen innan du initierar krypteringsåtgärden. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> Villkor för EncryptFormatAll
Parametern går igenom alla partitioner och krypterar dem så länge de uppfyller *alla* följande kriterier: 
- Är inte en rot/OS/startpartition
- Är inte redan krypterad
- Är inte en BEK-volym
- Är inte en RAID-volym
- Är inte en LVM-volym
- Är monterad

Kryptera diskarna som utgör RAID- eller LVM-volymen i stället för RAID- eller LVM-volymen.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> Använda parametern EncryptFormatAll med en mall
Om du vill använda alternativet EncryptFormatAll använder du alla befintliga Azure Resource Manager-mallar som krypterar en Virtuell Linux-dator och ändrar fältet **EncryptionOperation** för AzureDiskEncryption-resursen.

1. Använd till exempel [resource manager-mallen för att kryptera en linux IaaS-vm som körs](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Välj **Distribuera till Azure** på snabbstartsmallen för Azure.
3. Ändra fältet **EncryptionOperation** från **EnableEncryption** till **EnableEncryptionFormatAl**.
4. Välj prenumeration, resursgrupp, resursgruppsplats, andra parametrar, juridiska termer och avtal. Välj **Skapa** för att aktivera kryptering på den befintliga eller körande virtuella datorn för IaaS.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> Använda parametern EncryptFormatAll med en PowerShell-cmdlet
Använd [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) med parametern EncryptFormatAll.

**Kryptera en virtuell dator som körs med hjälp av en klienthemlighet och EncryptFormatAll:** Som ett exempel initierar följande skript dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension med parametern EncryptFormatAll. Resursgruppen, VM, nyckelvalv, Azure AD-app och klienthemlighet borde redan ha skapats som förutsättningar. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID och My-AAD-client-secret med dina värden.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> Använda parametern EncryptFormatAll med Logisk volymhanterare (LVM) 
Vi rekommenderar en LVM-on-crypt setup. För alla följande exempel, byt ut enhetsbanan och monteringspunkterna mot vad som passar ditt användningsfall. Den här inställningen kan göras på följande sätt:

- Lägg till de datadiskar som ska skapa den virtuella datorn.
- Formatera, montera och lägg till dessa diskar i fstab-filen.

    1. Formatera den nyligen tillagda disken. Vi använder symlinks som genereras av Azure här. Om du använder symlänkar undviks problem relaterade till att enhetsnamn ändras. Mer information finns i [Felsöka problem med enhetsnamn](troubleshoot-device-names-problems.md).
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Montera diskarna.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Lägg till fstab.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Kör Cmdleten Set-AzVMDiskEncryptionExtension PowerShell med -EncryptFormatAll för att kryptera dessa diskar.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Ställ in LVM ovanpå dessa nya diskar. Observera att de krypterade enheterna låses upp när den virtuella datorn har startats klart. Så, LVM montering måste också senare försenas.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> Nya virtuella IaaS-datorer som skapats från kundkrypterade virtuella hårddiskar och krypteringsnycklar
I det här fallet kan du aktivera kryptering med hjälp av Resource Manager-mallen, PowerShell-cmdlets eller CLI-kommandon. I följande avsnitt beskrivs resurshanterarens mall och CLI-kommandon mer i detalj. 

Använd instruktionerna i bilagan för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan du använda stegen i nästa avsnitt för att skapa en krypterad Azure VM.

* [Förbereda en förkrypterad Virtuell Linux-hårddisk](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Det är obligatoriskt att ta en ögonblicksbild eller säkerhetskopiera en hanterad diskbaserad VM-instans utanför och innan Azure Disk Encryption aktiveras. Du kan ta en ögonblicksbild av den hanterade disken från portalen eller använda [Azure Backup](../../backup/backup-azure-vms-encryption.md). Säkerhetskopior säkerställer att ett återställningsalternativ är möjligt vid oväntade fel under krypteringen. När en säkerhetskopia har gjorts använder du cmdleten Set-AzVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Set-AzVMDiskEncryptionExtension-kommandot misslyckas med hanterade diskbaserade virtuella datorer tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Om du krypterar eller inaktiverar kryptering kan den virtuella datorn startas om.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Använda Azure PowerShell för att kryptera virtuella IaaS-datorer med förkrypterade virtuella hårddiskar 
Du kan aktivera diskkryptering på din krypterade virtuella hårddisk med hjälp av PowerShell cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). I följande exempel får du några vanliga parametrar. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan lägga till en ny datadisk med hjälp av [az vm-disken eller](add-disk.md) [via Azure-portalen](attach-disk-portal.md). Innan du kan kryptera måste du först montera den nyligen anslutna datadisken. Du måste begära kryptering av dataenheten eftersom enheten kommer att vara ouppnämbar medan kryptering pågår. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
 Om den virtuella datorn tidigare har krypterats med "Alla" ska parametern --volume-type förbli alla. Alla innehåller både OS och datadiskar. Om den virtuella datorn tidigare har krypterats med en volymtyp av "OS" bör parametern --volume-type ändras till Alla så att både operativsystemet och den nya datadisken inkluderas. Om den virtuella datorn krypterades med endast volymtypen "Data" kan den förbli data som visas här. Det räcker inte att lägga till och koppla en ny datadisk till en virtuell dator för kryptering. Den nyligen anslutna disken måste också formateras och monteras korrekt i den virtuella datorn innan du aktiverar kryptering. På Linux måste disken monteras i /etc/fstab med ett [beständigt blockenhetsnamn](troubleshoot-device-names-problems.md). 

I motsats till Powershell-syntaxen kräver CLI inte att du tillhandahåller en unik sekvensversion när du aktiverar kryptering. CLI genererar och använder automatiskt sitt eget unika sekvensversionsvärde.

-  **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Kryptera en virtuell dator som körs med KEK för att slå in klienthemligheten:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder Powershell för att kryptera en ny disk för Linux måste en ny sekvensversion anges. Sekvensversionen måste vara unik. Följande skript genererar ett GUID för sekvensversionen. 
 

-  **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:** Följande skript initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Resursgruppen, VM, nyckelvalv, Azure AD-app och klienthemlighet borde redan ha skapats som förutsättningar. Ersätt MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID och My-AAD-client-secret med dina värden. Parametern -VolumeType är inställd på datadiskar och inte OS-disken. Om den virtuella datorn tidigare har krypterats med en volymtyp av "OS" eller "Alla" bör parametern -VolumeType ändras till Alla så att både operativsystemet och den nya datadisken inkluderas.

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
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Kryptera en virtuell dator som körs med KEK för att slå in klienthemligheten:** Med Azure Disk Encryption kan du ange en befintlig nyckel i nyckelvalvet för att radbrytas diskkrypteringshemligheter som genererades när kryptering aktiverades. När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption den nyckeln för att radbrytas krypteringshemligheterna innan du skriver till nyckelvalvet. Parametern -VolumeType är inställd på datadiskar och inte OS-disken. Om den virtuella datorn tidigare har krypterats med en volymtyp av "OS" eller "Alla" bör parametern -VolumeType ändras till Alla så att både operativsystemet och den nya datadisken inkluderas.

     ```azurepowershell
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
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI till KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Inaktivera kryptering för virtuella Linux-datorer
Du kan inaktivera kryptering med hjälp av Azure PowerShell, Azure CLI eller en Resource Manager-mall. 

>[!IMPORTANT]
>Inaktivera kryptering med Azure Disk Encryption på virtuella Linux-datorer stöds endast för datavolymer. Det stöds inte på data- eller OS-volymer om OS-volymen har krypterats. 

- **Inaktivera diskkryptering med Azure PowerShell:** Om du vill inaktivera kryptering använder du cmdleten [Disable-AzureMMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du kommandot [az vm-kryptering inaktivera.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** Om du vill inaktivera kryptering använder du [inaktivera kryptering på en linux-vm-mall som körs.](https://aka.ms/decrypt-linuxvm)
     1. Välj **Distribuera till Azure**.
     2. Välj prenumeration, resursgrupp, plats, virtuell dator, juridiska termer och avtal.
     3. Välj **Köp** om du vill inaktivera diskkryptering på en windows-virtuell dator som körs. 


## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Disk Encryption för Linux](disk-encryption-overview-aad.md)
- [Skapa och konfigurera ett nyckelvalv för Azure Disk-kryptering med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md)
