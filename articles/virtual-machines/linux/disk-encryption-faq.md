---
title: Vanliga frågor och svar – Azure Disk Encryption för virtuella Linux-datorer | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om Microsoft Azure disk kryptering för virtuella Linux IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: f8d31c8df4d073ccd744e792d1316ce02e4bf387
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828642"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Vanliga frågor och svar om IaaS VM Azure Disk Encryption

Den här artikeln innehåller svar på vanliga frågor och svar om Azure Disk Encryption för virtuella Linux-datorer. Mer information om den här tjänsten finns i [Azure Disk Encryption översikt](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Var finns Azure Disk Encryption i allmän tillgänglighet (GA)?

Azure Disk Encryption för virtuella Linux-datorer är allmänt tillgängliga i alla offentliga Azure-regioner.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Vilken användare som inträffar är tillgängliga med Azure Disk Encryption?

Azure Disk Encryption GA stöder Azure Resource Manager-mallar, Azure PowerShell och Azure CLI. Erfarenheterna från olika användare ger dig flexibilitet. Du har tre olika alternativ för att aktivera disk kryptering för dina virtuella datorer. Mer information om användar upplevelsen och stegvisa vägledningar som är tillgängliga i Azure Disk Encryption finns [Azure Disk Encryption scenarier för Linux](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Hur mycket kostar Azure Disk Encryption?

Det kostar inget att kryptera virtuella dator diskar med Azure Disk Encryption, men det finns kostnader som är kopplade till användningen av Azure Key Vault. Mer information om kostnader för Azure Key Vault finns i den [prissättning för Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) sidan.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hur kan jag börja använda Azure Disk Encryption?

Kom igång genom att läsa den [översikt över Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Vilka VM-storlekar och operativ system stöder Azure Disk Encryption?

I artikeln [Azure Disk Encryption översikt](disk-encryption-overview.md) visas de [VM-storlekar](disk-encryption-overview.md#supported-vm-sizes) och [virtuella dator operativ system](disk-encryption-overview.md#supported-operating-systems) som stöder Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kan jag kryptera både start- och datavolymer med Azure Disk Encryption?

Ja, du kan kryptera både start-och data volymer, eller så kan du kryptera data volymen utan att först behöva kryptera operativ system volymen. 

När du har krypterat operativ system volymen stöds inte kryptering på operativ system volymen. För virtuella Linux-datorer i en skalnings uppsättning kan endast data volymen krypteras.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Kan jag kryptera en demonterad volym med Azure Disk Encryption?

Nej, Azure Disk Encryption krypterar bara monterade volymer.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hur gör jag för att rotera hemligheter eller krypterings nycklar?

Om du vill rotera hemligheter anropar du bara samma kommando som du använde ursprungligen för att aktivera disk kryptering, vilket anger en annan Key Vault. Om du vill rotera nyckel krypterings nyckeln anropar du samma kommando som du använde ursprungligen för att aktivera disk kryptering och anger den nya nyckel krypteringen. 

>[!WARNING]
> - Om du tidigare har använt [Azure Disk Encryption med Azure AD-appen](disk-encryption-linux-aad.md) genom att ange autentiseringsuppgifter för Azure AD för att kryptera den här virtuella datorn måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Du kan inte använda Azure Disk Encryption på den här krypterade virtuella datorn eftersom detta inte stöds, vilket innebär att det inte finns stöd för att växla bort från AAD-program för den här krypterade virtuella datorn än.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Hur gör jag för att lägga till eller ta bort en nyckel krypterings nyckel om jag inte ursprungligen använde den?

Om du vill lägga till en nyckel krypterings nyckel anropar du kommandot Aktivera igen och skickar nyckel krypterings nyckel parametern. Om du vill ta bort en nyckel krypterings nyckel anropar du kommandot Aktivera igen utan parametern nyckel krypterings nyckel.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure Disk Encryption tillåter att du kan använda en egen nyckel (BYOK)?

Ja, kan du ange en egen nyckel krypteringsnycklar. Dessa nycklar skyddas i Azure Key Vault, vilket är nyckelarkivet för Azure Disk Encryption. Mer information om nyckel krypterings nycklarna stöder scenarier finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Kan jag använda en Azure-skapade nyckelkrypteringsnyckel?

Ja, du kan använda Azure Key Vault för att generera en nyckel krypteringsnyckel för Azure disk encryption användning. Dessa nycklar skyddas i Azure Key Vault, vilket är nyckelarkivet för Azure Disk Encryption. Mer information om nyckel krypterings nyckeln finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Kan jag använda en lokal nyckelhanteringstjänst eller HSM för att skydda krypteringsnycklar?

Du kan inte använda lokal nyckelhanteringstjänst eller HSM för att skydda krypteringsnycklar med Azure Disk Encryption. Du kan bara använda Azure Key Vault-tjänsten för att skydda krypteringsnycklar. Mer information om stöd scenarier för nyckel krypterings nyckel finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Vilka är kraven för att konfigurera Azure Disk Encryption?

Det finns förutsättningar för Azure Disk Encryption. Se Skapa [och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md) artikel om du vill skapa ett nytt nyckel valv, eller konfigurera ett befintligt nyckel valv för disk krypterings åtkomst för att aktivera kryptering och skydda hemligheter och nycklar. Mer information om stöd scenarier för nyckel krypterings nyckel finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Vilka är kraven för att konfigurera Azure Disk Encryption med Azure AD-app (tidigare version)?

Det finns förutsättningar för Azure Disk Encryption. Se [Azure Disk Encryption med Azure AD](disk-encryption-linux-aad.md) -innehåll för att skapa ett Azure Active Directory program, skapa ett nytt nyckel valv eller konfigurera ett befintligt nyckel valv för disk krypterings åtkomst för att aktivera kryptering och skydda hemligheter och nycklar. Mer information om stöd scenarier för nyckel krypterings nyckel finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure Disk Encryption använder en Azure AD-app (tidigare version) som fortfarande stöds?
Ja. Diskkryptering som använder en Azure AD-app stöds fortfarande. Men när du krypterar nya virtuella datorer rekommenderas att du använder den nya metoden i stället för att kryptera med en Azure AD-app. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Kan jag migrera virtuella datorer som krypterats med en Azure AD-app till kryptering utan en Azure AD-app?
  Det finns inte för närvarande en direkt migreringsvägen för datorer som krypterats med en Azure AD-app till kryptering utan en Azure AD-app. Det finns inte dessutom en direkt sökväg från kryptering utan en Azure AD-app till kryptering med en AD-app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Vilken version av Azure PowerShell har stöd för Azure Disk Encryption?

Använd den senaste versionen av Azure PowerShell SDK för att konfigurera Azure Disk Encryption. Ladda ned den senaste versionen av [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption är *inte* stöds av Azure SDK version 1.1.0.

> [!NOTE]
> Linux Azure Disk Encryption Preview-tillägget "Microsoft. OSTCExtension. AzureDiskEncryptionForLinux" är föråldrat. Tillägget publicerades för Azure Disk Encryption Preview-versionen. Du bör inte använda för hands versionen av tillägget i test-eller produktions distributionen.

> För distributions scenarier som Azure Resource Manager (ARM), där du behöver Distribuera Azure Disk Encryption-tillägg för virtuella Linux-datorer för att aktivera kryptering på din Linux IaaS-VM, måste du använda tillägget Azure Disk Encryption Production som stöds " Microsoft. Azure. Security. AzureDiskEncryptionForLinux ".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Kan jag använda Azure Disk Encryption på min anpassade Linux-avbildning?

Du kan inte använda Azure Disk Encryption på din anpassade Linux-avbildning. Stöds endast i galleriet Linux-avbildningar för distributioner som stöds som beskrivs tidigare. Anpassade Linux-avbildningar stöds inte för närvarande.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Kan jag använda uppdateringar till virtuella Linux Red Hat-datorer som använder yum-uppdateringen?

Ja, du kan utföra en yum uppdatering på en Red Hat Linux-dator.  Mer information finns i [hantering av Linux-paket bakom en brand vägg](disk-encryption-troubleshooting.md#linux-package-management-behind-a-firewall).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Vad är rekommenderade Azure disk encryption-arbetsflöde för Linux?

Följande arbetsflöde bör vara de bästa resultat på Linux:
* Starta från oförändrad lagerartiklar galleriet bilden som motsvarar de nödvändiga OS-distribution och version
* Säkerhetskopiera alla monterade enheter som kommer att krypteras.  Det möjliggör tillbaka upp återställning om det uppstår ett fel, till exempel om den virtuella datorn startas om innan kryptering har slutförts.
* Kryptera (kan ta flera timmar eller t.o.m. dagar beroende på VM-egenskaper och storleken på eventuella anslutna datadiskar)
* Anpassa och lägga till programvara i avbildningen efter behov.

Om det här arbetsflödet inte är möjligt, förlitar sig på [Lagringstjänstkryptering](../../storage/common/storage-service-encryption.md) (SSE) på plattformen storage konto lager kan vara ett alternativ till fullständig diskkryptering med dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Vad är den disken ”Bek volym” eller ”/ mnt/azure_bek_disk”?

"Bek Volume" är en lokal data volym som lagrar krypterings nycklarna på ett säkert sätt för krypterade virtuella Azure-datorer.
> [!NOTE]
> Ta bort eller inte redigera något innehåll i den här disken. Demontera inte disken eftersom viktiga förekomsten kryptering krävs för krypteringsåtgärder på IaaS-VM.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Vilken krypteringsmetod som använder Azure Disk Encryption?

Azure Disk Encryption använder dekrypteringen standardvärdet AES-XTS-plain64 med en huvud nyckel på 256-bitars volym.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Om jag använder EncryptFormatAll och anger alla volymtyper av, kommer den att radera data på dataenheter som vi redan har krypterat?
Nej, inte data raderas från enheter som redan är krypterade med Azure Disk Encryption. Liknande hur EncryptFormatAll inte kryptera operativsystemenheten, den kommer inte att kryptera enheten redan krypterade data. Mer information finns i den [EncryptFormatAll kriterier](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>Stöds XFS-filsystem?
XFS-volymer stöds endast för datadisk-kryptering med EncryptFormatAll. Då formateras volymen och eventuella data raderas tidigare. Mer information finns i den [EncryptFormatAll kriterier](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Kan jag säkerhetskopiera och återställa en krypterad virtuell dator? 

Azure Backup tillhandahåller en mekanism för att säkerhetskopiera och återställa krypterade virtuella datorer i samma prenumeration och region.  Anvisningar finns i [säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).  Det finns för närvarande inte stöd för att återställa en krypterad virtuell dator till en annan region.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Var kan jag få ställa frågor eller lämna feedback?

Du kan ställa frågor eller lämna feedback om den [forum för Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig mer om de vanligaste frågor som rör Azure Disk Encryption. Mer information om den här tjänsten finns i följande artiklar:

- [Azure Disk Encryption-översikt](disk-encryption-overview.md)
- [Tillämpa diskkryptering i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure datakryptering i vila](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
