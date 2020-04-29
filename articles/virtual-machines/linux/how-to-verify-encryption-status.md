---
title: Så här verifierar du krypterings status för Linux
description: Den här artikeln innehåller instruktioner för att verifiera krypterings statusen från Platform-och OS-nivån.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123426"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Så här verifierar du krypterings status för Linux 

**Det här scenariot gäller för ADE-tillägg med dubbla pass och ett enda pass.**  
Det här dokument omfånget är att verifiera krypterings statusen för en virtuell dator med hjälp av olika metoder.

### <a name="environment"></a>Miljö

- Linux-distributioner

### <a name="procedure"></a>Procedur

En virtuell dator har krypterats med dubbla pass eller enstaka pass.

Krypterings statusen kan verifieras under eller efter krypteringen med hjälp av olika metoder.

>[!NOTE] 
>Vi använder variabler i hela dokumentet och ersätter värdena enligt detta.

### <a name="verification"></a>Verifiering

Verifieringen kan göras från portalen, PowerShell, AZ CLI och eller från den virtuella datorns OS-sida. 

Du kan utföra den här verifieringen genom att kontrol lera diskarna som är anslutna till en viss virtuell dator. 

Eller genom att fråga krypterings inställningarna på varje enskild disk om disken är ansluten eller inte ansluten.

Under de olika validerings metoderna:

## <a name="using-the-portal"></a>Använda portalen

Verifiera krypterings statusen genom att kontrol lera avsnittet tillägg på Azure Portal.

I avsnittet **tillägg** ser du det ade-tillägg som visas. 

Klicka på den och ta en titt på **status meddelandet**, visar den aktuella krypterings statusen:

![Portal kontroll nummer 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

I listan över tillägg visas motsvarande version av ADE-tillägget. Version 0. x motsvarar ADE Dual-pass och version 1. x motsvarar ADE-ett-pass.

Du kan få mer information om hur du klickar på tillägget och sedan på *Visa detaljerad status*.

Du ser en mer detaljerad status för krypterings processen i JSON-format:

![Portal kontroll nummer 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Portal kontroll nummer 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Ett annat sätt att verifiera krypterings statusen är att ta en titt på avsnittet **diskar** .

![Portal kontroll nummer 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Denna status innebär att diskarna har krypterings inställningarna stämplade men inte att de faktiskt har krypterats på operativ system nivå. Som design kommer diskarna att stämplas först och krypteras senare. Om krypterings processen Miss lyckas kan diskarna tillföras stämplade men inte krypterade. Du kan kontrol lera om diskarna verkligen är krypterade genom att dubbelklicka på krypteringen för varje disk på operativ system nivå.

## <a name="using-powershell"></a>Använda PowerShell

Du kan verifiera den **allmänna** krypterings statusen för en krypterad virtuell dator med hjälp av följande PowerShell-kommandon:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![kontrol lera PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Du kan avbilda krypterings inställningarna från varje enskild disk med hjälp av följande PowerShell-kommandon:

### <a name="single-pass"></a>Enstaka pass
Om ett enskilt pass, krypterings inställningarna är stämplade på varje disk (OS och data), kan du avbilda disk krypterings inställningarna för operativ systemet i Single pass på följande sätt:

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Verifiera OS-ett steg 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Om det inte finns några tidsbegränsade krypterings inställningar på disken så är utdatan tomma som visas nedan:

![Inställningar för operativ system kryptering 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Avbilda data disk (s) krypterings inställningar:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Verifiera data enkel PS 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Dubbla pass
I dubbelt pass stämplas krypterings inställningarna i den virtuella dator modellen och inte på varje enskild disk.

Du kan kontrol lera att krypterings inställningarna har stämplats i dubbla pass genom att använda följande kommandon:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Verifiera dubbel pass PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Ej anslutna diskar

Kontrol lera krypterings inställningarna för diskar som inte är kopplade till en virtuell dator.

### <a name="managed-disks"></a>Hanterade diskar
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="using-az-cli"></a>Använda AZ CLI

Du kan verifiera den **allmänna** krypterings statusen för en krypterad virtuell dator med följande AZ CLI-kommandon:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Verifiera allmän användning av CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Enda steg
Du kan verifiera krypterings inställningarna från varje enskild disk med följande AZ CLI-kommandon:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Inställningar för data kryptering](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Om det inte finns några tidsbegränsade krypterings inställningar på disken visas den som "disken är inte krypterad"

Detaljerade inställningar för status och kryptering:

OS-disk:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Data diskar:

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Data-Single CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Dubbelt pass

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Verifiera allmän dubbel användning av ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) CLI du kan också kontrol lera krypterings inställningarna för VM-modellens lagrings profil på OS-disken:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Verifiera VM-profil Dual med CLI ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Ej anslutna diskar

Kontrol lera krypterings inställningarna för diskar som inte är kopplade till en virtuell dator.

### <a name="managed-disks"></a>Hanterade diskar

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Ohanterade diskar

Ohanterade diskar är VHD-filer som lagras som Page blobbar i Azure Storage-konton.

För att få information om en speciell disk måste du ange:

ID för det lagrings konto som innehåller disken.
En anslutnings sträng för just det lagrings kontot.
Namnet på behållaren som lagrar disken.
Disk namnet.

Det här kommandot visar alla ID: n för alla dina lagrings konton:

```bash
az storage account list --query [].[id] -o tsv
```
Lagrings kontots ID: n visas i följande format:

/Subscriptions/\<prenumerations-ID\<>/resourcegroups/resurs grupp namn\<>/providers/Microsoft.Storage/storageaccounts/lagrings konto namn>

Välj lämpligt ID och lagra det på en variabel:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
Anslutnings strängen.

Det här kommandot hämtar anslutnings strängen för ett visst lagrings konto och lagrar det på en variabel:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Behållarens namn.

Följande kommando visar alla behållare under ett lagrings konto:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Den behållare som används för diskar kallas vanligt vis "VHD"

Lagra behållar namnet på en variabel 
```bash
ContainerName="name of the container"
```

Disk namnet.

Använd det här kommandot för att visa en lista över alla blobbar i en viss behållare
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Välj den disk som du vill fråga efter och lagra namnet på en variabel.
```bash
DiskName="diskname.vhd"
```
Fråga om disk krypterings inställningarna
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Från operativ systemet
Verifiera om datadiskpartitionerna är krypterade (och OS-disken inte)

När en partition/disk krypteras visas den som **krypterings** typ, om den inte är krypterad visas den som **en del/disk** -typ

``` bash
lsblk
```

![Operativ system krypterings skikt ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Du kan få mer information med hjälp av följande "lsblk"-variant. 

Du ser ett **krypterings** typ lager som är monterat av tillägget.

I följande exempel visas logiska volymer och normala diskar med "**\_krypto LUKS FSTYPE**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![OS-kryptografi skikt 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Som ett extra steg kan du också kontrol lera om data disken har lästa nycklar

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

Och vilka DM-enheter som listas som kryptering

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Nästa steg

- [Felsökning för Azure Disk Encryption](disk-encryption-troubleshooting.md)
