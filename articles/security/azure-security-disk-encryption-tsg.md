---
title: Felsökning för Azure Disk Encryption | Microsoft Docs
description: Den här artikeln innehåller felsökningstips för Microsoft Azure Disk Encryption för Windows och Linux virtuella IaaS-datorer.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: DevTiw
ms.openlocfilehash: 4bc1f7cf64a2a08215cd82d8e72e40eba8db3093
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Felsökningsguide för Azure Disk Encryption

Den här guiden är för IT-proffs, informationssäkerhetsanalytiker och molnet administratörer vars organisationer att använda Azure Disk Encryption och vägledning för att felsöka problem med Diskhantering med kryptering.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Felsökning av Linux OS-diskkryptering

Linux-operativsystem (OS) diskkryptering måste demontera OS-enhet innan du kör via krypteringsprocessen fullständig disk. Om det går inte att demontera enheten och ett felmeddelande av ”det gick inte att demontera efter...” kommer troligen att inträffa.

Detta fel är stor sannolikhet kommer att hända när OS-diskkryptering görs på en målmiljön VM som har ändrats eller har ändrats från dess stöds lager galleriet avbildning. Följande är exempel på avvikelser från bildtyper som kan störa tilläggets möjlighet att demontera OS-enhet:
- Anpassade avbildningar matchar inte längre ett filsystem som stöds eller partitioneringsschema.
- Stora program, till exempel SAP, MongoDB, Apache Cassandra och Docker stöds inte när de är installerade och körs i OS före kryptering.  Azure Disk Encryption kan inte stänga på ett säkert sätt av dessa processer som krävs som förberedelse för OS-enhet för diskkryptering.  Om det finns fortfarande aktiva processer hålla öppna filreferenser på OS-enhet, får inte enhetens OS vara omonterade resulterar i att kryptera enheten OS. 
- Anpassade skript som körs i nära tiden närhet till att aktivera kryptering eller om andra ändringar görs på den virtuella datorn under krypteringsprocessen. Den här konflikten kan inträffa när en Azure Resource Manager-mall definierar flera tillägg för att köra samtidigt, eller när tillägget för anpassat skript eller annan åtgärd körs samtidigt för diskkryptering. Serialisering och isolera sådana åtgärder kan lösa problemet.
- Security Enhanced Linux (SELinux) har inte inaktiverats innan du aktiverar kryptering, så att koppla från steget misslyckas. Vara kan reenabled SELinux när kryptering är klar.
- OS-disken använder ett schema för logisk volym Manager (LVM). Även om det finns stöd för diskar begränsad LVM data är inte en LVM OS-disk.
- Minimikraven på minne är inte uppfyllda (7 GB rekommenderas för OS-diskkryptering).
- Dataenheter är rekursivt monteras under katalogen /mnt/ eller varandra (till exempel /mnt/data1, /mnt/data2, /data3 + /data3/data4).
- Andra Azure Disk Encryption [krav](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) för Linux är inte uppfyllda.

## <a name="unable-to-encrypt"></a>Det gick inte att kryptera

I vissa fall har Linux diskkryptering verkar ha fastnat i ”OS-disken kryptering igång” och SSH inaktiverats. Krypteringsprocessen kan ta mellan 3-16 timmar att slutföra på en bild för lager galleriet. Om flera terabyte storlek datadiskar läggs kan processen ta dagar.

Linux-Operativsystemdatorn disk encryption sekvensen demonterar tillfälligt OS-enhet. Block för block kryptering av hela OS-disken utför sedan innan den monterar om i det kryptera tillståndet. Till skillnad från Azure Disk Encryption i Windows tillåter Linux-diskkryptering inte samtidig användning av den virtuella datorn medan kryptering pågår. Prestandaegenskaperna för den virtuella datorn kan göra betydande skillnader vad gäller den tid som krävs till fullständig kryptering. Dessa egenskaper innehåller storleken på disken och om lagringskontot är standard eller (SSD) premiumlagring.

