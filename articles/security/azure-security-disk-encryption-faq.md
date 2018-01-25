---
title: "Azure Disk Encryption vanliga frågor och svar | Microsoft Docs"
description: "Den här artikeln innehåller svar på vanliga frågor och svar om Microsoft Azure Disk Encryption för Windows och Linux virtuella IaaS-datorer."
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: devtiw;ejarvi;mayank88mahajan;vermashi;sudhakarareddyevuri;aravindthoram
ms.openlocfilehash: 1144533cd64e80ed2b15c70732c94e332ea06c3d
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="azure-disk-encryption-faq"></a>Azure Disk Encryption vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om Azure Disk Encryption för Windows och Linux virtuella IaaS-datorer. Mer information om den här tjänsten finns [Azure Disk Encryption för Windows och Linux IaaS-VM](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="general-questions"></a>Allmänna frågor
**F:** där är Azure Disk Encryption i allmän tillgänglighet (GA)?

**S:** Azure Disk Encryption för Windows och Linux IaaS-VM är i allmänhet tillgänglighet i alla offentliga Azure-regioner.

**F:** vad användaren upplever är tillgängliga med Azure Disk Encryption?

**S:** Azure Disk Encryption GA stöder Azure Resource Manager-mallar, Azure PowerShell och Azure CLI. Detta ger dig stor flexibilitet. Det finns tre olika alternativ för att aktivera kryptering för IaaS-VM. Mer information om användarupplevelse och stegvisa anvisningar som är tillgängliga i Azure Disk Encryption finns i Azure Disk Encryption distributionsscenarier och erfarenheter.

**F:** hur mycket kostar Azure Disk Encryption?

**S:** är gratis för kryptering av Virtuella diskar med Azure Disk Encryption.

**F:** vilka nivåer av virtuella Azure Disk Encryption stöder?

**S:** Azure Disk Encryption är tillgängligt på standardnivån VMs inklusive [A, D, DS, G, GS och F](https://azure.microsoft.com/pricing/details/virtual-machines/) serien IaaS-VM. Det är också tillgänglig för virtuella datorer med premium-lagring. Det är inte tillgänglig på nivån basic virtuella datorer.

**F:** vad Linux-distributioner som stöder Azure Disk Encryption?

**S:** Azure Disk Encryption stöds på följande Linux-server-distributioner och versioner:

| Linux-distribution | Version | Volymtyp som stöds för kryptering|
| --- | --- |--- |
| Ubuntu | 16.04-VARJE DAG-LTS | OS- och disk |
| Ubuntu | 14.04.5-DAILY-LTS | OS- och disk |
| RHEL | 7.3 | OS- och disk |
| RHEL | 7.2 | OS- och disk |
| RHEL | 6.8 | OS- och disk |
| RHEL | 6.7 | Datadisk |
| CentOS | 7.3 | OS- och disk |
| CentOS | 7.2n | OS- och disk |
| CentOS | 6.8 | OS- och disk |
| CentOS | 7.1 | Datadisk |
| CentOS | 7.0 | Datadisk |
| CentOS | 6.7 | Datadisk |
| CentOS | 6.6 | Datadisk |
| CentOS | 6.5 | Datadisk |
| openSUSE | 13.2 | Datadisk |
| SLES | 12 SP1 | Datadisk |
| SLES | Prioritet: 12-SP1 | Datadisk |
| SLES | HPC 12 | Datadisk |
| SLES | Priority:11-SP4 | Datadisk |
| SLES | 11 SP4 | Datadisk |

**F:** hur kan jag börja använda Azure Disk Encryption?

**S:** att komma igång, Läs den [Azure Disk Encryption för Windows och Linux IaaS-VM](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) vitboken.

**F:** kan jag kryptera både start- och datavolymer med Azure Disk Encryption?

**S:** Ja, du kan kryptera start- och datavolymer för Windows och Linux virtuella IaaS-datorer. För virtuella Windows-datorer kan kryptera du inte data utan att första kryptera systemvolymen. För Linux virtuella datorer kan kryptera du datavolym utan att behöva kryptera systemvolymen först. När du har krypterat systemvolymen för Linux, stöds om du inaktiverar kryptering på en OS-volym för Linux virtuella IaaS-datorer inte.

**F:** har Azure Disk Encryption kan du ta med din egen nyckel (BYOK)-funktionen?

**S:** Ja, du kan ange en egen nyckel krypteringsnycklar. Dessa nycklar skyddas i Azure Key Vault som är KeyStore för Azure Disk Encryption. Mer information om viktiga krypteringsnycklarna stöd för scenarier, se Azure Disk Encryption distributionsscenarier och erfarenheter.

**F:** kan jag använda en Azure-skapade viktiga krypteringsnyckel?

**S:** Ja, du kan använda Azure Key Vault för att generera en krypteringsnyckel nyckel för Azure disk encryption användning. Dessa nycklar skyddas i Azure Key Vault som är KeyStore för Azure Disk Encryption. Mer information om stöd för nyckel-scenarier nyckelkryptering finns i Azure Disk Encryption distributionsscenarier och erfarenheter.

**F:** kan jag använda en lokal nyckelhanteringstjänst eller HSM för att skydda krypteringsnycklarna?

**S:** du inte använda lokala nyckelhanteringstjänst eller HSM för att skydda krypteringsnycklar med Azure Disk Encryption. Du kan bara använda Azure Key Vault-tjänsten för att skydda krypteringsnycklarna. Mer information om stöd för nyckel-scenarier nyckelkryptering finns i Azure Disk Encryption distributionsscenarier och erfarenheter.

**F:** vad är förutsättningar för att kunna konfigurera Azure Disk Encryption?

**S:** det finns ett nödvändiga PowerShell-skript. Med det här skriptet kan du skapa ett Azure Active Directory-program, skapa ett nytt nyckelvalv eller ställa in en befintlig nyckelvalv för kryptering av diskåtkomst att aktivera kryptering och skydda hemligheter och nycklar. Mer information om stöd för nyckel-scenarier nyckelkryptering finns i Azure Disk Encryption krav och distributionsscenarier och erfarenheter.

**F:** där får mer information om hur du använder PowerShell för att konfigurera Azure Disk Encryption?

**S:** vi har några bra artiklar om hur du kan utföra grundläggande uppgifter för Azure Disk Encryption, samt mer avancerade scenarier. Grundläggande uppgifter finns [utforska Azure Disk Encryption med Azure PowerShell – del 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). För mer avancerade scenarier finns [utforska Azure Disk Encryption med Azure PowerShell – del 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

**F:** vilken version av Azure PowerShell Azure Disk Encryption stöder?

**S:** använder den senaste versionen av Azure PowerShell SDK för att konfigurera Azure Disk Encryption. Hämta den senaste versionen av [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption är *inte* stöds av Azure SDK version 1.1.0.

> [!NOTE]
> Linux Azure disk encryption preview tillägget är föråldrad. Mer information finns i [sluta Azure disk encryption preview tillägget för Linux IaaS-VM](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

**F:** kan jag använda Azure Disk Encryption på min anpassade Linux-avbildningen?

**S:** du kan inte använda Azure Disk Encryption på den anpassade Linux-avbildningen. Vi stöder bara galleriavbildningar för Linux för distributioner som stöds som beskrivs tidigare. Vi stöder för närvarande inte anpassade Linux-avbildningar.

**F:** kan jag installera uppdateringar för en virtuell Linux Red Hat-dator som använder yum uppdateringen?

**S:** Ja, du kan utföra en uppdatering eller korrigera en Red Hat virtuell Linux-dator. Mer information finns i [tillämpa uppdateringar på en krypterad Azure IaaS Red Hat-dator med hjälp av yum update](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

**F:** vad är rekommenderade Azure disk encryption arbetsflöde för Linux?

**S:** följande arbetsflöde du bör ha bästa resultat på Linux:
* Starta från oförändrade lager galleriet bilden överensstämmer med önskade OS distro och version
* Säkerhetskopiera alla monterade enheter som kommer att krypteras.  Detta gör det möjligt för återställning vid fel, till exempel om den virtuella datorn startas om innan kryptering har slutförts.
* Kryptera (kan ta flera timmar eller t.o.m. dagar beroende på vm-egenskaper och storleken på eventuella anslutna hårddiskar)
* Anpassa och lägga till program i bilden efter behov.

Om det här arbetsflödet inte är möjligt att förlita sig på [Lagringstjänstens kryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE) på lagringsplatsen plattform konto lager kan vara ett alternativ till fullständig diskkryptering med dm-crypt.

**F:** vad är disken ”Bek volym” eller ”/ mnt/azure_bek_disk”?

**S:** ”Bek volym” för Windows eller ”/ mnt/azure_bek_disk” om du för Linux är en volym med lokala data som lagras på ett säkert sätt krypteringsnycklarna för krypterade Azure IaaS-VM.
> [!NOTE]
> Inte ta bort eller redigera en innehållet i den här disken. Demontera inte disken eftersom encryption key förekomst krävs för alla kryptering på IaaS-VM.

**F:** kan var jag ställa frågor eller ge feedback?

**S:** du kan ställa frågor eller ge feedback om den [Azure Disk Encryption forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet du lära dig mer om de vanligaste frågorna som rör Azure Disk Encryption. Mer information om den här tjänsten och dess funktioner finns i följande artiklar:

- [Tillämpa diskkryptering i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Kryptera en virtuell Azure-dator](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Azure datakryptering i viloläge](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
