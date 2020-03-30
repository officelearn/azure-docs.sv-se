---
title: Konfigurera LVM och RAID på kryptan på en virtuell Linux-dator
description: Den här artikeln innehåller instruktioner om hur du konfigurerar LVM och RAID på kryptan på virtuella Linux-datorer.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 78ba47ba887cf7c90adf70d9d444fbd8a3c721cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284916"
---
# <a name="how-to-configure-lvm-and-raid-on-crypt"></a>Konfigurera LVM och RAID på kryptan

Det här dokumentet är en steg-för-steg-process om hur du utför LVM på kryptan och Raid på kryptankonfigurationer.

### <a name="environment"></a>Miljö

- Linux-distributioner
    - RHEL 7,6+
    - Ubuntu 18.04+
    - SUSE 12+
- ADE Enkelpass
- ADE Dubbla pass


## <a name="scenarios"></a>Scenarier

**Det här scenariot gäller för ADE dual-pass och single-pass-tillägg.**  

- Konfigurera LVM ovanpå krypterade enheter (LVM-on-Crypt)
- Konfigurera RAID ovanpå krypterade enheter (RAID-on-Crypt)

När de underliggande enheterna har krypterats kan du skapa LVM- eller RAID-strukturerna ovanpå det krypterade lagret. De fysiska volymerna (PV) skapas ovanpå det krypterade lagret.
De fysiska volymerna används för att skapa volymgruppen.
Du skapar volymerna och lägger till de nödvändiga posterna på /etc/fstab. 

![Kontrollera diskar som är anslutna med PowerShell](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

På liknande sätt skapas RAID-enheten ovanpå det krypterade lagret på diskarna. Ett filsystem skapas ovanpå RAID-enheten och läggs till /etc/fstab som en vanlig enhet.

### <a name="considerations"></a>Överväganden

Den rekommenderade metoden att använda är LVM-on-Crypt.

RAID beaktas när LVM inte kan användas på grund av specifika begränsningar för program/miljö.

Du ska använda alternativet EncryptFormatAll, information om den https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vmshär funktionen finns här: .

Även om den här metoden kan göras när du även krypterar operativsystemet krypterar vi bara dataenheter.

Denna procedur förutsätter att du redan granskat de https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux förutsättningar https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstartsom nämns här: och här .

ADE dual pass-versionen är på utfasningssökvägen och bör inte längre användas på nya ADE-krypteringar.

### <a name="procedure"></a>Procedur

När du använder konfigurationerna "på kryptan" följer du processen som beskrivs nedan:

>[!NOTE] 
>Vi använder variabler i hela dokumentet, ersätter värdena därefter.
## <a name="general-steps"></a>Allmänna steg
### <a name="deploy-a-vm"></a>Distribuera en virtuell dator 
>[!NOTE] 
>Även om detta är valfritt rekommenderar vi att du använder detta på en nyligen distribuerad virtuell dator.

PowerShell
```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Cli:
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
### <a name="attach-disks-to-the-vm"></a>Koppla diskar till den virtuella datorn:
Upprepa för $N antal nya diskar som du vill koppla till VM PowerShell
```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```
Cli:
```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```
### <a name="verify-the-disks-are-attached-to-the-vm"></a>Kontrollera att diskarna är anslutna till den virtuella datorn:
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Kontrollera diskar anslutna](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png) PowerShell CLI:
```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Kontrollera diskar bifogade](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png) ![CLI Portal: Kontrollera](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png) diskar bifogade CLI OS:
```bash
lsblk 
```
![Kontrollera diskar som är bifogade](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)
### <a name="configure-the-disks-to-be-encrypted"></a>Konfigurera diskarna som ska krypteras
Den här konfigurationen görs att operativsystemet nivå, motsvarande diskar är konfigurerade för en traditionell ADE-kryptering:

Filsystem skapas ovanpå diskarna.

Tillfälliga monteringspunkter skapas för att montera filsystemen.