Om du vill kontrollera krypteringsstatus avsöka den **ProgressMessage** fält som returneras från den [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) kommando. När OS-enheten krypteras den virtuella datorn försätts i tillståndet underhåll och inaktiverar SSH för att förhindra eventuella avbrott i den pågående processen. Den **EncryptionInProgress** meddelande rapporter för flesta av tiden medan kryptering pågår. Flera timmar senare, en **VMRestartPending** du uppmanas att starta om den virtuella datorn. Exempel:


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

När du uppmanas att starta om den virtuella datorn och när den virtuella datorn har startats om måste du vänta 2-3 minuter för att omstart och de sista stegen som ska utföras på målet. Meddelandet statusändringar när kryptering är slutligen Slutför. När det här meddelandet är tillgänglig, den krypterade enheten OS förväntas vara redo för användning och den virtuella datorn är redo att användas igen.

I följande fall rekommenderar vi att du återställa den virtuella datorn till ögonblicksbild eller säkerhetskopior som gjorts omedelbart före kryptering:
   - Om omstart sekvensen beskrivs tidigare sker inte.
   - Om boot information, pågående meddelande eller andra indikatorer felrapporten misslyckades att OS-kryptering på den här processen. Felet ”Det gick inte att demontera” som beskrivs i den här guiden är ett exempel på ett meddelande.

Omvärdera egenskaperna för den virtuella datorn och se till att alla krav är uppfyllda innan nästa försök.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Felsöka Azure Disk Encryption bakom en brandvägg
När anslutningen är begränsad av en brandvägg, proxy krav eller grupp (NSG) för nätverkssäkerhet kan i tillägget möjligheten att utföra uppgifter som krävs avbrytas. Den här avbrott kan resultera i statusmeddelanden, till exempel ”tillståndets status är inte tillgänglig på den virtuella datorn”. I scenarier med förväntade misslyckas kryptering ska slutföras. Avsnitten som följer ha några vanliga brandväggsproblem som du kan undersöka.

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
En grupp för nätverkssäkerhet som ska användas måste fortfarande att slutpunkten att uppfylla dokumenterade nätverkskonfigurationen [krav](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites) för diskkryptering.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault bakom en brandvägg
Den virtuella datorn måste kunna komma åt en nyckelvalvet. Referera till hjälp med åtkomst till nyckelvalvet finns bakom en brandvägg som den [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall) team som underhåller.

### <a name="linux-package-management-behind-a-firewall"></a>Linux-paketet management bakom en brandvägg

Vid körning, Azure Disk Encryption för Linux förlitar sig på mål-distribution systemet för pakethantering att installera nödvändiga komponenter innan du aktiverar kryptering. Brandväggsinställningar förhindrar att den virtuella datorn från att kunna ladda ned och installera komponenterna, förväntas efterföljande försök. Hur du konfigurerar det här systemet för pakethantering kan variera av distribution. När en proxyserver krävs på Red Hat måste du kontrollera att prenumerationen manager och yum har ställts in korrekt. Mer information finns i [felsökning av problem med prenumeration manager och yum](https://access.redhat.com/solutions/189533).  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Felsöka Windows Server 2016 Server Core

Bdehdcfg-komponenten är inte tillgängligt som standard på Windows Server 2016 Server Core. Den här komponenten krävs av Azure Disk Encryption. Används för att dela upp systemvolymen från systemvolymen görs bara en gång för livslängden för den virtuella datorn. Dessa binärfiler krävs inte under senare kryptering.

För att lösa problemet, kopiera följande 4 filer från en Windows Server 2016 Data Center VM till samma plats på Server Core:

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

   3. Detta kommando skapar en 550 MB partition. Starta om systemet.

   4. Använd DiskPart för att kontrollera volymerna och gå sedan vidare.  

Exempel:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="troubleshooting-encryption-status"></a>Felsöka krypteringsstatus

Om den förväntade krypteringsstatus inte matchar vad som rapporteras i portalen, finns i följande supportartikeln: [krypteringsstatus visas felaktigt på Azure-hanteringsportalen](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por)

## <a name="next-steps"></a>Nästa steg

I det här dokumentet du lära dig mer om några vanliga problem i Azure Disk Encryption och hur du felsöker dessa problem. Mer information om den här tjänsten och dess funktioner finns i följande artiklar:

- [Tillämpa diskkryptering i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Kryptera en virtuell Azure-dator](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Azure datakryptering i viloläge](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
