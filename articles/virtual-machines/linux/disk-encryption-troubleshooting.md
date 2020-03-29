---
title: Felsöka Azure Disk Encryption för virtuella Linux-datorer
description: Den här artikeln innehåller felsökningstips för virtuella virtuella linux-datorer med Microsoft Azure Disk Encryption for Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: eeacea9e3305865881747801100dc17770b7df63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970494"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Felsökningsguide för Azure Disk Encryption

Den här guiden är avsedd för IT-proffs, informationssäkerhetsanalytiker och molnadministratörer vars organisationer använder Azure Disk Encryption. Den här artikeln är till hjälp med felsökning av diskkrypteringsrelaterade problem.

Innan du gör något av stegen nedan, se först till att de virtuella datorerna som du försöker kryptera är bland de [vm-storlekar och operativsystem](disk-encryption-overview.md#supported-vms-and-operating-systems)som stöds och att du har uppfyllt alla förutsättningar:

- [Ytterligare krav för virtuella datorer](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Krav på nätverk](disk-encryption-overview.md#networking-requirements)
- [Lagringskrav för krypteringsnyckel](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Felsöka diskkryptering av Linux OS

Linux operativsystem (OS) diskkryptering måste avmontera OS-enheten innan du kör den genom hela diskkrypteringsprocessen. Om den inte kan avmontera enheten kunde ett felmeddelande av "det gick inte att avmontera efter ..." sannolikt kommer att inträffa.

Det här felet kan uppstå när OS-diskkryptering försökers på en virtuell dator med en miljö som har ändrats från den som stöds stock galleri avbildning. Avvikelser från den bild som stöds kan störa tilläggets förmåga att avmontera OS-enheten. Exempel på avvikelser kan vara följande:
- Anpassade avbildningar matchar inte längre ett filsystem eller partitioneringsschema som stöds.
- Stora program som SAP, MongoDB, Apache Cassandra och Docker stöds inte när de installeras och körs i operativsystemet före kryptering. Azure Disk Encryption kan inte stänga av dessa processer på ett säkert sätt som krävs för att förbereda OS-enheten för diskkryptering. Om det fortfarande finns aktiva processer som innehåller öppna filreferenser till OS-enheten kan OS-enheten inte avmonteras, vilket resulterar i ett fel att kryptera OS-enheten. 
- Anpassade skript som körs i närheten av krypteringen som aktiveras, eller om några andra ändringar görs på den virtuella datorn under krypteringsprocessen. Den här konflikten kan inträffa när en Azure Resource Manager-mall definierar flera tillägg som ska köras samtidigt, eller när ett anpassat skripttillägg eller annan åtgärd körs samtidigt till diskkryptering. Serialisering och isolering av sådana steg kan lösa problemet.
- Security Enhanced Linux (SELinux) har inte inaktiverats innan kryptering aktiveras, så avmonteringssteget misslyckas. SELinux kan återsänas när krypteringen är klar.
- OS-disken använder ett LVM-schema (Logical Volume Manager). Även om begränsat stöd för LVM-datadiskar är tillgängligt, är en LVM OS-disk inte.
- Minimikrav på minne uppfylls inte (7 GB föreslås för OS-diskkryptering).
- Dataenheter monteras rekursivt under katalogen /mnt/eller varandra (till exempel /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Uppdatera standardkärnan för Ubuntu 14.04 LTS

Ubuntu 14.04 LTS-avbildningen levereras med en standardkärnversion av 4.4. Den här kärnversionen har ett känt problem där Out of Memory Killer felaktigt avslutar DD-kommandot under OS-krypteringsprocessen. Det här felet har åtgärdats i den senaste Azure-trimmade Linux-kärnan. För att undvika det här felet, innan du aktiverar kryptering på avbildningen, uppdaterar du till [Azure-trimmad kärna 4.15](https://packages.ubuntu.com/trusty/linux-azure) eller senare med hjälp av följande kommandon:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

När den virtuella datorn har startats om till den nya kärnan kan den nya kärnversionen bekräftas med:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Uppdatera Azure Virtual Machine Agent och tilläggsversioner

Azure Disk Encryption-åtgärder kan misslyckas på avbildningar på virtuella datorer med hjälp av versioner av Azure Virtual Machine Agent som inte stöds. Linux-avbildningar med agentversioner tidigare än 2.2.38 bör uppdateras innan kryptering aktiveras. Mer information finns i [Så här uppdaterar du Azure Linux-agenten på ett vm-](../extensions/update-linux-agent.md) och [minimum-versionsstöd för virtuella datoragenter i Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

Rätt version av gästagenttillägget Microsoft.Azure.Security.AzureDiskEncryption eller Microsoft.Azure.Security.AzureDiskEncryptionForLinux krävs också. Tilläggsversioner underhålls och uppdateras automatiskt av plattformen när Azure Virtual Machine-agentens förutsättningar är uppfyllda och en version av den virtuella datorn används.

Tillägget Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux har inaktuellt och stöds inte längre.  

## <a name="unable-to-encrypt-linux-disks"></a>Det går inte att kryptera Linux-diskar

I vissa fall verkar Linux-diskkrypteringen ha fastnat på "OS diskkryptering startad" och SSH är inaktiverat. Krypteringsprocessen kan ta mellan 3-16 timmar att slutföra på en arkivbild. Om datadiskar i flera terabyte-storlekar läggs till kan processen ta dagar.

Linux OS-diskkrypteringssekvensen avmonterar OS-enheten tillfälligt. Den utför sedan block-för-block-kryptering av hela OS-disken, innan den återmonterar den i sitt krypterade tillstånd. Linux Diskkryptering tillåter inte samtidig användning av den virtuella datorn medan krypteringen pågår. Prestandaegenskaperna för den virtuella datorn kan göra en betydande skillnad i den tid som krävs för att slutföra kryptering. Dessa egenskaper inkluderar storleken på disken och om lagringskontot är standard- eller premiumlagring (SSD).

Om du vill kontrollera krypteringsstatusen avsöker du fältet **ProgressMessage** som returneras från kommandot [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Medan OS-enheten krypteras går den virtuella datorn in i ett servicetillstånd och inaktiverar SSH för att förhindra störningar i den pågående processen. Meddelandet **EncryptionInProgress** rapporterar för större delen av tiden medan krypteringen pågår. Flera timmar senare **VMRestartPending** uppmanas du att starta om den virtuella datorn. Ett exempel:


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

När du uppmanas att starta om den virtuella datorn, och efter att den virtuella datorn startas om, måste du vänta 2-3 minuter för omstarten och för de sista stegen som ska utföras på målet. Statusmeddelandet ändras när krypteringen slutligen är klar. När det här meddelandet är tillgängligt förväntas den krypterade OS-enheten vara klar för användning och den virtuella datorn är klar att användas igen.

I följande fall rekommenderar vi att du återställer den virtuella datorn till ögonblicksbilden eller säkerhetskopian som tas omedelbart före krypteringen:
   - Om omstartssekvensen, som beskrivits tidigare, inte inträffar.
   - Om startinformation, förloppsmeddelande eller andra felindikatorer rapporterar att OS-kryptering har misslyckats mitt i den här processen. Ett exempel på ett meddelande är felet "det gick inte att avmontera" som beskrivs i den här guiden.

Före nästa försök, omvärdera egenskaperna för den virtuella datorn och se till att alla förutsättningar är uppfyllda.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Felsöka Azure Disk-kryptering bakom en brandvägg

Se [Diskkryptering i ett isolerat nätverk](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Felsöka krypteringsstatus 

Portalen kan visa en disk som krypterad även efter att den har okrypterats inom den virtuella datorn.  Detta kan inträffa när kommandon på låg nivå används för att direkt okryptera disken från den virtuella datorn, i stället för att använda azure diskkrypteringshanteringskommandon på högre nivå.  Kommandon på högre nivå inte bara okryptera disken inifrån den virtuella datorn, men utanför den virtuella datorn uppdaterar de också viktiga krypteringsinställningar på plattformsnivå och tilläggsinställningar som är associerade med den virtuella datorn.  Om dessa inte hålls i linje kan plattformen inte rapportera krypteringsstatus eller etablera den virtuella datorn korrekt.   

Om du vill inaktivera Azure Disk Encryption med PowerShell använder du [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) följt av [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Det går inte att köra Ta bort-AzVMDiskEncryptionExtension innan krypteringen inaktiveras.

Om du vill inaktivera Azure Disk Encryption med CLI använder du [az vm-kryptering inaktivera](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig mer om några vanliga problem i Azure Disk Encryption och hur du felsöker dessa problem. Mer information om den här tjänsten och dess funktioner finns i följande artiklar:

- [Använda diskkryptering i Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Azure-datakryptering i vila](../../security/fundamentals/encryption-atrest.md)
