---
title: Felsökning – Azure Disk Encryption för virtuella IaaS-datorer | Microsoft Docs
description: Den här artikeln innehåller felsökningstips för Microsoft Azure Disk Encryption för Windows och Linux IaaS-datorer.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 01/08/2018
ms.custom: seodec18
ms.openlocfilehash: 36ecfe8942d263ed84e430b01727743ed2cad00c
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103173"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Felsökningsguide för Azure Disk Encryption

Den här guiden är för IT-proffs, informationssäkerhetsanalytiker och molnadministratörer i organisationer som använder Azure Disk Encryption. Den här artikeln är att hjälpa till med felsökning av disk-kryptering-relaterade problem.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Felsökning av Linux OS-diskkryptering

Diskkryptering för Linux-operativsystem (OS) måste demontera operativsystemenheten innan du kör via krypteringsprocessen fullständig disk. Om det går inte att koppla bort enheten, ett felmeddelande för ”det gick inte att demontera efter...” är sannolikt kan förekomma.

Det här felet kan inträffa när OS-diskkryptering testas på en målmiljö för virtuell dator som har ändrats från stöds lagerartiklar galleriet bilden. Avvikelser från avbildningen som stöds kan störa tilläggets möjligheten att demontera operativsystemenheten. Exempel på avvikelser kan innehålla följande objekt:
- Anpassade avbildningar matchar inte längre ett filsystem som stöds eller partitioneringsschema.
- Stora program som SAP, MongoDB, Apache Cassandra och Docker stöds inte när de är installerade och körs i Operativsystemet innan du kryptering. Azure Disk Encryption kan inte stänga av de här processerna som krävs som förberedelse för operativsystemenheten för diskkryptering på ett säkert sätt. Om det finns fortfarande aktiva processer med öppna filhandtag till operativsystemenheten, får inte operativsystemenheten vara demonterats, vilket resulterar i ett fel att kryptera operativsystemenheten. 
- Anpassade skript som körs i Stäng tid närhet till kryptering som är aktiverade, eller om någon annan ändringar görs på den virtuella datorn under krypteringsprocessen. Den här konflikten kan inträffa när en Azure Resource Manager-mall definierar flera tillägg för att köra samtidigt, eller när ett anpassat skripttillägg eller annan åtgärd körs samtidigt till diskkryptering. Serialisering och isolera sådana åtgärder kan lösa problemet.
- Security förbättrad Linux (SELinux) har inte inaktiverats innan du aktiverar kryptering, så att demontera steg misslyckas. Vara kan reenabled SELinux när kryptering är klar.
- OS-disken använder ett schema för logiska Volume Manager (LVM). Även om det finns begränsat LVM data disk-stöd är inte en LVM OS-disk.
- Minimikraven på minne inte uppfylls (7 GB rekommenderas för OS-diskkryptering).
- Dataenheter är rekursivt monterad under katalogen /mnt/ eller varandra (till exempel /mnt/data1, /mnt/data2, /data3 + /data3/data4).
- Andra Azure Disk Encryption [krav](azure-security-disk-encryption-prerequisites.md) för Linux inte uppfylls.

## <a name="unable-to-encrypt"></a>Det går inte att kryptera

I vissa fall kan har Linux diskkryptering verkar ha fastnat på ”OS diskkryptering igång” och SSH inaktiverats. Krypteringsprocessen kan ta mellan 3-16 timmar att slutföra på en lagerartiklar galleriavbildning. Om flera terabyte storlek datadiskar läggs kan processen ta dagar.

Linux OS-disk kryptering sekvensen demonterar operativsystemenheten tillfälligt. Den utför sedan block för block kryptering av hela OS-disken innan den monterar om i det krypterade tillståndet. Till skillnad från Azure Disk Encryption på Windows kan Linux-diskkryptering inte för samtidig användning av den virtuella datorn medan kryptering pågår. Prestandaegenskaperna för den virtuella datorn kan göra stor skillnad i den tid som krävs för att slutföra krypteringen. Följande egenskaper är storleken på disken och om lagringskontot är standard eller (SSD) premiumlagring.

