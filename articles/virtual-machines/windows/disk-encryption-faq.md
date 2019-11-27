---
title: Vanliga frågor och svar – Azure Disk Encryption för virtuella Windows-datorer
description: Den här artikeln innehåller svar på vanliga frågor om Microsoft Azure disk kryptering för virtuella Windows IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: ea2a66a6b012664a9596a02ea32c1a0b677ee3ea
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384272"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Vanliga frågor och svar om Azure Disk Encryption för Windows-VM

Den här artikeln innehåller svar på vanliga frågor och svar om Azure Disk Encryption för virtuella Windows-datorer. Mer information om den här tjänsten finns i [Azure Disk Encryption översikt](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Var finns Azure Disk Encryption i allmän tillgänglighet (GA)?

Azure Disk Encryption är allmänt tillgänglig i alla offentliga Azure-regioner.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Vilken användare som inträffar är tillgängliga med Azure Disk Encryption?

Azure Disk Encryption GA stöder Azure Resource Manager-mallar, Azure PowerShell och Azure CLI. Erfarenheterna från olika användare ger dig flexibilitet. Du har tre olika alternativ för att aktivera disk kryptering för dina virtuella datorer. Mer information om användar upplevelsen och stegvisa vägledningar som är tillgängliga i Azure Disk Encryption finns i [Azure Disk Encryption scenarier för Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Hur mycket kostar Azure Disk Encryption?

Det kostar inget att kryptera virtuella dator diskar med Azure Disk Encryption, men det finns kostnader som är kopplade till användningen av Azure Key Vault. Mer information om Azure Key Vault kostnader finns på sidan för [Key Vault priser](https://azure.microsoft.com/pricing/details/key-vault/) .

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hur kan jag börja använda Azure Disk Encryption?

Läs [Azure Disk Encryption översikt](disk-encryption-overview.md)för att komma igång.

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Vilka VM-storlekar och operativ system stöder Azure Disk Encryption?

I artikeln [Azure Disk Encryption översikt](disk-encryption-overview.md) visas de [VM-storlekar](disk-encryption-overview.md#supported-vm-sizes) och [virtuella dator operativ system](disk-encryption-overview.md#supported-operating-systems) som stöder Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kan jag kryptera både start- och datavolymer med Azure Disk Encryption?

Du kan kryptera både start-och data volymer, men du kan inte kryptera data utan att först kryptera operativ system volymen.

När du har krypterat operativ system volymen stöds inte kryptering på operativ system volymen.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Kan jag kryptera en demonterad volym med Azure Disk Encryption?

Nej, Azure Disk Encryption krypterar bara monterade volymer.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hur gör jag för att rotera hemligheter eller krypterings nycklar?

Om du vill rotera hemligheter anropar du bara samma kommando som du använde ursprungligen för att aktivera disk kryptering, vilket anger en annan Key Vault. Om du vill rotera nyckel krypterings nyckeln anropar du samma kommando som du använde ursprungligen för att aktivera disk kryptering och anger den nya nyckel krypteringen. 

>[!WARNING]
> - Om du tidigare har använt [Azure Disk Encryption med Azure AD-appen](disk-encryption-windows-aad.md) genom att ange autentiseringsuppgifter för Azure AD för att kryptera den här virtuella datorn måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Du kan inte använda Azure Disk Encryption på den här krypterade virtuella datorn eftersom detta inte stöds, vilket innebär att det inte finns stöd för att växla bort från AAD-program för den här krypterade virtuella datorn än.

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

Det finns förutsättningar för Azure Disk Encryption. Se [Azure Disk Encryption med Azure AD](disk-encryption-windows-aad.md) -innehåll för att skapa ett Azure Active Directory program, skapa ett nytt nyckel valv eller konfigurera ett befintligt nyckel valv för disk krypterings åtkomst för att aktivera kryptering och skydda hemligheter och nycklar. Mer information om stöd scenarier för nyckel krypterings nyckel finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure Disk Encryption använder en Azure AD-app (tidigare version) som fortfarande stöds?
Ja. Diskkryptering som använder en Azure AD-app stöds fortfarande. Men när du krypterar nya virtuella datorer rekommenderas att du använder den nya metoden i stället för att kryptera med en Azure AD-app. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Kan jag migrera virtuella datorer som krypterats med en Azure AD-app till kryptering utan en Azure AD-app?
  Det finns inte för närvarande en direkt migreringsvägen för datorer som krypterats med en Azure AD-app till kryptering utan en Azure AD-app. Det finns inte dessutom en direkt sökväg från kryptering utan en Azure AD-app till kryptering med en AD-app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Vilken version av Azure PowerShell har stöd för Azure Disk Encryption?

Använd den senaste versionen av Azure PowerShell SDK för att konfigurera Azure Disk Encryption. Ladda ned den senaste versionen av [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption stöds *inte* av Azure SDK-versionen 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Vad är den disken ”Bek volym” eller ”/ mnt/azure_bek_disk”?

"Bek Volume" är en lokal data volym som lagrar krypterings nycklarna på ett säkert sätt för krypterade virtuella Azure-datorer.

> [!NOTE]
> Ta bort eller inte redigera något innehåll i den här disken. Demontera inte disken eftersom viktiga förekomsten kryptering krävs för krypteringsåtgärder på IaaS-VM.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Vilken krypteringsmetod som använder Azure Disk Encryption?

Azure Disk Encryption väljer krypterings metoden i BitLocker baserat på Windows-versionen enligt följande:

| Windows-versioner                 | Version | Krypterings metod        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 eller senare  | > = 1511 |XTS-AES 256-bit           |
| Windows Server 2012, Windows 8, 8,1, 10 | < 1511 |AES 256-bit *              |
| Windows Server-2008R2            |        |AES 256-bit med diffuser |

\* AES 256-bitar med diffuser stöds inte i Windows 2012 och senare.

Du fastställer Windows OS-versionen genom att köra verktyget winver på den virtuella datorn.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Om jag använder EncryptFormatAll och anger alla volymtyper av, kommer den att radera data på dataenheter som vi redan har krypterat?
Nej, inte data raderas från enheter som redan är krypterade med Azure Disk Encryption. Liknande hur EncryptFormatAll inte kryptera operativsystemenheten, den kommer inte att kryptera enheten redan krypterade data. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Kan jag säkerhetskopiera och återställa en krypterad virtuell dator? 

Azure Backup tillhandahåller en mekanism för att säkerhetskopiera och återställa krypterade virtuella datorer i samma prenumeration och region.  Anvisningar finns i [säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup](../../backup/backup-azure-vms-encryption.md).  Det finns för närvarande inte stöd för att återställa en krypterad virtuell dator till en annan region.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Var kan jag få ställa frågor eller lämna feedback?

Du kan ställa frågor eller ge feedback i [Azure Disk Encryption-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig mer om de vanligaste frågor som rör Azure Disk Encryption. Mer information om den här tjänsten finns i följande artiklar:

- [Översikt av Azure Disk Encryption](disk-encryption-overview.md)
- [Använd disk kryptering i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure Data Encryption i vila](../../security/fundamentals/encryption-atrest.md)
