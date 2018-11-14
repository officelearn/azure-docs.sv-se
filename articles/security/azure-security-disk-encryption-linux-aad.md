---
title: Azure Disk Encryption med Azure AD App Linux IaaS-datorer (tidigare version) | Microsoft Docs
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure Disk Encryption för virtuella Linux IaaS-datorer.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 09/19/2018
ms.openlocfilehash: d544d131aed8dc6560f09b7a293e950c38380fb8
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623426"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms-previous-release"></a>Aktivera Azure Disk Encryption för virtuella Linux IaaS-datorer (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar behovet av att tillhandahålla en parameter för Azure AD-program att aktivera VM-diskkryptering. Med den nya versionen kan krävs du inte längre för att ange autentiseringsuppgifter för Azure AD under steget Aktivera kryptering. Alla nya virtuella datorer måste vara krypterat utan parametrarna Azure AD-program med hjälp av den nya versionen. Instruktioner för att aktivera diskkryptering för virtuell dator med hjälp av den nya versionen finns [Azure Disk Encryption för Linux VMS](azure-security-disk-encryption-linux.md). Virtuella datorer som redan har krypterats med Azure AD-program parametrar stöds fortfarande och bör fortsätta att underhållas med AAD-syntax.**

Du kan aktivera många diskkryptering scenarier och stegen kan variera beroende på scenario. Följande avsnitt beskriver scenarier i större detalj för virtuella Linux IaaS-datorer. Innan du kan använda diskkryptering, den [som krävs för Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) måste slutföras och [ytterligare krav för Linux IaaS-datorer](azure-security-disk-encryption-prerequisites-aad.md#bkmk_LinuxPrereq) avsnittet bör granskas.

Ta en [ögonblicksbild](../virtual-machines/windows/snapshot-copy-managed-disk.md) och/eller säkerhetskopierar innan diskar krypteras. Säkerhetskopior Se till att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. Du kan använda cmdleten Set-AzureRmVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Mer information om hur du säkerhetskopiera och återställa krypterade virtuella datorer finns i den [Azure Backup](../backup/backup-azure-vms-encryption.md) artikeln. 

>[!WARNING]
 >För att se till att kryptering hemligheterna inte går över regionala gränser, måste Azure Disk Encryption Key Vault och de virtuella datorerna för att finnas i samma region. Skapa och använda ett Nyckelvalv som är i samma region som den virtuella datorn måste vara krypterade.</br></br>

> När du krypterar Linux OS-volymer, kan ta några timmar. Det är normalt för Linux-operativsystem volymer tar längre tid än datavolymer för att kryptera. 

>Inaktivera kryptering på den virtuella Linux-datorer stöds bara för datavolymer. Det stöds inte på data eller operativsystemvolymer om operativsystemvolymen har krypterats.  


## <a name="bkmk_NewLinux"></a> Distribuera nya virtuella Linux IaaS-datorer med diskkryptering aktiverat 

1. Använd den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel) att skapa en ny krypterade virtuella Linux IaaS-datorer. Mallen skapar en ny Red Hat Linux 7.2 VM med en 200 GB RAID-0-matris och fullständig diskkryptering som använder hanterade diskar. På den [vanliga frågor och svar](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) artikeln ser du att vissa Linux-distributioner bara stöder kryptering för datadiskar. Den här mallen innehåller dock en möjlighet att bekanta dig med att distribuera mallar och verifierar krypteringsstatus med flera metoder. 
 
1. Klicka på **distribuera till Azure** på Azure Resource Manager-mall.

2. Välj prenumeration, resursgrupp, resursgruppens plats, parametrar, juridiska villkor och avtal. Klicka på **skapa** att aktivera kryptering på den befintliga eller körs IaaS-VM.

3. När du har distribuerat mallen Kontrollera krypteringsstatus VM med önskad metod:
     - Kontrollera med Azure CLI med hjälp av den [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) kommando. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Kontrollera med Azure PowerShell med hjälp av den [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) cmdlet. 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     - Välj den virtuella datorn, och klicka sedan på **diskar** under den **inställningar** rubrik för att kontrollera status för datakryptering i portalen. I diagrammet under **kryptering**, ser du om den är aktiverad. 

| Parameter | Beskrivning |
| --- | --- |
| Klient-ID för AAD | Klient-ID för Azure AD-program som har behörighet att skriva hemligheter i key vault. |
| Klienthemlighet för AAD | Klienthemlighet för Azure AD-program som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| Namn på Key Vault | Namnet på det nyckelvalv som nyckeln ska placeras. |
| Key Vault-resursgrupp | Resursgrupp för nyckelvalvet. |


## <a name="bkmk_RunningLinux"> </a> Aktivera kryptering på en befintlig eller körs IaaS Linux virtuell dator
I det här scenariot kan du aktivera kryptering med hjälp av Resource Manager-mall, PowerShell-cmdletar eller CLI-kommandon. 

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzureRmVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Kommandot Set-AzureRmVMDiskEncryptionExtension misslyckas mot virtuella datorer med hanterade diskar baserade tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Aktivera kryptering på en befintlig eller som kör Linux-VM med Azure CLI 
Du kan aktivera diskkryptering på en krypterad virtuell Hårddisk genom att installera och använda den [Azure CLI 2.0](/cli/azure) kommandoradsverktyget. Du kan använda det i webbläsaren med [Azure Cloud Shell](../cloud-shell/overview.md) eller installera det på din lokala dator och använda det i PowerShell-sessioner. Om du vill möjliggöra kryptering på befintliga eller kör IaaS virtuella Linux-datorer i Azure måste du använda följande CLI-kommandon:

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
    > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** för att kontrollera statusen för kryptering av en IaaS-VM, använda den [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) kommando. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Inaktivera kryptering:** inaktivera kryptering med den [az vm encryption inaktivera](/cli/azure/vm/encryption#az-vm-encryption-disable) kommando. Inaktivera kryptering tillåts endast på datavolymer för virtuella Linux-datorer.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Aktivera kryptering på en befintlig eller som kör Linux-VM med hjälp av PowerShell
Använd den [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) cmdlet för att aktivera kryptering på en aktiv IaaS virtuell dator i Azure. 

-  **Kryptera en aktiv virtuell dator med hjälp av en klienthemlighet:** skriptet nedan initierar dina variabler och kör cmdleten Set-AzureRmVMDiskEncryptionExtension. Resursgrupp, virtuell dator, nyckelvalvet, AAD-appar och klienthemlighet bör redan har skapats som krav. Ersätt MySecureRg, MySecureVM, MySecureVault, min-AAD-klient-ID och min-AAD-client-secret med dina värden. Du kan behöva lägga till parametern - VolumeType om du krypterar datadiskar och inte OS-disken. 

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
- **Kryptera en aktiv virtuell dator med hjälp av KEK du omsluter klienthemlighet:** Azure Disk Encryption kan du ange en befintlig nyckel i ditt nyckelvalv du omsluter disk encryption hemligheter som genererades vid aktivering av kryptering. När du anger en nyckelkrypteringsnyckel använder Azure Disk Encryption nyckeln för att omsluta kryptering hemligheter innan du skriver till Key Vault. Du kan behöva lägga till parametern - VolumeType om du krypterar datadiskar och inte OS-disken. 

     ```azurepowershell-interactive
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

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

 >[!NOTE]
 > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Kontrollera att diskarna är krypterade:** för att kontrollera statusen för kryptering av en IaaS-VM, använda den [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) cmdlet. 
    
     ```azurepowershell-interactive 
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **Inaktivera diskkryptering:** inaktivera kryptering med den [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet. Inaktivera kryptering tillåts endast på datavolymer för virtuella Linux-datorer.
     
     ```azurepowershell-interactive 
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Aktivera kryptering på en befintlig eller körs IaaS Linux-dator med en mall

Du kan aktivera diskkryptering på en befintlig eller körs IaaS Linux-dator i Azure med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Klicka på **distribuera till Azure** på Azure-snabbstartsmall.

2. Välj prenumeration, resursgrupp, resursgruppens plats, parametrar, juridiska villkor och avtal. Klicka på **skapa** att aktivera kryptering på den befintliga eller körs IaaS-VM.

I följande tabell visas Resource Manager-mallens parametrar för befintliga eller som kör virtuella datorer som använder en Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Azure AD-program som har behörighet att skriva hemligheter i key vault. |
| AADClientSecret | Klienthemlighet för Azure AD-program som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| keyVaultName | Namnet på det nyckelvalv som nyckeln ska överföras till. Du kan hämta den med hjälp av Azure CLI-kommando `az keyvault show --name "MySecureVault" --query resourceGroup`. |
|  KeyEncryptionKeyURL | URL för nyckelkrypteringsnyckel som används för att kryptera den genererade nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan UseExistingKek. Om du väljer **kek** i listrutan UseExistingKek måste du ange den _keyEncryptionKeyURL_ värde. |
| VolumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden som stöds är _OS_ eller _alla_ (Se stöd för Linux-distributioner och deras versioner för Operativsystemet och datadiskarna i avsnittet förutsättningar ovan). |
| SequenceVersion | Sekvens-versionen av BitLocker-åtgärden. Öka det här versionsnumret varje gång en diskkryptering åtgärd utförs på samma virtuella dator. |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på. |
| lösenfras | Ange ett starkt lösenord som krypteringsnyckeln för tjänstdata. |



## <a name="bkmk_EFA"> </a>Använd EncryptFormatAll funktion för datadiskar på Linux IaaS-datorer
Den **EncryptFormatAll** parametern minskar tid för Linux datadiskar måste vara krypterade. Partitioner som uppfyller vissa kriterier formateras (med dess aktuella filsystem). De kommer att återmonteras tillbaka till föregående Kommandokörning. Om du vill exkludera en datadisk som uppfyller villkoren kan demontera du den innan du kör kommandot.

 När du har kört det här kommandot kommer eventuella enheter som har monterats tidigare att formateras. Krypteringslagret kan sedan startas ovanpå nu tom enheten. När det här alternativet är markerat krypteras också tillfälliga resursdisk som är ansluten till den virtuella datorn. Om den tillfälliga enheten återställs den formateras om och omkrypteras med hjälp av Azure Disk Encryption-lösningen vid nästa tillfälle för den virtuella datorn.

>[!WARNING]
> EncryptFormatAll bör inte användas när det nödvändiga data på en virtuell dators datavolymer. Du kan undanta diskar från kryptering av demontera dem. Du bör först prova EncryptFormatAll först på en virtuell testdator, Förstå funktionsparametern och dess de innan du försöker på den Virtuella produktionsdatorn. Alternativet EncryptFormatAll formaterar datadisken och alla data på den kommer att gå förlorade. Kontrollera att diskarna som du vill utesluta korrekt är unmouted innan du fortsätter. </br></br>
 >Om du konfigurerar den här parametern vid uppdatering av krypteringsinställningar, kan det leda till en omstart innan du själva krypteringen. I det här fallet vill du också ta bort disken som du inte vill formaterade från fstab-filen. På samma sätt bör du lägga till den partition som du vill kryptera-formaterad till fstab-filen innan du påbörjar krypteringsåtgärden. 

### <a name="bkmk_EFACriteria"> </a> EncryptFormatAll kriterier
Parametern går dock alla partitioner och krypterar dem så länge de uppfyller **alla** av följande villkor: 
- Är inte en rot-OS-Start-partition
- Krypteras inte redan
- Är inte en BEK volym
- Är inte en RAID-volym
- Är inte en LVM-volym
- Är monterad

Kryptera diskar som utgör RAID- eller LVM volymen i stället för RAID- eller LVM volymen.

### <a name="bkmk_EFATemplate"> </a> Använd parametern EncryptFormatAll med en mall
Använd alternativet EncryptFormatAll, använda alla befintliga Azure Resource Manager-mallar som krypterar en Linux-VM och ändra den **EncryptionOperation** för AzureDiskEncryption resursen.

1. Till exempel använda den [Resource Manager-mall för att kryptera en aktiv virtuell dator IaaS Linux](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Klicka på **distribuera till Azure** på Azure-snabbstartsmall.
3. Ändra den **EncryptionOperation** från **EnableEncryption** till **EnableEncryptionFormatAl**
4. Välj prenumerationen, resursgrupp, resursgruppens plats, andra parametrar, juridiska villkor och avtal. Klicka på **skapa** att aktivera kryptering på den befintliga eller körs IaaS-VM.


### <a name="bkmk_EFAPSH"> </a> Använd parametern EncryptFormatAll med en PowerShell-cmdlet
Använd den [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) cmdlet med den [EncryptFormatAll parametern](https://www.powershellgallery.com/packages/AzureRM/5.0.0). 

**Kryptera en aktiv virtuell dator med hjälp av en klienthemlighet och EncryptFormatAll:** exempelvis skriptet nedan initierar dina variabler och kör cmdleten Set-AzureRmVMDiskEncryptionExtension med parametern EncryptFormatAll. Resursgrupp, virtuell dator, nyckelvalvet, AAD-appar och klienthemlighet bör redan har skapats som krav. Ersätt MySecureRg, MySecureVM, MySecureVault, min-AAD-klient-ID och min-AAD-client-secret med dina värden.
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Använd parametern EncryptFormatAll med logiska Volume Manager (LVM) 
Vi rekommenderar en LVM-på-crypt-installation. Ersätt enhetens sökväg och monteringspunkter med det passar ditt användningsområde för alla i följande exempel. Den här konfigurationen kan du göra på följande sätt:

- Lägga till datadiskar som kommer utgör den virtuella datorn.
- Formatera, montera och lägga till diskarna i fstab-filen.

    1. Formatera den nytillagda disken. Vi använder symlinks som genereras av Azure här. Med hjälp av symlinks undviker problem som rör enhetsnamn ändras. Mer information finns i den [felsöka enhetsnamn problem](../virtual-machines/linux/troubleshoot-device-names-problems.md) artikeln.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Montera diskarna.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Lägg till i fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Kör Set-AzureRmVMDiskEncryptionExtension PowerShell-cmdlet med - EncryptFormatAll att kryptera dessa diskar.
         ```azurepowershell-interactive
         Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Konfigurera LVM ovanpå dessa nya diskar. Observera enheterna som är krypterade är upplåst när den virtuella datorn har slutförts startar. LVM-montering måste därför också fördröjas senare.




## <a name="bkmk_VHDpre"> </a> Nya virtuella IaaS-datorer skapas från kund-krypterad VHD och kryptering nycklar
Du kan aktivera kryptering med hjälp av Resource Manager-mall, PowerShell-cmdletar eller CLI-kommandona i det här scenariot. I följande avsnitt beskrivs mer detaljerat Resource Manager-mall och CLI-kommandon. 

Följ instruktionerna i tillägget för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan använda du stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbered en förkrypterade Windows virtuell Hårddisk](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Förbereda en förkrypterade Linux-VHD](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzureRmVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Kommandot Set-AzureRmVMDiskEncryptionExtension misslyckas mot virtuella datorer med hanterade diskar baserade tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 



### <a name="bkmk_VHDprePSH"> </a> Använda Azure PowerShell för att kryptera virtuella IaaS-datorer med förkrypterade virtuella hårddiskar 
Du kan aktivera diskkryptering på en krypterad virtuell Hårddisk med hjälp av PowerShell-cmdleten [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). I exemplet nedan visas några vanliga parametrar. 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName "MySecureRG"
```

### <a name="bkmk_VHDpreRM"> </a> Använda Resource Manager-mallen för att kryptera virtuella IaaS-datorer med förkrypterade virtuella hårddiskar 
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
Du kan lägga till en ny disk med [az vm disk attach](../virtual-machines/linux/add-disk.md), eller [via Azure portal](../virtual-machines/linux/attach-disk-portal.md). Innan du kan kryptera måste du först montera den nyligen anslutna disken. Du måste begära kryptering av data-enheten eftersom enheten inte att använda medan kryptering pågår. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
 Om den virtuella datorn som har krypterats med ”alla” sedan--volymtyp parametern bör vara alla. Alla innehåller både operativsystem och datadiskar. Om den virtuella datorn tidigare har krypterats med en volymtyp av ”OS” och sedan--volymtyp parametern bör ändras till alla så att både Operativsystemet och den nya datadisken inkluderas. Om den virtuella datorn har krypterats med endast volymtyp ”data”, kan det finnas ”Data” som visas nedan. Lägga till och koppla en ny datadisk till en virtuell dator är inte tillräckligt med förberedelse för kryptering. Den nyligen anslutna disken måste också vara formaterad och korrekt monterade i den virtuella datorn innan du aktiverar krypteringen. I Linux måste disken monteras i/etc/fstab med en [beständiga block enhetsnamn](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

Till skillnad från Powershell-syntax kräver CLI inte att användaren anger en unik teckensekvens version när du aktiverar kryptering. CLI genererar automatiskt och använder en egen unik teckensekvens värde.

-  **Kryptera en aktiv virtuell dator med hjälp av en klienthemlighet:** 

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Kryptera en aktiv virtuell dator med hjälp av KEK du omsluter klienthemlighet:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder Powershell för att kryptera en ny disk för Linux, måste en ny sekvens-version anges. Sekvens-versionen måste vara unikt. Skriptet nedan genererar ett GUID för sekvens-versionen. 
 

-  **Kryptera en aktiv virtuell dator med hjälp av en klienthemlighet:** skriptet nedan initierar dina variabler och kör cmdleten Set-AzureRmVMDiskEncryptionExtension. Resursgrupp, virtuell dator, nyckelvalvet, AAD-appar och klienthemlighet bör redan har skapats som krav. Ersätt MySecureRg, MySecureVM, MySecureVault, min-AAD-klient-ID och min-AAD-client-secret med dina värden. Parametern - VolumeType anges till datadiskar och inte OS-disken. Om den virtuella datorn har tidigare har krypterats med en volymtyp av ”OS” eller ”alla”, ska sedan parametern - VolumeType ändras till alla så att både Operativsystemet och den nya datadisken inkluderas.

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

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **Kryptera en aktiv virtuell dator med hjälp av KEK du omsluter klienthemlighet:** Azure Disk Encryption kan du ange en befintlig nyckel i ditt nyckelvalv du omsluter disk encryption hemligheter som genererades vid aktivering av kryptering. När du anger en nyckelkrypteringsnyckel använder Azure Disk Encryption nyckeln för att omsluta kryptering hemligheter innan du skriver till Key Vault. Parametern - VolumeType anges till datadiskar och inte OS-disken. Om den virtuella datorn har tidigare har krypterats med en volymtyp av ”OS” eller ”alla”, ska sedan parametern - VolumeType ändras till alla så att både Operativsystemet och den nya datadisken inkluderas.

     ```azurepowershell-interactive
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

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data';
     ```


>[!NOTE]
> Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> </br>
Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

## <a name="disable-encryption-for-linux-vms"></a>Inaktivera kryptering för virtuella Linux-datorer
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI, eller med en Resource Manager-mall. 

>[!IMPORTANT]
>Inaktivera kryptering med Azure Disk Encryption på virtuella Linux-datorer stöds bara för datavolymer. Det stöds inte på data eller operativsystemvolymer om operativsystemvolymen har krypterats.  

- **Inaktivera disk encryption med Azure PowerShell:** inaktivera kryptering med den [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Inaktivera kryptering med Azure CLI:** inaktivera kryptering med den [az vm encryption inaktivera](/cli/azure/vm/encryption#az-vm-encryption-disable) kommando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** används den [inaktivera kryptering på en som kör Linux VM](https://aka.ms/decrypt-linuxvm) mall för att inaktivera kryptering.
     1. Klicka på **Distribuera till Azure**.
     2. Välj prenumeration, resursgrupp, plats, VM, juridiska villkor och avtal.
     3.  Klicka på **köp** att inaktivera diskkryptering på en aktiv virtuell Windows-dator. 


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Aktivera Azure Disk Encryption för Windows](azure-security-disk-encryption-windows-aad.md)
