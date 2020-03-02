---
title: Fel sökning – Azure Disk Encryption för virtuella Linux-datorer
description: Den här artikeln innehåller fel söknings tips för Microsoft Azure disk kryptering för virtuella Linux-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: dd21b6520dc68a6f7faa5500054b2865556e3dfb
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205916"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Felsökningsguide för Azure Disk Encryption

Den här guiden är för IT-proffs, informationssäkerhetsanalytiker och molnadministratörer i organisationer som använder Azure Disk Encryption. Den här artikeln är att hjälpa till med felsökning av disk-kryptering-relaterade problem.

Innan du vidtar stegen nedan måste du först se till att de virtuella datorer som du försöker kryptera finns bland de [VM-storlekar och operativ system som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems)och att du uppfyller alla krav:

- [Ytterligare krav för virtuella datorer](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Nätverks krav](disk-encryption-overview.md#networking-requirements)
- [Lagrings krav för krypterings nyckel](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Felsökning av Linux OS-diskkryptering

Diskkryptering för Linux-operativsystem (OS) måste demontera operativsystemenheten innan du kör via krypteringsprocessen fullständig disk. Om det går inte att koppla bort enheten, ett felmeddelande för ”det gick inte att demontera efter...” är sannolikt kan förekomma.

Det här felet kan inträffa när disk kryptering för operativ systemet görs på en virtuell dator med en miljö som har ändrats från den bild som stöds av lager galleriet. Avvikelser från avbildningen som stöds kan störa tilläggets möjligheten att demontera operativsystemenheten. Exempel på avvikelser kan innehålla följande objekt:
- Anpassade avbildningar matchar inte längre ett filsystem som stöds eller partitioneringsschema.
- Stora program som SAP, MongoDB, Apache Cassandra och Docker stöds inte när de är installerade och körs i Operativsystemet innan du kryptering. Azure Disk Encryption kan inte stänga av de här processerna som krävs som förberedelse för operativsystemenheten för diskkryptering på ett säkert sätt. Om det finns fortfarande aktiva processer med öppna filhandtag till operativsystemenheten, får inte operativsystemenheten vara demonterats, vilket resulterar i ett fel att kryptera operativsystemenheten. 
- Anpassade skript som körs i Stäng tid närhet till kryptering som är aktiverade, eller om någon annan ändringar görs på den virtuella datorn under krypteringsprocessen. Den här konflikten kan inträffa när en Azure Resource Manager-mall definierar flera tillägg för att köra samtidigt, eller när ett anpassat skripttillägg eller annan åtgärd körs samtidigt till diskkryptering. Serialisering och isolera sådana åtgärder kan lösa problemet.
- Security förbättrad Linux (SELinux) har inte inaktiverats innan du aktiverar kryptering, så att demontera steg misslyckas. Vara kan reenabled SELinux när kryptering är klar.
- OS-disken använder ett schema för logiska Volume Manager (LVM). Även om det finns begränsat LVM data disk-stöd är inte en LVM OS-disk.
- Minimikraven på minne inte uppfylls (7 GB rekommenderas för OS-diskkryptering).
- Dataenheter är rekursivt monterad under katalogen /mnt/ eller varandra (till exempel /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Uppdatera standard kärnan för Ubuntu 14,04-LTS

Ubuntu 14,04 LTS-avbildningen levereras med en standard-kernel-version på 4,4. Den här kernel-versionen har ett känt problem där slut på minne-Killer felaktigt avslutar kommandot DD under processen för operativ system kryptering. Den här buggen har åtgärd ATS i den senaste Azure-justerade Linux-kärnan. För att undvika det här felet, innan du aktiverar kryptering på avbildningen, uppdaterar du till [Azure-justerade kernel 4,15](https://packages.ubuntu.com/trusty/linux-azure) eller senare med hjälp av följande kommandon:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

När den virtuella datorn har startats om i den nya kerneln kan den nya kernel-versionen bekräftas med:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Uppdatera Azure Virtual Machine agent och tilläggs versioner

Azure Disk Encryption åtgärder kan Miss lyckas på avbildningar av virtuella datorer med hjälp av versioner av Azure Virtual Machine agent som inte stöds. Linux-avbildningar med agent versioner tidigare än 2.2.38 bör uppdateras innan kryptering aktive ras. Mer information finns i [så här uppdaterar du Azure Linux-agenten på en VM](../extensions/update-linux-agent.md) och [lägsta versions stöd för virtuella dator agenter i Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

Rätt version av gäst agent tillägget Microsoft. Azure. Security. AzureDiskEncryption eller Microsoft. Azure. Security. AzureDiskEncryptionForLinux krävs också. Tilläggs versioner underhålls och uppdateras automatiskt av plattformen när krav för Azure Virtual Machine agent är uppfyllda och en version av den virtuella dator agenten som stöds används.

Tillägget Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux är föråldrat och stöds inte längre.  

## <a name="unable-to-encrypt-linux-disks"></a>Det gick inte att kryptera Linux-diskar

I vissa fall kan har Linux diskkryptering verkar ha fastnat på ”OS diskkryptering igång” och SSH inaktiverats. Krypteringsprocessen kan ta mellan 3-16 timmar att slutföra på en lagerartiklar galleriavbildning. Om flera terabyte storlek datadiskar läggs kan processen ta dagar.

Linux OS-disk kryptering sekvensen demonterar operativsystemenheten tillfälligt. Den utför sedan block för block kryptering av hela OS-disken innan den monterar om i det krypterade tillståndet. Linux Disk Encryption tillåter inte samtidig användning av den virtuella datorn medan krypteringen pågår. Prestandaegenskaperna för den virtuella datorn kan göra stor skillnad i den tid som krävs för att slutföra krypteringen. Följande egenskaper är storleken på disken och om lagringskontot är standard eller (SSD) premiumlagring.

Om du vill kontrol lera krypterings statusen avsöker du fältet **ProgressMessage** som returnerades från kommandot [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . Medan operativsystemenheten krypteras den virtuella datorn försätts i ett tillstånd för underhåll och inaktiverar SSH för att förhindra eventuella avbrott i den pågående processen. **EncryptionInProgress** -meddelande rapporter för majoriteten av tiden medan kryptering pågår. Flera timmar senare visas ett **VMRestartPending** -meddelande där du ombeds att starta om den virtuella datorn. Exempel:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

När du uppmanas att starta om den virtuella datorn och när den virtuella datorn har startats om måste du vänta 2 – 3 minuter för omstarten och för de sista stegen som ska utföras på målet. Meddelandet statusändringar när krypteringen är slutligen Slutför. När det här meddelandet är tillgängligt, krypterade operativsystemenheten förväntas vara redo att användas och den virtuella datorn är redo att användas igen.

I följande fall rekommenderar vi att du återställer den virtuella datorn tillbaka till ögonblicksbild eller säkerhetskopior som gjorts omedelbart före kryptering:
   - Om omstart-sekvensen som beskrivs ovan, inte inträffa.
   - Om startinformation, pågående meddelande eller andra indikatorer felrapport misslyckades att OS-kryptering mitt i den här processen. Ett exempel på ett meddelande är felet ”Det gick inte att demontera” som beskrivs i den här guiden.

Dags att omvärdera egenskaperna för den virtuella datorn och kontrollera att alla krav är uppfyllda innan nästa försök.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Felsöka Azure Disk Encryption bakom en brandvägg

Se [disk kryptering i ett isolerat nätverk](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Felsöka krypterings status 

Portalen kan visa en disk som krypterad även efter att den har krypterats på den virtuella datorn.  Detta kan inträffa när lågnivå kommandon används för att direkt dekryptera disken inifrån den virtuella datorn, i stället för att använda Azure Disk Encryption hanterings kommandon på högre nivå.  Med kommandona på högre nivå kan du inte bara dekryptera disken inifrån den virtuella datorn, men utanför den virtuella datorn uppdaterar de också viktiga krypterings inställningar och tilläggs inställningar som är associerade med den virtuella datorn.  Om dessa inte behålls i justeringen kommer plattformen inte att kunna rapportera krypterings status eller upprätta den virtuella datorn korrekt.   

Om du vill inaktivera Azure Disk Encryption med PowerShell använder du [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) följt av [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Det går inte att köra Remove-AzVMDiskEncryptionExtension innan krypteringen inaktive ras.

Om du vill inaktivera Azure Disk Encryption med CLI använder du [inaktivera AZ VM-kryptering](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig mer om några vanliga problem i Azure Disk Encryption och hur du felsöker dessa problem. Mer information om den här tjänsten och dess funktioner finns i följande artiklar:

- [Använd disk kryptering i Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Azure Data Encryption i vila](../../security/fundamentals/encryption-atrest.md)
