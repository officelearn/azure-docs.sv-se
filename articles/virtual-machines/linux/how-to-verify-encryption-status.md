---
title: Så här verifierar du krypteringsstatus för Linux
description: Den här artikeln innehåller instruktioner om hur du verifierar krypteringsstatusen från plattforms- och OS-nivå.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123426"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Så här verifierar du krypteringsstatus för Linux 

**Det här scenariot gäller för ADE dual-pass och single-pass-tillägg.**  
Det här dokumentomfånget är att validera krypteringsstatus för en virtuell dator med hjälp av olika metoder.

### <a name="environment"></a>Miljö

- Linux-distributioner

### <a name="procedure"></a>Procedur

En virtuell dator har krypterats med dubbla pass eller single-pass.

Krypteringsstatusen kan valideras under eller efter krypteringen med hjälp av olika metoder.

>[!NOTE] 
>Vi använder variabler i hela dokumentet, ersätter värdena därefter.

### <a name="verification"></a>Verifiering

Verifieringen kan göras från portalen, PowerShell, AZ CLI och, eller från VM OS-sidan. 

Den här verifieringen kan göras genom att kontrollera diskarna som är anslutna till en viss virtuell dator. 

Eller genom att fråga krypteringsinställningarna på varje enskild disk om disken är ansluten eller obunden.

Nedan nedan för de olika valideringsmetoderna:

## <a name="using-the-portal"></a>Använda portalen

Verifiera krypteringsstatusen genom att kontrollera tilläggsavsnittet på Azure-portalen.

I avsnittet **Tillägg** visas ADE-tillägget i listan. 

Klicka på den och ta en titt på **statusmeddelandet**, det kommer att ange den aktuella krypteringsstatus:

![Portalkontroll nummer 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

I listan över tillägg visas motsvarande ADE-tilläggsversion. Version 0.x motsvarar ADE Dual-Pass och version 1.x motsvarar ADE Single-pass.

Du kan få mer information klicka på tillägget och sedan på *Visa detaljerad status*.

Du ser en mer detaljerad status för krypteringsprocessen i json-format:

![Portalkontroll nummer 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Portalkontroll nummer 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Ett annat sätt att validera krypteringsstatus är att ta en titt på **disksektionen.**

![Portalkontroll nummer 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Den här statusen innebär att diskarna har krypteringsinställningar stämplade men inte att de faktiskt krypterades på OS-nivå. Avsiktligt stämplas diskarna först och krypteras senare. Om krypteringsprocessen misslyckas kan diskarna bli stämplade men inte krypterade. För att bekräfta om diskarna verkligen är krypterade kan du dubbelkolla krypteringen av varje disk på OS-nivå.

## <a name="using-powershell"></a>Använda PowerShell

Du kan validera den **allmänna** krypteringsstatusen för en krypterad virtuell dator med följande PowerShell-kommandon:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![kontrollera PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Du kan samla in krypteringsinställningarna från varje enskild disk med följande PowerShell-kommandon:

### <a name="single-pass"></a>Enkelpass
Om enkelpass är krypteringsinställningarna stämpel på var och en av diskarna (OS och data) kan du fånga in os-diskkrypteringsinställningarna i ett enda pass enligt följande:

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
![Verifiera OS Single pass 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Om disken inte har stämplade krypteringsinställningarna är utdata tom enligt nedan:

![Inställningar för OS-kryptering 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Krypteringsinställningar för insamling av datadiskar:

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
![Verifiera data enkel ps 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Dubbla pass
I Dual Pass stämplas krypteringsinställningarna i vm-modellen och inte på varje enskild disk.

Om du vill kontrollera att krypteringsinställningarna har stämplats med dubbla pass kan du använda följande kommandon:

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
![Verifiera PowerShell 1 med dubbla pass](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Obundna diskar

Kontrollera krypteringsinställningarna för diskar som inte är anslutna till en virtuell dator.

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

Du kan validera den **allmänna** krypteringsstatusen för en krypterad virtuell dator med hjälp av följande AZ CLI-kommandon:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Verifiera allmänt med CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Enkelt pass
Du kan validera krypteringsinställningarna från varje enskild disk med hjälp av följande AZ CLI-kommandon:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Inställningar för datakryptering](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Om disken inte har krypteringsinställningar stämplade visas den som "Disken är inte krypterad"

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

Datadiskar:

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

![Data enda CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Dubbla pass

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Verifiera allmän dubbel ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) med CLI Du kan också kontrollera krypteringsinställningarna på VM-modelllagringsprofilen för OS-disken:

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

![Verifiera vm-profil dubbel med CLI ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Obundna diskar

Kontrollera krypteringsinställningarna för diskar som inte är anslutna till en virtuell dator.

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

Ohanterat diskar är VHD-filer som lagras som sidblobar i Azure-lagringskonton.

Om du vill få information om en viss disk måste du ange:

ID:et för lagringskontot som innehåller disken.
En anslutningssträng för just det lagringskontot.
Namnet på behållaren som lagrar disken.
Disknamnet.

Det här kommandot visar alla ID:er för alla dina lagringskonton:

```bash
az storage account list --query [].[id] -o tsv
```
Lagringskonto-ID:erna visas i följande formulär:

/subscriptions/subscription\<id>/resourceGroups/resource\<group name>/providers/Microsoft.Storage/storageAccounts/storage\<account name>

Välj lämpligt ID och lagra det på en variabel:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
Anslutningssträngen.

Det här kommandot hämtar anslutningssträngen för ett visst lagringskonto och lagrar det på en variabel:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Behållarnamnet.

Följande kommando visar alla behållare under ett lagringskonto:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Behållaren som används för diskar kallas normalt "vhds"

Lagra behållarnamnet på en variabel 
```bash
ContainerName="name of the container"
```

Disknamnet.

Använd det här kommandot om du vill visa alla blobbar på en viss behållare
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Välj den disk som du vill fråga och lagra dess namn på en variabel.
```bash
DiskName="diskname.vhd"
```
Fråga diskkrypteringsinställningarna
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Från operativsystemet
Verifiera om datadiskpartitionerna är krypterade (och os-disken inte är det)

När en partition/disk krypteras visas den som **krypttyp,** när den inte är krypterad visas den som **del-/disktyp**

``` bash
lsblk
```

![Os Crypt lager ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Du kan få ytterligare information med hjälp av följande "lsblk" variant. 

Du ser ett **krypttypslag** som är monterat av tillägget.

I följande exempel visas logiska volymer och normala diskar med en "**krypto-LUKS\_FSTYPE**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Os Crypt lager 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Som ett extra steg kan du också validera om datadisken har några nycklar inlästa

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

Och vilka dm-enheter som anges som kryptan

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Efterföljande moment

- [Felsökning för Azure Disk Encryption](disk-encryption-troubleshooting.md)
