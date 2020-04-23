---
title: Felsökningsguide för Azure Disk Encryption
description: Den här artikeln innehåller felsökningstips för Microsoft Azure DiskKryptering för virtuella Windows-datorer.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 11c1e0bf10725173a2a341addf4c3f845bbb7fba
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085696"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Felsökningsguide för Azure Disk Encryption

Den här guiden är avsedd för IT-proffs, informationssäkerhetsanalytiker och molnadministratörer vars organisationer använder Azure Disk Encryption. Den här artikeln är till hjälp med felsökning av diskkrypteringsrelaterade problem.

Innan du gör något av stegen nedan, se först till att de virtuella datorerna som du försöker kryptera är bland de [vm-storlekar och operativsystem](disk-encryption-overview.md#supported-vms-and-operating-systems)som stöds och att du har uppfyllt alla förutsättningar:

- [Krav på nätverk](disk-encryption-overview.md#networking-requirements)
- [Krav på grupprincip](disk-encryption-overview.md#group-policy-requirements)
- [Lagringskrav för krypteringsnyckel](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Felsöka Azure Disk-kryptering bakom en brandvägg

När anslutningen begränsas av en brandvägg, proxykrav eller NSG-inställningar (Network Security Group) kan möjligheten för tillägget att utföra nödvändiga uppgifter störas. Den här störningen kan resultera i statusmeddelanden som "Tilläggsstatus är inte tillgänglig på den virtuella datorn". I förväntade scenarier misslyckas krypteringen att slutföras. De avsnitt som följer har några vanliga brandväggsproblem som du kan undersöka.

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
Alla nätverkssäkerhetsgruppinställningar som tillämpas måste fortfarande göra det möjligt för slutpunkten att uppfylla de dokumenterade [nätverkskonfigurationsförutsättningarna](disk-encryption-overview.md#networking-requirements) för diskkryptering.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault bakom en brandvägg

När kryptering aktiveras med [Azure AD-autentiseringsuppgifter](disk-encryption-windows-aad.md#)måste måldatorn tillåta anslutning till både Azure Active Directory-slutpunkter och Key Vault-slutpunkter. Aktuella slutpunkter för Azure Active Directory-autentisering finns i avsnitten 56 och 59 i dokumentationen för [Office 365-URL:er och IP-adressintervall.](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) Key Vault-instruktioner finns i dokumentationen om hur du [kommer åt Azure Key Vault bakom en brandvägg](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure-instansmetadatatjänst 
Den virtuella datorn måste kunna komma åt [Azure Instance Metadata-tjänstslutpunkten](../windows/instance-metadata-service.md) som använder`169.254.169.254`en välkänd icke-dirigerbar IP-adress ( ) som endast kan nås från den virtuella datorn.  Proxykonfigurationer som ändrar lokal HTTP-trafik till den här adressen (till exempel att lägga till ett X-Forwarded-For-huvud) stöds inte.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Felsöka Windows Server 2016 Server Core

På Windows Server 2016 Server Core är bdehdcfg-komponenten inte tillgänglig som standard. Den här komponenten krävs av Azure Disk Encryption. Det används för att dela systemvolymen från OS-volymen, vilket görs endast en gång för livslängden för den virtuella datorn. Dessa binärfiler krävs inte under senare krypteringsåtgärder.

Du kan lösa problemet genom att kopiera följande fyra filer från en virtuell Windows Server 2016-datacenter till samma plats på Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Ange följande kommando:

   ```
   bdehdcfg.exe -target default
   ```

1. Det här kommandot skapar en systempartition på 550 MB. Starta om systemet.

1. Använd DiskPart för att kontrollera volymerna och fortsätt sedan.  

Ett exempel:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Felsöka krypteringsstatus 

Portalen kan visa en disk som krypterad även efter att den har okrypterats inom den virtuella datorn.  Detta kan inträffa när kommandon på låg nivå används för att direkt okryptera disken från den virtuella datorn, i stället för att använda azure diskkrypteringshanteringskommandon på högre nivå.  Kommandon på högre nivå inte bara okryptera disken inifrån den virtuella datorn, men utanför den virtuella datorn uppdaterar de också viktiga krypteringsinställningar på plattformsnivå och tilläggsinställningar som är associerade med den virtuella datorn.  Om dessa inte hålls i linje kan plattformen inte rapportera krypteringsstatus eller etablera den virtuella datorn korrekt.   

Om du vill inaktivera Azure Disk Encryption med PowerShell använder du [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) följt av [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Det går inte att köra Ta bort-AzVMDiskEncryptionExtension innan krypteringen inaktiveras.

Om du vill inaktivera Azure Disk Encryption med CLI använder du [az vm-kryptering inaktivera](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig mer om några vanliga problem i Azure Disk Encryption och hur du felsöker dessa problem. Mer information om den här tjänsten och dess funktioner finns i följande artiklar:

- [Använda diskkryptering i Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Azure-datakryptering i vila](../../security/fundamentals/encryption-atrest.md)
