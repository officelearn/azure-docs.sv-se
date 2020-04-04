---
title: Konfigurera LVM och RAID på krypterade enheter – Azure Disk Encryption
description: Den här artikeln innehåller instruktioner för hur du konfigurerar LVM och RAID på krypterade enheter för virtuella Linux-datorer.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 4e342ff44af38b8e79dc8695c1270b1f5c68e0a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657432"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Konfigurera LVM och RAID på krypterade enheter

Den här artikeln är en steg-för-steg-process för hur du utför LVM (Logical Volume Management) och RAID på krypterade enheter. Processen gäller följande miljöer:

- Linux-distributioner
    - RHEL 7,6+
    - Ubuntu 18.04+
    - SUSE 12+
- Azure Disk Encryption enkelpasstillägg
- Tillägg för dubbla pass för Azure Disk Encryption


## <a name="scenarios"></a>Scenarier

Procedurerna i den här artikeln stöder följande scenarier:  

- Konfigurera LVM ovanpå krypterade enheter (LVM-on-crypt)
- Konfigurera RAID ovanpå krypterade enheter (RAID-on-crypt)

När den underliggande enheten eller enheterna har krypterats kan du skapa LVM- eller RAID-strukturerna ovanpå det krypterade lagret. 

De fysiska volymerna (PVs) skapas ovanpå det krypterade lagret. De fysiska volymerna används för att skapa volymgruppen. Du skapar volymerna och lägger till de nödvändiga posterna på /etc/fstab. 

