---
title: Azure Disk Encryption vanliga frågor och svar | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor och svar om Microsoft Azure Disk Encryption för Windows och Linux virtuella IaaS-datorer.
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
ms.date: 04/27/2018
ms.author: barclayn
ms.openlocfilehash: 47ccf91a64653c928cc4da01bc98535c97440d37
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-disk-encryption-faq"></a>Azure Disk Encryption vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om Azure Disk Encryption för Windows och Linux virtuella IaaS-datorer. Mer information om den här tjänsten finns [Azure Disk Encryption för Windows och Linux IaaS-VM](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Var finns Azure Disk Encryption i allmän tillgänglighet (GA)?

Azure Disk Encryption för Windows och Linux virtuella IaaS-datorer är i allmänhet tillgänglig i alla offentliga Azure-regioner.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Vilken användaren upplever är tillgängliga med Azure Disk Encryption?

Azure Disk Encryption GA stöder Azure Resource Manager-mallar, Azure PowerShell och Azure CLI. Detta ger dig stor flexibilitet. Det finns tre olika alternativ för att aktivera kryptering för IaaS-VM. Mer information om användarupplevelse och stegvisa anvisningar som är tillgängliga i Azure Disk Encryption finns [Azure Disk Encryption distributionsscenarier och upplevelser](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Hur mycket kostar Azure Disk Encryption

Är gratis för kryptering av Virtuella diskar med Azure Disk Encryption men det finns avgifter som kopplas med hjälp av Azure Key Vault. Mer information om Azure Key Vault kostnader avser den [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/) sidan.

## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Vilka virtuella nivåer har stöd för Azure Disk Encryption?

Azure Disk Encryption är tillgängligt på standardnivån VMs inklusive [A, D, DS, G, GS och F](https://azure.microsoft.com/pricing/details/virtual-machines/) serien IaaS-VM. Det är också tillgänglig för virtuella datorer med premium-lagring. Det är inte tillgänglig på nivån basic virtuella datorer.

## <a name="what-linux-distributions-does-azure-disk-encryption-support"></a>Vilka Linux-distributioner som stöder Azure Disk Encryption?

Azure Disk Encryption stöds på följande Linux-server-distributioner och versioner:

| Linux-distribution | Version | Volymtyp som stöds för kryptering|
| --- | --- |--- |
| Ubuntu | 16.04-VARJE DAG-LTS | OS- och disk |
| Ubuntu | 14.04.5-DAILY-LTS | OS- och disk |
| RHEL | 7.4 | Data disk * |
| RHEL | 7.3 | Data disk * |
| RHEL | 7.2 | Data disk * |
| RHEL | 6.8 | Data disk * |
| RHEL | 6.7 | Data disk * |
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
| SLES | Prioritet: 11-SP4 | Datadisk |
| SLES | 11 SP4 | Datadisk |

*__ADE stöds för RHEL för datadisk. Den aktuella implementeringen ADE fungerar för OS-disken, men stöds inte för närvarande tillsammans. Både Microsoft och Red Hat arbetar på en lösning som gemensamt stöds. Under tiden kan du referera ADE vitboken för Linux OS-diskkryptering [här](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hur kan jag börja använda Azure Disk Encryption?

Att komma igång, Läs den [Azure Disk Encryption för Windows och Linux IaaS-VM](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) vitboken.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kan jag kryptera både start- och datavolymer med Azure Disk Encryption?

Ja, kan du kryptera start- och datavolymer för Windows och Linux virtuella IaaS-datorer. För virtuella Windows-datorer kan kryptera du inte data utan att första kryptera systemvolymen. För Linux virtuella datorer kan kryptera du datavolym utan att behöva kryptera systemvolymen först. När du har krypterat systemvolymen för Linux, stöds om du inaktiverar kryptering på en OS-volym för Linux virtuella IaaS-datorer inte.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>Azure Disk Encryption tillåter att du kan ta med din egen nyckel (BYOK)-funktionen?

Ja, kan du ange en egen nyckel krypteringsnycklar. Dessa nycklar skyddas i Azure Key Vault som är KeyStore för Azure Disk Encryption. Mer information om viktiga krypteringsnycklarna stöd för scenarier, se [Azure Disk Encryption distributionsscenarier och upplevelser](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Kan jag använda en Azure-skapade viktiga krypteringsnyckel?

Ja, du kan använda Azure Key Vault för att generera en krypteringsnyckel nyckel för Azure disk encryption användning. Dessa nycklar skyddas i Azure Key Vault som är KeyStore för Azure Disk Encryption. Mer information om scenarier för nyckelkryptering stöd för nyckel finns [Azure Disk Encryption distributionsscenarier och upplevelser](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Kan jag använda en lokal nyckelhanteringstjänst eller HSM för att skydda krypteringsnycklarna?

Du kan inte använda lokala nyckelhanteringstjänst eller HSM för att skydda krypteringsnycklar med Azure Disk Encryption. Du kan bara använda Azure Key Vault-tjänsten för att skydda krypteringsnycklarna. Mer information om scenarier för nyckelkryptering stöd för nyckel finns [Azure Disk Encryption distributionsscenarier och upplevelser](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Vad är förutsättningar för att kunna konfigurera Azure Disk Encryption?

Det finns ett nödvändiga PowerShell-skript. Med det här skriptet kan du skapa ett Azure Active Directory-program, skapa ett nytt nyckelvalv eller ställa in en befintlig nyckelvalv för kryptering av diskåtkomst att aktivera kryptering och skydda hemligheter och nycklar. Mer information om scenarier för nyckelkryptering stöd för nyckel finns [krav för Azure Disk Encryption och distributionsscenarier och upplevelser](azure-security-disk-encryption.md#prerequisites).

## <a name="where-can-i-get-more-information-on-how-to-use-powershell-for-configuring-azure-disk-encryption"></a>Var hittar jag mer information om hur du använder PowerShell för att konfigurera Azure Disk Encryption

Vi har några bra artiklar om hur du kan utföra grundläggande uppgifter för Azure Disk Encryption, samt mer avancerade scenarier. Grundläggande uppgifter finns [utforska Azure Disk Encryption med Azure PowerShell – del 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). För mer avancerade scenarier finns [utforska Azure Disk Encryption med Azure PowerShell – del 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Vilken version av Azure PowerShell stöder Azure Disk Encryption?

Använd den senaste versionen av Azure PowerShell SDK för att konfigurera Azure Disk Encryption. Hämta den senaste versionen av [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption är *inte* stöds av Azure SDK version 1.1.0.

> [!NOTE]
> Linux Azure disk encryption preview tillägget är föråldrad. Mer information finns i [sluta Azure disk encryption preview tillägget för Linux IaaS-VM](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Kan jag använda Azure Disk Encryption på min anpassade Linux-avbildningen?

Du kan inte använda Azure Disk Encryption på den anpassade Linux-avbildningen. Vi stöder bara galleriavbildningar för Linux för distributioner som stöds som beskrivs tidigare. Vi stöder för närvarande inte anpassade Linux-avbildningar.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Kan jag installera uppdateringar för en virtuell Linux Red Hat-dator som använder yum uppdateringen?

Ja, kan du utföra en uppdatering eller korrigera en Red Hat virtuell Linux-dator. Mer information finns i [tillämpa uppdateringar på en krypterad Azure IaaS Red Hat-dator med hjälp av yum update](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Vad är rekommenderade Azure disk encryption arbetsflöde för Linux?

Följande arbetsflöde bör ha bästa resultat på Linux:
* Starta från oförändrade lager galleriet bilden överensstämmer med önskade OS distro och version
* Säkerhetskopiera alla monterade enheter som kommer att krypteras.  Detta gör det möjligt för återställning vid fel, till exempel om den virtuella datorn startas om innan kryptering har slutförts.
* Kryptera (kan ta flera timmar eller t.o.m. dagar beroende på vm-egenskaper och storleken på eventuella anslutna hårddiskar)
* Anpassa och lägga till program i bilden efter behov.

Om det här arbetsflödet inte är möjligt att förlita sig på [Lagringstjänstens kryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE) på lagringsplatsen plattform konto lager kan vara ett alternativ till fullständig diskkryptering med dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>Vad är disken ”Bek volym” eller ”/ mnt/azure_bek_disk”?

”Bek volym” för Windows eller ”/ mnt/azure_bek_disk” för Linux är en lokal datavolym som lagras på ett säkert sätt krypteringsnycklarna för krypterade Azure IaaS-VM.
> [!NOTE]
> Inte ta bort eller redigera en innehållet i den här disken. Demontera inte disken eftersom encryption key förekomst krävs för alla kryptering på IaaS-VM.

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Där kan gå att ställa frågor eller ge feedback?

Du kan ställa frågor eller ge feedback om den [Azure Disk Encryption forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet du lära dig mer om de vanligaste frågorna som rör Azure Disk Encryption. Mer information om den här tjänsten och dess funktioner finns i följande artiklar:

- [Tillämpa diskkryptering i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Kryptera en virtuell Azure-dator](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Azure datakryptering i viloläge](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