Filsystemen är konfigurerade på /etc/fstab som ska monteras vid uppstart.

Kontrollera enhetsbeteckningen som tilldelats de nya diskarna, i det här exemplet använder vi fyra datadiskar

```bash
lsblk 
```
![Kontrollera diskar bifogade os](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-filesystem-on-top-of-each-disk"></a>Skapa ett filsystem ovanpå varje disk.
Detta kommando itererar en ext4 filsystem skapas på varje disk som definieras på "i" del av "för" cykel.
```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Kontrollera diskar bifogade os](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png) Hitta UUID av filsystem som nyligen skapats, skapa en tillfällig mapp för att montera den, lägga till motsvarande poster på / etc / fstab och montera alla filsystem.

Detta befaller också itererar på varje disk som definieras på "i" delen av "för" cyklar:
```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 
### <a name="verify-the-disks-are-mounted-properly"></a>Kontrollera att diskarna är korrekt monterade:
```bash
lsblk
```
![Kontrollera tempfilsystem monterade](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png) och konfigurerade:
```bash
cat /etc/fstab
```
![Kontrollera fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)
### <a name="encrypt-the-data-disks"></a>Kryptera datadiskarna:
PowerShell med KEK:
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
CLI med KEK:
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
![Kontrollera kryptering](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png) ps CLI:
```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Kontrollera kryptering](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png) CLI ![Portal:](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png) Kontrollera kryptering OS OS-nivå:
```bash
lsblk
```
![Kontrollera kryptering CLI](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Tillägget kommer att lägga till filsystemen i "/var/lib/azure_disk_encryption_config/azure_crypt_mount" (en gammal kryptering) eller läggas till i "/etc/crypttab" (nya krypteringar).

Ändra inte någon av dessa filer.

Den här filen kommer att ta hand om att aktivera dessa diskar under uppstartsprocessen så att de senare kan användas av LVM eller RAID. 

Oroa dig inte om monteringspunkterna på den här filen, eftersom ADE kommer att förlora möjligheten att få diskarna monterade som ett normalt filsystem efter att vi skapar en fysisk volym eller en raid-enhet ovanpå de krypterade enheterna (som kommer att bli av med filsystemformatet som vi använde under förberedelseprocessen).
### <a name="remove-the-temp-folders-and-temp-fstab-entries"></a>Ta bort temp mappar och temp fstab poster
Du avmonterar filsystemen på diskarna som ska användas som en del av LVM
```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
Och ta bort / etc / fstab poster:
```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Kontrollera att diskarna inte är monterade och att posterna på /etc/fstab har tagits bort
```bash
lsblk
```
![Kontrollera temp-filsystem avmonterade](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png) och konfigurerade:
```bash
cat /etc/fstab
```
![Kontrollera temp fstab poster tas bort](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)
## <a name="for-lvm-on-crypt"></a>För LVM-on-crypt
Nu när de underliggande diskarna är krypterade kan du fortsätta att skapa LVM-strukturerna.

I stället för att använda enhetsnamnet använder du sökvägarna /dev/mapper för var och en av diskarna för att skapa en fysisk volym (på kryptans lager ovanpå disken inte på själva disken).
### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Konfigurera LVM ovanpå de krypterade lagren
#### <a name="create-the-physical-volumes"></a>Skapa de fysiska volymerna
Du får en varning som frågar om det är OK att utplåna filsystemets signatur. 

Du kan fortsätta genom att ange "y" eller använda ekot "y" som visas:
```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![pvcreate (pvcreate)](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)
>[!NOTE] 
>Namnen /dev/mapper/device här måste ersättas för dina faktiska värden baserat på utdata från lsblk.
#### <a name="verify-the-physical-volumes-information"></a>Verifiera information om fysiska volymer
```bash
pvs
```
![kontrollera fysiska volymer 1](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)
#### <a name="create-the-volume-group"></a>Skapa volymgruppen
Skapa VG med samma enheter som redan initierats
```bash
vgcreate vgdata /dev/mapper/
```
### <a name="check-the-volume-group-information"></a>Kontrollera information om volymgruppen
```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![kontrollera fysiska volymer 2](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)
#### <a name="create-logical-volumes"></a>Skapa logiska volymer
```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 
#### <a name="check-the-logical-volumes-created"></a>Kontrollera de logiska volymer som skapats
```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![kontrollera lvs](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)
#### <a name="create-filesystems-on-top-of-the-logical-volumes-structures"></a>Skapa filsystem ovanpå den eller de logiska volymstrukturerna
```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```
#### <a name="create-the-mount-points-for-the-new-filesystems"></a>Skapa monteringspunkter för de nya filsystemen
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
#### <a name="verify-that-the-new-filesystems-are-mounted"></a>Kontrollera att de nya filsystemen är monterade
```bash
lsblk -fs
df -h
```
![kontrollera logiska volymer](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png) På den här varianten av lsblk listar vi de enheter som visar beroenden på omvänd ordning, det här alternativet hjälper till att identifiera de enheter som grupperas efter den logiska volymen i stället för de ursprungliga /dev/sd[disk]-enhetsnamnen.

Viktigt: Se till att alternativet "nofail" läggs till i monteringspunktsalternativen för LVM-volymer som skapats ovanpå en ADE-krypterad enhet. Är viktigt att undvika os från att fastna under startprocessen (eller i underhållsläge). 

Den krypterade disken låses upp i slutet av startprocessen, LVM-volymerna och filsystemen monteras automatiskt.

Om alternativet inget svik inte används kommer operativsystemet aldrig in i det stadium där ADE startas och datadiskarna låses upp och monteras.

Du kan testa att starta om den virtuella datorn och validera filsystemen också automatiskt få monteras efter starttid. 

Ta hänsyn till att denna process kan ta flera minuter beroende på antalet filsystem och storlekarna
#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Starta om den virtuella datorn och verifiera efter omstart
```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="for-raid-on-crypt"></a>För RAID-on-Crypt
Nu krypteras de underliggande diskarna och du kan fortsätta att skapa RAID-strukturerna, samma som LVM, i stället för att använda enhetsnamnet, med hjälp av sökvägarna /dev/mapper för var och en av diskarna.

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
![mdadm skapa](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)
>[!NOTE] 
>Namnen /dev/mapper/device här måste ersättas för dina faktiska värden baserat på utdata från lsblk.
#### <a name="checkmonitor-the-raid-creation"></a>Kontrollera/övervaka skapandet av RAID:
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![kontrollera mdadm](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)
#### <a name="create-a-filesystem-on-top-of-the-new-raid-device"></a>Skapa ett filsystem ovanpå den nya Raid-enheten:
```bash
mkfs.ext4 /dev/md10
```
Skapa en ny monteringspunkt för filsystemet, lägg till det nya filsystemet i /etc/fstab och montera den
```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```
Kontrollera att de nya filsystemen är monterade
```bash
lsblk -fs
df -h
```
![kontrollera mdadm](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Viktigt: Se till att alternativet "nofail" läggs till i monteringspunktsalternativen för RAID-volymer som skapats ovanpå en ADE-krypterad enhet. 

Är mycket viktigt att undvika os från att fastna under uppstartsprocessen (eller i underhållsläge). 

Den krypterade disken kommer att låsas upp i slutet av startprocessen och RAID-volymer och filsystem kommer automatiskt att monteras tills de är upplåst av ADE, om alternativet nofail inte används.

Operativsystemet kommer aldrig in i det skede där ADE startas, och datadiskarna låses upp och monteras.

Du kan testa att starta om den virtuella datorn och validera filsystemen också automatiskt få monteras efter starttid. Ta hänsyn till att denna process kan ta flera minuter beroende på antalet filsystem och storlekarna
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

