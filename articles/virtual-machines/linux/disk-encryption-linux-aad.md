---
title: Azure Disk Encryption med Azure AD App Linux IaaS VM (tidigare version)
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure disk kryptering för virtuella Linux IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 35d14599ca9a7abdad88603deeb8431f77e92a92
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748934"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Aktivera Azure Disk Encryption med Azure AD på virtuella Linux-datorer (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure AD-programparameter för att aktivera disk kryptering för virtuella datorer. Med den nya versionen behöver du inte längre ange autentiseringsuppgifter för Azure AD under steget aktivera kryptering. Alla nya virtuella datorer måste krypteras utan Azure AD-programmets parametrar med den nya versionen. Om du vill visa instruktioner för att aktivera disk kryptering för virtuella datorer med den nya versionen, se [Azure Disk Encryption för virtuella Linux-datorer](disk-encryption-linux.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att behållas med AAD-syntaxen.**

Du kan aktivera många disk krypterings scenarier och stegen kan variera beroende på scenariot. I följande avsnitt beskrivs scenarierna i större detalj för virtuella Linux IaaS-datorer. Du kan bara använda disk kryptering för virtuella datorer med [stödda VM-storlekar och operativ system](disk-encryption-overview.md#supported-vms-and-operating-systems). Du måste också uppfylla följande krav:

- [Ytterligare krav för virtuella datorer](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Nätverks-och grupprincip](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Lagrings krav för krypterings nyckel](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Ta en [ögonblicks bild](snapshot-copy-managed-disk.md) och/eller säkerhetskopiera innan diskarna krypteras. Säkerhets kopieringar säkerställer att ett återställnings alternativ är möjligt om ett oväntat fel uppstår under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhets kopia innan krypteringen utförs. När du har gjort en säkerhets kopia kan du använda cmdleten Set-AzVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern-skipVmBackup. Mer information om hur du säkerhetskopierar och återställer krypterade virtuella datorer finns i [Azure Backup](../../backup/backup-azure-vms-encryption.md) artikeln. 

>[!WARNING]
 > - Om du tidigare har använt [Azure Disk Encryption med Azure AD-appen](disk-encryption-overview-aad.md) för att kryptera den här virtuella datorn måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Du kan inte använda [Azure Disk Encryption](disk-encryption-overview.md) på den här krypterade virtuella datorn eftersom detta inte stöds, vilket innebär att det inte finns stöd för att växla bort från AAD-program för den här krypterade virtuella datorn än.
 > - För att se till att krypterings hemligheterna inte korsar regionala gränser måste Azure Disk Encryption Key Vault och de virtuella datorerna ska samplaceras i samma region. Skapa och Använd en Key Vault som finns i samma region som den virtuella dator som ska krypteras.
 > - Processen kan ta några timmar när du krypterar Linux OS-volymer. Det är normalt att Linux OS-volymer tar längre tid än data volymer att kryptera.
> - När du krypterar Linux OS-volymer bör den virtuella datorn anses vara otillgänglig. Vi rekommenderar starkt att du undviker SSH-inloggningar medan krypteringen pågår för att undvika problem som blockerar eventuella öppna filer som behöver nås under krypterings processen. För att kontrol lera förloppet kan kommandot [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) eller [VM-kryptering Visa](/cli/azure/vm/encryption#az-vm-encryption-show) användas. Den här processen kan förväntas ta några timmar för en 30 GB OS-volym, plus ytterligare tid för kryptering av data volymer. Krypterings tiden för data volymer är proportionell mot storlek och kvantitet för data volymerna om inte alternativet Kryptera format alla används. 
 > - Det går bara att inaktivera kryptering på virtuella Linux-datorer för data volymer. Det stöds inte på data-eller OS-volymer om operativ system volymen har krypterats.  

 

## <a name="bkmk_RunningLinux"></a> Aktivera kryptering på en befintlig eller virtuell IaaS Linux-dator

I det här scenariot kan du aktivera kryptering med hjälp av Resource Manager-mallen, PowerShell-cmdletar eller CLI-kommandon. 

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicks bilder och/eller säkerhetskopiera en hanterad disk baserad virtuell dator instans utanför, och innan Azure Disk Encryption aktive ras. En ögonblicks bild av den hanterade disken kan hämtas från portalen, eller [Azure Backup](../../backup/backup-azure-vms-encryption.md) kan användas. Säkerhets kopieringar säkerställer att ett återställnings alternativ är möjligt om det uppstår oväntade fel under krypteringen. När en säkerhets kopia har gjorts kan cmdleten Set-AzVMDiskEncryptionExtension användas för att kryptera Managed disks genom att ange parametern-skipVmBackup. Kommandot Set-AzVMDiskEncryptionExtension fungerar inte mot hanterade diskbaserade virtuella datorer förrän en säkerhets kopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering kan leda till att den virtuella datorn startas om. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Aktivera kryptering på en befintlig eller virtuell Linux-dator med Azure CLI 
Du kan aktivera disk kryptering på din krypterade virtuella hård disk genom att installera och använda kommando rads verktyget [Azure CLI 2,0](/cli/azure) . Du kan använda det i webbläsaren med [Azure Cloud Shell](../../cloud-shell/overview.md) eller installera det på din lokala dator och använda det i PowerShell-sessioner. Använd följande CLI-kommandon om du vill aktivera kryptering på befintliga eller aktiva virtuella IaaS Linux-datorer i Azure:

Använd kommandot [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) för att aktivera kryptering på en virtuell IaaS-dator som körs i Azure.

-  **Kryptera en virtuell dator som körs med en klient hemlighet:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Kryptera en virtuell dator som körs med KEK för att omsluta klient hemligheten:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resurs grupp-namn]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn]</br> </br>
Syntaxen för värdet för nyckeln Key-Encryption-Key är den fullständiga URI: n till KEK som i: https://[Key Vault-Name]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID] 

- **Kontrol lera att diskarna är krypterade:** Om du vill kontrol lera krypterings statusen för en virtuell IaaS-dator använder du kommandot [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du kommandot [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Det går bara att inaktivera kryptering på data volymer för virtuella Linux-datorer.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"></a> Aktivera kryptering på en befintlig eller virtuell Linux-dator med PowerShell
Använd cmdleten [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell IaaS-dator som körs i Azure. Ta en [ögonblicks bild](snapshot-copy-managed-disk.md) och/eller säkerhetskopiera den virtuella datorn med [Azure Backup](../../backup/backup-azure-vms-encryption.md) innan diskarna krypteras. Parametern-skipVmBackup har redan angetts i PowerShell-skripten för att kryptera en virtuell Linux-dator som körs.

- **Kryptera en virtuell dator som körs med en klient hemlighet:** Skriptet nedan initierar variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension. Resurs gruppen, den virtuella datorn, nyckel valvet, AAD-appen och klient hemligheten bör redan ha skapats som krav. Ersätt MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-Client-ID och My-AAD-client-Secret med dina värden. Ändra parametern-VolumeType för att ange vilka diskar som du ska kryptera.

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
- **Kryptera en virtuell dator som körs med KEK för att omsluta klient hemligheten:** Med Azure Disk Encryption kan du ange en befintlig nyckel i ditt nyckel valv för att figursätta disk krypterings hemligheter som genererades när kryptering aktiverades. När en nyckel krypterings nyckel anges använder Azure Disk Encryption den nyckeln för att omsluta krypterings hemligheter innan du skriver till Key Vault. Ändra parametern-VolumeType för att ange vilka diskar som du ska kryptera. 

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
  > Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[KVresource-Group-name]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn]</br> </br>
  Syntaxen för värdet för nyckeln Key-Encryption-Key är den fullständiga URI: n till KEK som i: https://[Key Vault-Name]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID] 
    
- **Kontrol lera att diskarna är krypterade:** Använd cmdleten [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) för att kontrol lera krypterings statusen för en virtuell IaaS-dator. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Inaktivera disk kryptering:** Om du vill inaktivera krypteringen använder du cmdleten [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Det går bara att inaktivera kryptering på data volymer för virtuella Linux-datorer.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"></a> Aktivera kryptering på en befintlig eller virtuell IaaS Linux-dator med en mall

Du kan aktivera disk kryptering på en befintlig eller köra en virtuell IaaS Linux-dator i Azure med hjälp av [Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Klicka på **distribuera till Azure** i Azure snabb starts mal len.

2. Välj prenumeration, resurs grupp, plats för resurs grupp, parametrar, juridiska villkor och avtal. Klicka på **skapa** för att aktivera kryptering på den befintliga eller aktiva virtuella IaaS-datorn.

I följande tabell visas parametrar för Resource Manager-mallar för befintliga eller aktiva virtuella datorer som använder ett Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Azure AD-programmet som har behörighet att skriva hemligheter till nyckel valvet. |
| AADClientSecret | Klient hemlighet för Azure AD-programmet som har behörighet att skriva hemligheter till ditt nyckel valv. |
| keyVaultName | Namnet på nyckel valvet som nyckeln ska överföras till. Du kan hämta den med hjälp av Azure CLI-kommandot `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  keyEncryptionKeyURL | URL till den nyckel krypterings nyckel som används för att kryptera den genererade nyckeln. Den här parametern är valfri om du väljer **nokek** i list rutan UseExistingKek. Om du väljer **KEK** i list rutan UseExistingKek måste du ange värdet _keyEncryptionKeyURL_ . |
| volumeType | Typ av volym som krypterings åtgärden utförs på. Giltiga värden som stöds är _OS_ eller _alla_ (se Linux-distributioner som stöds och deras versioner för operativ system och data diskar i förutsättnings avsnitt tidigare). |
| sequenceVersion | Sekvens-version av BitLocker-åtgärden. Öka det här versions numret varje gång en disk krypterings åtgärd utförs på samma virtuella dator. |
| vmName | Namnet på den virtuella dator som krypterings åtgärden ska utföras på. |
| lösenfras | Skriv en stark lösen fras som data krypterings nyckel. |



## <a name="bkmk_EFA"> </a>Använda funktionen EncryptFormatAll för data diskar på virtuella Linux-IaaS-datorer
Parametern **EncryptFormatAll** minskar tiden för att Linux-datadiskarna ska krypteras. Partitioner som uppfyller vissa kriterier kommer att formateras (med det aktuella fil systemet). Sedan kommer de att monteras tillbaka till den plats där det var innan kommando körningen. Om du vill undanta en datadisk som uppfyller villkoren kan du Demontera den innan du kör kommandot.

 När du har kört det här kommandot kommer alla enheter som monterats tidigare att formateras. Sedan startas krypterings skiktet ovanpå enheten som nu är tom. När det här alternativet är markerat krypteras även den tillfälliga resurs disk som är ansluten till den virtuella datorn. Om den tillfälliga enheten återställs, formateras den om och krypteras om för den virtuella datorn med Azure Disk Encryption lösning vid nästa tillfälle.

>[!WARNING]
> EncryptFormatAll bör inte användas när det behövs data på en virtuell dators data volymer. Du kan utesluta diskar från kryptering genom att demontera dem. Du bör först testa EncryptFormatAll först på en virtuell test dator, förstå funktions parametern och dess indirekt innan du testar den på den virtuella produktions datorn. Alternativet EncryptFormatAll formaterar data disken och alla data på den kommer att gå förlorade. Innan du fortsätter bör du kontrol lera att diskarna som du vill undanta är korrekt demonterade. </br></br>
 >Om du anger den här parametern när du uppdaterar krypterings inställningarna kan det leda till en omstart före den faktiska krypteringen. I det här fallet ska du också ta bort disken som du inte vill formatera från fstab-filen. På samma sätt bör du lägga till partitionen som du vill kryptera-formaterad till fstab-filen innan du initierar krypterings åtgärden. 

### <a name="bkmk_EFACriteria"></a> EncryptFormatAll kriterier
Parametern går ut om alla partitioner och krypterar dem så länge de uppfyller **alla** kriterier nedan: 
- Är inte en rot-/OS/startpartition
- Är inte redan krypterad
- Är inte en BEK-volym
- Är inte en RAID-volym
- Är inte en LVM-volym
- Är monterad

Kryptera diskarna som skapar RAID-eller LVM-volymen i stället för RAID-eller LVM-volymen.

### <a name="bkmk_EFATemplate"></a> Använda parametern EncryptFormatAll med en mall
Om du vill använda alternativet EncryptFormatAll använder du en befintlig Azure Resource Manager-mall som krypterar en virtuell Linux-dator och ändrar fältet **EncryptionOperation** för AzureDiskEncryption-resursen.

1. Du kan till exempel använda [Resource Manager-mallen för att kryptera en virtuell Linux IaaS-dator som körs](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Klicka på **distribuera till Azure** i Azure snabb starts mal len.
3. Ändra **EncryptionOperation** från **EnableEncryption** till **EnableEncryptionFormatAl**
4. Välj prenumeration, resurs grupp, plats för resurs grupp, andra parametrar, juridiska villkor och avtal. Klicka på **skapa** för att aktivera kryptering på den befintliga eller aktiva virtuella IaaS-datorn.


### <a name="bkmk_EFAPSH"></a> Använda parametern EncryptFormatAll med en PowerShell-cmdlet
Använd cmdleten [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) med parametern `EncryptFormatAll`.

**Kryptera en virtuell dator som körs med en klient hemlighet och EncryptFormatAll:** Skriptet nedan initierar till exempel variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension med parametern EncryptFormatAll. Resurs gruppen, den virtuella datorn, nyckel valvet, AAD-appen och klient hemligheten bör redan ha skapats som krav. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-Client-ID och My-AAD-client-Secret med dina värden.
  
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


### <a name="bkmk_EFALVM"></a> Använd parametern EncryptFormatAll med Logical Volume Manager (LVM) 
Vi rekommenderar en LVM-in-Encrypt-installation. I följande exempel ersätter du enhets Sök vägen och mountpoints med det som passar dina användnings fall. Du kan göra följande inställningar:

- Lägg till de data diskar som ska skapa den virtuella datorn.
- Formatera, montera och Lägg till de här diskarna i fstab-filen.

    1. Formatera den nyligen tillagda disken. Vi använder symlinks som genererats av Azure här. Om du använder symlinks undviker du problem som rör ändring av enhets namn. Mer information finns i artikeln [Felsök problem med enhets namn](troubleshoot-device-names-problems.md) .
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Montera diskarna.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Lägg till i fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Kör PowerShell-cmdleten Set-AzVMDiskEncryptionExtension med-EncryptFormatAll för att kryptera diskarna.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Konfigurera LVM ovanpå de här nya diskarna. Observera att de krypterade enheterna låses upp när den virtuella datorn har startats. Det innebär att LVM-monteringen också måste förskjutas senare.




## <a name="bkmk_VHDpre"></a> Nya virtuella IaaS-datorer som skapats från kund-krypterade VHD-och krypterings nycklar
I det här scenariot kan du aktivera kryptering med hjälp av Resource Manager-mallen, PowerShell-cmdletar eller CLI-kommandon. I följande avsnitt beskrivs mer information i Resource Manager-mallen och CLI-kommandona. 

Följ anvisningarna i bilagan för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan du använda stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbereda en förkrypterad virtuell Linux-hårddisk](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicks bilder och/eller säkerhetskopiera en hanterad disk baserad virtuell dator instans utanför, och innan Azure Disk Encryption aktive ras. En ögonblicks bild av den hanterade disken kan hämtas från portalen, eller [Azure Backup](../../backup/backup-azure-vms-encryption.md) kan användas. Säkerhets kopieringar säkerställer att ett återställnings alternativ är möjligt om det uppstår oväntade fel under krypteringen. När en säkerhets kopia har gjorts kan cmdleten Set-AzVMDiskEncryptionExtension användas för att kryptera Managed disks genom att ange parametern-skipVmBackup. Kommandot Set-AzVMDiskEncryptionExtension fungerar inte mot hanterade diskbaserade virtuella datorer förrän en säkerhets kopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering kan leda till att den virtuella datorn startas om. 



### <a name="bkmk_VHDprePSH"></a> Använd Azure PowerShell för att kryptera virtuella IaaS-datorer med förkrypterade virtuella hård diskar 
Du kan aktivera disk kryptering på din krypterade virtuella hård disk med PowerShell-cmdleten [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Exemplet nedan visar några vanliga parametrar. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan lägga till en ny datadisk med [AZ VM disk Attach](add-disk.md)eller [via Azure Portal](attach-disk-portal.md). Innan du kan kryptera måste du montera den nyligen anslutna data disken först. Du måste begära kryptering av data enheten eftersom enheten inte kan användas medan kryptering pågår. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
 Om den virtuella datorn tidigare har krypterats med "alla", ska parametern--volym typ vara kvar. Alla omfattar både OS-och data diskar. Om den virtuella datorn tidigare har krypterats med en volymtyp av typen "OS", ska parametern--type ändras till alla så att både operativ systemet och den nya datadisken tas med. Om den virtuella datorn har krypterats med endast volym typen "data" kan den vara kvar "data" som visas nedan. Det räcker inte att lägga till och ansluta en ny datadisk till en virtuell dator för kryptering. Den nyligen anslutna disken måste också formateras och monteras korrekt i den virtuella datorn innan krypteringen aktive ras. På Linux måste disken monteras i/etc/fstab med ett [beständigt block enhets namn](troubleshoot-device-names-problems.md).  

Till skillnad från PowerShell-syntaxen kräver CLI inte att användaren anger en unik sekvens-version när krypteringen aktive ras. CLI genererar automatiskt och använder sitt eget unika sekvens versions värde.

-  **Kryptera en virtuell dator som körs med en klient hemlighet:** 

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Kryptera en virtuell dator som körs med KEK för att omsluta klient hemligheten:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder PowerShell för att kryptera en ny disk för Linux måste en ny sekvens-version anges. Sekvens-versionen måste vara unik. Skriptet nedan genererar en GUID för sekvens-versionen. 
 

-  **Kryptera en virtuell dator som körs med en klient hemlighet:** Skriptet nedan initierar variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension. Resurs gruppen, den virtuella datorn, nyckel valvet, AAD-appen och klient hemligheten bör redan ha skapats som krav. Ersätt MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-Client-ID och My-AAD-client-Secret med dina värden. Parametern-VolumeType har angetts till data diskar och inte operativ system disken. Om den virtuella datorn tidigare har krypterats med volym typen "OS" eller "alla", ska parametern-VolumeType ändras till alla så att både operativ systemet och den nya datadisken tas med.

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
- **Kryptera en virtuell dator som körs med KEK för att omsluta klient hemligheten:** Med Azure Disk Encryption kan du ange en befintlig nyckel i ditt nyckel valv för att figursätta disk krypterings hemligheter som genererades när kryptering aktiverades. När en nyckel krypterings nyckel anges använder Azure Disk Encryption den nyckeln för att omsluta krypterings hemligheter innan du skriver till Key Vault. Parametern-VolumeType har angetts till data diskar och inte operativ system disken. Om den virtuella datorn tidigare har krypterats med volym typen "OS" eller "alla", ska parametern-VolumeType ändras till alla så att både operativ systemet och den nya datadisken tas med.

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
> Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resurs grupp-namn]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn] </br> </br>
Syntaxen för värdet för nyckeln Key-Encryption-Key är den fullständiga URI: n till KEK som i: https://[Key Vault-Name]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID] 

## <a name="disable-encryption-for-linux-vms"></a>Inaktivera kryptering för virtuella Linux-datorer
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI eller med en Resource Manager-mall. 

>[!IMPORTANT]
>Det går bara att inaktivera kryptering med Azure Disk Encryption på virtuella Linux-datorer för data volymer. Det stöds inte på data-eller OS-volymer om operativ system volymen har krypterats.  

- **Inaktivera disk kryptering med Azure PowerShell:** Om du vill inaktivera krypteringen använder du cmdleten [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du kommandot [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** Använd [inaktivera kryptering på en virtuell Linux](https://aka.ms/decrypt-linuxvm) -mall för virtuella datorer för att inaktivera kryptering.
     1. Klicka på **Distribuera till Azure**.
     2. Välj prenumeration, resurs grupp, plats, virtuell dator, juridiska villkor och avtal.
     3.  Klicka på **köp** för att inaktivera disk kryptering på en Windows-VM som körs. 


## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Disk Encryption för Linux](disk-encryption-overview-aad.md)
- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md)
