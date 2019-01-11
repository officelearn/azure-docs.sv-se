---
title: Vanliga frågor och svar – Azure Disk Encryption för virtuella IaaS-datorer | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor och svar om Microsoft Azure Disk Encryption för Windows och Linux IaaS-datorer.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 12/21/2018
ms.custom: seodec18
ms.openlocfilehash: 3afe60e7a00cb4fc4aca5046da8e26aaf3c77a29
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198821"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Azure Disk Encryption för virtuella IaaS-datorer: vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om Azure Disk Encryption för Windows och Linux IaaS-datorer. Mer information om den här tjänsten finns i [Azure Disk Encryption för Windows och Linux IaaS-datorer](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Var finns Azure Disk Encryption i allmän tillgänglighet (GA)?

Azure Disk Encryption för Windows och Linux IaaS-datorer är allmänt tillgängliga i alla offentliga Azure-regioner.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Vilken användare som inträffar är tillgängliga med Azure Disk Encryption?

Azure Disk Encryption GA stöder Azure Resource Manager-mallar, Azure PowerShell och Azure CLI. Erfarenheterna från olika användare ger dig flexibilitet. Du har tre olika alternativ för att aktivera diskkryptering för din virtuella IaaS-datorer. Mer information om användarupplevelsen och stegvisa anvisningar som är tillgängliga i Azure Disk Encryption finns [aktivera Azure Disk Encryption för Windows](azure-security-disk-encryption-windows.md) och [aktivera Azure Disk Encryption för Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Hur mycket kostar Azure Disk Encryption?

Det är kostnadsfritt för att kryptera Virtuella diskar med Azure Disk Encryption men det kostar som är associerade med hjälp av Azure Key Vault. Mer information om kostnader för Azure Key Vault finns i den [prissättning för Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) sidan.


## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Vilka nivåer för virtual Machines har stöd för Azure Disk Encryption?

Azure Disk Encryption är tillgängliga på standard-nivån virtuella datorer, inklusive [A, D, DS, G, GS och F](https://azure.microsoft.com/pricing/details/virtual-machines/) serien virtuella IaaS-datorer. Det är också tillgänglig för virtuella datorer med premium storage. Det är inte tillgängligt i basic-nivån virtuella datorer.

## <a name="bkmk_LinuxOSSupport"></a> Vilka Linux-distributioner har stöd för Azure Disk Encryption?

Azure Disk Encryption stöds på följande Linux-server-distributioner och versioner:

| Linux-distribution | Version | Volymtyp som stöds för kryptering|
| --- | --- |--- |
| Ubuntu | 16.04 DAGLIGA LTS | OS- och disk |
| Ubuntu | 14.04.5-DAILY-LTS | OS- och disk |
| RHEL | 7.5 | OS- och disk * |
| RHEL | 7.4 | OS- och disk * |
| RHEL | 7.3 | OS- och disk * |
| RHEL | 7.2 | OS- och disk * |
| RHEL | 6.8 | Data disk * |
| RHEL | 6.7 | Data disk * |
| CentOS | 7.4 | OS- och disk |
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
| SLES | Prioritet: 11 SP4 | Datadisk |
| SLES | 11 SP4 | Datadisk |


*__Ny ADE implementering har stöd för RHEL-OS- och datadisk för RHEL7 betala per användning-avbildningar. ADE stöds för närvarande inte för RHEL Bring-Your-Own-prenumeration (BYOS)-avbildningar. Också finns den [Azure Disk Encryption för Linux](azure-security-disk-encryption-linux.md) artikeln för mer information.__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hur kan jag börja använda Azure Disk Encryption?

Kom igång genom att läsa den [översikt över Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kan jag kryptera både start- och datavolymer med Azure Disk Encryption?

Ja, du kan kryptera start- och datavolymer för Windows och Linux IaaS-datorer. Du kan inte krypteras data utan att första kryptera operativsystemvolymen för Windows-datorer. Det är möjligt att kryptera datavolymen utan att behöva kryptera operativsystemvolymen först för virtuella Linux-datorer. När du har krypterat operativsystemvolymen för Linux, stöds inaktivering av kryptering på en OS-volym för virtuella Linux IaaS-datorer inte.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure Disk Encryption tillåter att du kan använda en egen nyckel (BYOK)?

Ja, kan du ange en egen nyckel krypteringsnycklar. Dessa nycklar skyddas i Azure Key Vault, vilket är nyckelarkivet för Azure Disk Encryption. Mer information om viktiga krypteringsnycklarna stöder scenarier, se [som krävs för Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Kan jag använda en Azure-skapade nyckelkrypteringsnyckel?

Ja, du kan använda Azure Key Vault för att generera en nyckel krypteringsnyckel för Azure disk encryption användning. Dessa nycklar skyddas i Azure Key Vault, vilket är nyckelarkivet för Azure Disk Encryption. Mer information om viktiga krypteringsnyckeln finns [som krävs för Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Kan jag använda en lokal nyckelhanteringstjänst eller HSM för att skydda krypteringsnycklar?

Du kan inte använda lokal nyckelhanteringstjänst eller HSM för att skydda krypteringsnycklar med Azure Disk Encryption. Du kan bara använda Azure Key Vault-tjänsten för att skydda krypteringsnycklar. Mer information om nyckelkryptering viktiga supportscenarier finns i [som krävs för Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Vilka är kraven för att konfigurera Azure Disk Encryption?

Det finns förutsättningar för Azure Disk Encryption. Se den [som krävs för Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) artikel om du vill skapa ett nytt nyckelvalv eller ställa in ett befintligt nyckelvalv för kryptering av diskåtkomst att aktivera kryptering, och skydda hemligheter och nycklar. Mer information om nyckelkryptering viktiga supportscenarier finns i [översikt över Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Vilka är kraven för att konfigurera Azure Disk Encryption med Azure AD-app (tidigare version)?

Det finns förutsättningar för Azure Disk Encryption. Se den [som krävs för Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) artikeln om du vill skapa ett Azure Active Directory-program, skapa ett nytt nyckelvalv, eller konfigurera ett befintligt nyckelvalv för kryptering av diskåtkomst att aktivera kryptering och skydda hemligheter och nycklar. Mer information om nyckelkryptering viktiga supportscenarier finns i [översikt över Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure Disk Encryption använder en Azure AD-app (tidigare version) som fortfarande stöds?
Ja. Diskkryptering som använder en Azure AD-app stöds fortfarande. Men när du krypterar nya virtuella datorer rekommenderas att du använder den nya metoden i stället för att kryptera med en Azure AD-app. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Kan jag migrera virtuella datorer som krypterats med en Azure AD-app till kryptering utan en Azure AD-app?
  Det finns inte för närvarande en direkt migreringsvägen för datorer som krypterats med en Azure AD-app till kryptering utan en Azure AD-app. Det finns inte dessutom en direkt sökväg från kryptering utan en Azure AD-app till kryptering med en AD-app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Vilken version av Azure PowerShell har stöd för Azure Disk Encryption?

Använd den senaste versionen av Azure PowerShell SDK för att konfigurera Azure Disk Encryption. Ladda ned den senaste versionen av [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption är *inte* stöds av Azure SDK version 1.1.0.

> [!NOTE]
> Linux Azure disk encryption preview-tillägget är inaktuell. Mer information finns i [avvecklar Azure disk encryption preview-tillägget för virtuella Linux IaaS-datorer](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Kan jag använda Azure Disk Encryption på min anpassade Linux-avbildning?

Du kan inte använda Azure Disk Encryption på din anpassade Linux-avbildning. Stöds endast i galleriet Linux-avbildningar för distributioner som stöds som beskrivs tidigare. Anpassade Linux-avbildningar stöds inte för närvarande.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Kan jag använda uppdateringar till virtuella Linux Red Hat-datorer som använder yum-uppdateringen?

Ja, kan du utföra en uppdatering eller uppdatera en Red Hat Linux-dator. Mer information finns i [utföra uppdateringar på en krypterade virtuella Azure IaaS Red Hat-datorer med hjälp av yum-update](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Vad är rekommenderade Azure disk encryption-arbetsflöde för Linux?

Följande arbetsflöde bör vara de bästa resultat på Linux:
* Starta från oförändrad lagerartiklar galleriet bilden som motsvarar de nödvändiga OS-distribution och version
* Säkerhetskopiera alla monterade enheter som kommer att krypteras.  Det möjliggör tillbaka upp återställning om det uppstår ett fel, till exempel om den virtuella datorn startas om innan kryptering har slutförts.
* Kryptera (kan ta flera timmar eller t.o.m. dagar beroende på VM-egenskaper och storleken på eventuella anslutna datadiskar)
* Anpassa och lägga till programvara i avbildningen efter behov.

Om det här arbetsflödet inte är möjligt, förlitar sig på [Lagringstjänstkryptering](../storage/common/storage-service-encryption.md) (SSE) på plattformen storage konto lager kan vara ett alternativ till fullständig diskkryptering med dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>Vad är den disken ”Bek volym” eller ”/ mnt/azure_bek_disk”?

”Bek volym” för Windows eller ”/ mnt/azure_bek_disk” om du för Linux är en lokal datavolym som lagras på ett säkert sätt krypteringsnycklarna för krypterade virtuella Azure IaaS-datorer.
> [!NOTE]
> Ta bort eller inte redigera något innehåll i den här disken. Demontera inte disken eftersom viktiga förekomsten kryptering krävs för krypteringsåtgärder på IaaS-VM.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Vilken krypteringsmetod som använder Azure Disk Encryption?

På Windows, ADE använder Bitlocker AES256 krypteringsmetod (AES256WithDiffuser på versioner före Windows Server 2012). På Linux använder ADE plain64-xts-aes dmcrypt standardvärdet med en 256-bitars volymens huvudnyckel.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Om jag använder EncryptFormatAll och anger alla volymtyper av, kommer den att radera data på dataenheter som vi redan har krypterat?
Nej, inte data raderas från enheter som redan är krypterade med Azure Disk Encryption. Liknande hur EncryptFormatAll inte kryptera operativsystemenheten, den kommer inte att kryptera enheten redan krypterade data. Mer information finns i den [EncryptFormatAll kriterier](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).        

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Var kan jag få ställa frågor eller lämna feedback?

Du kan ställa frågor eller lämna feedback om den [forum för Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig mer om de vanligaste frågor som rör Azure Disk Encryption. Mer information om den här tjänsten finns i följande artiklar:

- [Azure Disk Encryption-översikt](azure-security-disk-encryption-overview.md)
- [Tillämpa diskkryptering i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure datakryptering i vila](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