![Diagram över skikten av LVM strukturer](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

På liknande sätt skapas RAID-enheten ovanpå det krypterade lagret på diskarna. Ett filsystem skapas ovanpå RAID-enheten och läggs till /etc/fstab som en vanlig enhet.

## <a name="considerations"></a>Överväganden

Vi rekommenderar att du använder LVM-on-crypt. RAID är ett alternativ när LVM inte kan användas på grund av specifika program- eller miljöbegränsningar.

Du ska använda alternativet **EncryptFormatAll.** Mer information om det här alternativet finns i [Använda funktionen EncryptFormatAll för datadiskar på virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms).

Även om du kan använda den här metoden när du också krypterar operativsystemet krypterar vi bara dataenheter här.

Procedurerna förutsätter att du redan har granskat förutsättningarna i [Azure Disk Encryption-scenarier på virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux) och i [Snabbstart: Skapa och kryptera en Virtuell Linux-dator med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart).

Azure Disk Encryption dual-pass-versionen finns på en infasningssökväg och bör inte längre användas på nya krypteringar.

## <a name="general-steps"></a>Allmänna steg

När du använder konfigurationerna "on-crypt" använder du processen som beskrivs i följande procedurer.

>[!NOTE] 
>Vi använder variabler i hela artikeln. Ersätt värdena därefter.

### <a name="deploy-a-vm"></a>Distribuera en virtuell dator 
Följande kommandon är valfria, men vi rekommenderar att du använder dem på en nyutvecklad virtuell dator (VM).

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI:

```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Koppla diskar till den virtuella datorn
Upprepa följande kommandon `$N` för antalet nya diskar som du vill koppla till den virtuella datorn.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI:

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Kontrollera att diskarna är anslutna till den virtuella datorn
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Lista över anslutna diskar i PowerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Lista över anslutna diskar i Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portal:

![Lista över anslutna diskar i portalen](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

Operativsystem:

```bash
lsblk 
```
![Lista över anslutna diskar i operativsystemet](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Konfigurera diskarna som ska krypteras
Den här konfigurationen görs på operativsystemsnivå. Motsvarande diskar är konfigurerade för en traditionell kryptering via Azure Disk Encryption:

- Filsystem skapas ovanpå diskarna.
- Tillfälliga monteringspunkter skapas för att montera filsystemen.
- Filsystem är konfigurerade på /etc/fstab som ska monteras vid uppstart.

Kontrollera enhetsbeteckningen som tilldelats de nya diskarna. I det här exemplet använder vi fyra datadiskar.

```bash
lsblk 
```
![Datadiskar som är kopplade till operativsystemet](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Skapa ett filsystem ovanpå varje disk
Detta kommando itererar skapandet av ett ext4 filsystem på varje disk som definieras på "i" del av "för" cykel.

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Skapa ett ext4-filsystem](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Hitta den universellt unika identifieraren (UUID) för de filsystem som du nyligen skapade, skapa en tillfällig mapp, lägg till motsvarande poster på /etc/fstab och montera alla filsystem.

Detta befaller också itererar på varje disk som definieras på "i" delen av "för" cyklar:

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Kontrollera att diskarna är korrekt monterade
```bash
lsblk
```
![Lista över monterade temporära filsystem](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Kontrollera också att diskarna är konfigurerade:

```bash
cat /etc/fstab
```
![Konfigurationsinformation via fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Kryptera datadiskarna
PowerShell med hjälp av en nyckelkrypteringsnyckel (KEK):

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

Azure CLI med hjälp av en KEK:

```bash
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>Verifiera krypteringsstatus

Fortsätt till nästa steg endast när alla diskar är krypterade.

PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Krypteringsstatus i PowerShell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Krypteringsstatus i Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portal:

![Krypteringsstatus i portalen](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

OS-nivå:

```bash
lsblk
```
![Krypteringsstatus i operativsystemet](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Tillägget kommer att lägga till filsystemen i /var/lib/azure_disk_encryption_config/azure_crypt_mount (en gammal kryptering) eller till /etc/crypttab (nya krypteringar).

>[!NOTE] 
>Ändra inte någon av dessa filer.

Den här filen tar hand om att aktivera dessa diskar under startprocessen så att LVM eller RAID kan använda dem senare. 

Oroa dig inte för monteringspunkterna på den här filen. Azure Disk Encryption förlorar möjligheten att få diskarna monterade som ett vanligt filsystem när vi har skapat en fysisk volym eller en RAID-enhet ovanpå dessa krypterade enheter. (Detta tar bort filsystemformatet som vi använde under förberedelseprocessen.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Ta bort de temporära mapparna och de tillfälliga fstabposterna
Du avmonterar filsystemen på diskarna som ska användas som en del av LVM.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
Och ta bort / etc / fstab poster:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Kontrollera att diskarna inte är monterade och att posterna på /etc/fstab har tagits bort

```bash
lsblk
```
![Verifiering av att temporära filsystem är avmonterade](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

Och kontrollera att diskarna är konfigurerade:
```bash
cat /etc/fstab
```
![Verifiering av att tillfälliga fstab-poster tas bort](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Steg för LVM-on-crypt
Nu när de underliggande diskarna är krypterade kan du skapa LVM-strukturerna.

I stället för att använda enhetsnamnet använder du sökvägarna /dev/mapper för var och en av diskarna för att skapa en fysisk volym (på kryptans lager ovanpå disken, inte på själva disken).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Konfigurera LVM ovanpå de krypterade lagren
#### <a name="create-the-physical-volumes"></a>Skapa de fysiska volymerna
Du får en varning som frågar om det är OK att utplåna filsystemets signatur. Fortsätt genom att ange **y**eller använd **echo "y"** som visas:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Verifiering av att en fysisk volym har skapats](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>Namnen /dev/mapper/device här måste ersättas för dina faktiska värden baserat på utdata från **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Verifiera informationen för fysiska volymer
```bash
pvs
```

![Information för fysiska volymer](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Skapa volymgruppen
Skapa volymgruppen med samma enheter som redan initierats:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Kontrollera informationen för volymgruppen

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Information för volymgruppen](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Skapa logiska volymer

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Kontrollera de skapade logiska volymerna

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Information för logiska volymer](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Skapa filsystem ovanpå strukturerna för logiska volymer

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Skapa monteringspunkter för de nya filsystemen

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Lägg till de nya filsystemen i /etc/fstab och montera dem

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Kontrollera att de nya filsystemen är monterade

```bash
lsblk -fs
df -h
```
![Information för monterade filsystem](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

På den här varianten av **lsblk listar**vi de enheter som visar beroenden i omvänd ordning. Det här alternativet hjälper till att identifiera de enheter som grupperas efter den logiska volymen i stället för de ursprungliga /dev/sd[disk]-enhetsnamnen.

Det är viktigt att se till att alternativet **inget svikt** läggs till i monteringspunktsalternativen för LVM-volymer som skapats ovanpå en enhet som krypteras via Azure Disk Encryption. Det förhindrar att operativsystemet fastnar under startprocessen (eller i underhållsläge).

Om du inte använder alternativet **inget svikare:**

- Operativsystemet kommer aldrig in i det stadium där Azure Disk Encryption startas och datadiskarna låses upp och monteras. 
- De krypterade diskarna kommer att låsas upp i slutet av startprocessen. LVM-volymerna och filsystemen monteras automatiskt tills Azure Disk Encryption låser upp dem. 

Du kan testa att starta om den virtuella datorn och verifiera att filsystemen också automatiskt monteras efter starttid. Den här processen kan ta flera minuter, beroende på antalet filsystem och storlekar.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Starta om den virtuella datorn och verifiera efter omstart

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Steg för RAID-on-crypt
Nu när de underliggande diskarna är krypterade kan du fortsätta att skapa RAID-strukturerna. Processen är densamma som den för LVM, men i stället för att använda enhetsnamnet använder du sökvägarna /dev/mapper för varje disk.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Konfigurera RAID ovanpå det krypterade lagret på diskarna
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Information för konfigurerad RAID via mdadm-kommandot](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>Namnen /dev/mapper/device här måste ersättas med dina faktiska värden, baserat på utdata från **lsblk**.

### <a name="checkmonitor-raid-creation"></a>Kontrollera/övervaka RAID-skapande
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Status för RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Skapa ett filsystem ovanpå den nya RAID-enheten
```bash
mkfs.ext4 /dev/md10
```

Skapa en ny monteringspunkt för filsystemet, lägg till det nya filsystemet till /etc/fstab och montera den:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Kontrollera att det nya filsystemet är monterat:

```bash
lsblk -fs
df -h
```
![Information för monterade filsystem](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Det är viktigt att se till att alternativet **inget svikt** läggs till i monteringspunktsalternativen för RAID-volymer som skapats ovanpå en enhet som krypteras via Azure Disk Encryption. Det förhindrar att operativsystemet fastnar under startprocessen (eller i underhållsläge).

Om du inte använder alternativet **inget svikare:**

- Operativsystemet kommer aldrig in i det stadium där Azure Disk Encryption startas och datadiskarna låses upp och monteras.
- De krypterade diskarna kommer att låsas upp i slutet av startprocessen. RAID-volymerna och filsystemen monteras automatiskt tills Azure Disk Encryption låser upp dem.

Du kan testa att starta om den virtuella datorn och verifiera att filsystemen också automatiskt monteras efter starttid. Den här processen kan ta flera minuter, beroende på antalet filsystem och storlekar.

```bash
shutdown -r now
```

Och när du kan logga in:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Nästa steg

- [Felsökning för Azure Disk Encryption](disk-encryption-troubleshooting.md)