Om du vill kontrollera krypteringsstatus för, avsöker den **ProgressMessage** fält som returneras från den [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) kommando. Medan operativsystemenheten krypteras den virtuella datorn försätts i ett tillstånd för underhåll och inaktiverar SSH för att förhindra eventuella avbrott i den pågående processen. Den **EncryptionInProgress** meddelande rapporter för flesta av tiden medan kryptering pågår. Flera timmar senare, en **VMRestartPending** uppmanas du att starta om den virtuella datorn. Exempel:


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
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
När anslutningen är begränsad av en brandvägg eller proxy kravet nätverkssäkerhetsgrupper (NSG) för nätverkssäkerhet, kan möjligheten för tillägget för att utföra nödvändiga åtgärder avbrytas. Den här avbrott kan resultera i statusmeddelanden, till exempel ”tillståndets status är inte tillgänglig på den virtuella datorn”. I förväntade scenarier krypteringen inte kan slutföras. Avsnitten som följer har några vanliga brandväggsproblem som du kan undersöka.

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
Alla inställningar för nätverkssäkerhetsgrupper som tillämpas måste fortfarande tillåta slutpunkten så att den uppfyller dokumenterade nätverkskonfigurationen [krav](azure-security-disk-encryption-prerequisites.md#bkmk_GPO) för diskkryptering.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault bakom en brandvägg
Den virtuella datorn måste kunna komma åt ett nyckelvalv. Referera till vägledning för åtkomst till nyckelvalv bakom en brandvägg som den [Azure Key Vault](../key-vault/key-vault-access-behind-firewall.md) teamet bibehåller. 

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 
Den virtuella datorn måste kunna komma åt den [tjänsten Azure Instance Metadata](../virtual-machines/windows/instance-metadata-service.md) slutpunkt som använder en välkänd icke-dirigerbara IP-adress (`169.254.169.254`) som kan nås från den virtuella datorn.

### <a name="linux-package-management-behind-a-firewall"></a>Linux pakethantering bakom en brandvägg

Vid körning, Azure Disk Encryption för Linux förlitar sig på mål-distribution systemet för pakethantering att installera nödvändiga komponenter innan du aktiverar kryptering. Om brandväggsinställningarna att den virtuella datorn inte kan ladda ned och installera de här komponenterna, förväntas efterföljande försök. Hur du konfigurerar det här systemet för pakethantering kan variera beroende på distributionsplatsen. På Red Hat, när en proxyserver krävs, måste du se till att prenumerationen manager och yum har ställts in korrekt. Mer information finns i [så här felsöker du problem med prenumeration manager och yum](https://access.redhat.com/solutions/189533).  


## <a name="troubleshooting-windows-server-2016-server-core"></a>Felsökning av Windows Server 2016 Server Core

På Windows Server 2016 Server Core är bdehdcfg komponenten inte tillgängligt som standard. Den här komponenten kräver Azure Disk Encryption. Den används för att dela upp systemvolymen från OS-volym, vilket görs bara en gång för livstiden för den virtuella datorn. Dessa binärfiler inte behövs under senare krypteringsåtgärder.

Undvik problemet genom att kopiera följande fyra filer från en Windows Server 2016 Data Center VM till samma plats på Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

   2. Ange följande kommando:

   ```
   bdehdcfg.exe -target default
   ```

   3. Det här kommandot skapar en 550 MB systempartition. Starta om systemet.

   4. Använd DiskPart för att kontrollera volymerna och fortsätt sedan.  

Exempel:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
<!-- ## Troubleshooting encryption status

If the expected encryption state does not match what is being reported in the portal, see the following support article:
[Encryption status is displayed incorrectly on the Azure Management Portal](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por) --> 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig mer om några vanliga problem i Azure Disk Encryption och hur du felsöker dessa problem. Mer information om den här tjänsten och dess funktioner finns i följande artiklar:

- [Tillämpa diskkryptering i Azure Security Center](../security-center/security-center-apply-disk-encryption.md)
- [Azure datakryptering i vila](azure-security-encryption-atrest.md)
