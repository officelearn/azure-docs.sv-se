---
title: Vanliga frågor och svar – Azure Diskkryptering för virtuella Windows-datorer
description: Den här artikeln innehåller svar på vanliga frågor om virtuella datorer för Microsoft Azure Disk-kryptering för Windows IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 5cc6a3d8d736209f762959bca6f8ad379c14203f
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582715"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Vanliga frågor och svar om Azure Disk Encryption för Virtuella Windows-datorer

Den här artikeln innehåller svar på vanliga frågor och svar om Azure Disk Encryption för virtuella windows-datorer. Mer information om den här tjänsten finns i [Översikt över Azure Disk Encryption](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Var är Azure Disk Encryption i allmän tillgänglighet (GA)?

Azure Disk Encryption är i allmän tillgänglighet i alla offentliga Azure-regioner.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Vilka användarupplevelser är tillgängliga med Azure Disk Encryption?

Azure Disk Encryption GA stöder Azure Resource Manager-mallar, Azure PowerShell och Azure CLI. De olika användarupplevelserna ger dig flexibilitet. Du har tre olika alternativ för att aktivera diskkryptering för dina virtuella datorer. Mer information om användarupplevelsen och steg-för-steg-vägledning som är tillgänglig i Azure Disk Encryption finns i [Azure Disk Encryption-scenarier för Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Hur mycket kostar Azure Disk Encryption?

Det finns ingen avgift för att kryptera VM-diskar med Azure Disk Encryption, men det finns avgifter som är associerade med användningen av Azure Key Vault. Mer information om Azure Key Vault-kostnader finns på [prissidan för Key Vault.](https://azure.microsoft.com/pricing/details/key-vault/)

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hur kan jag börja använda Azure Disk Encryption?

Information om hur du kommer igång läser du [översikten över Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Vilka vm-storlekar och operativsystem stöder Azure Disk Encryption?

I översiktsartikeln [för Azure Disk Encryption](disk-encryption-overview.md) visas de [vm-storlekar](disk-encryption-overview.md#supported-vms) och [vm-operativsystem](disk-encryption-overview.md#supported-operating-systems) som stöder Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kan jag kryptera både start- och datavolymer med Azure Disk Encryption?

Du kan kryptera både start- och datavolymer, men du kan inte kryptera data utan att först kryptera OS-volymen.

När du har krypterat OS-volymen stöds inte inaktivering av kryptering på OS-volymen.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Kan jag kryptera en omonterad volym med Azure Disk Encryption?

Nej, Azure Disk Encryption krypterar bara monterade volymer.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hur roterar jag hemligheter eller krypteringsnycklar?

Om du vill rotera hemligheter anropar du bara samma kommando som du använde ursprungligen för att aktivera diskkryptering och anger ett annat Nyckelvalv. Om du vill rotera nyckelkrypteringsnyckeln anropar du samma kommando som du använde ursprungligen för att aktivera diskkryptering och anger den nya nyckelkrypteringen. 

>[!WARNING]
> - Om du tidigare har använt [Azure Disk Encryption med Azure AD-app](disk-encryption-windows-aad.md) genom att ange Azure AD-autentiseringsuppgifter för att kryptera den här virtuella datorn, måste du fortsätta använda det här alternativet för att kryptera din virtuella dator. Du kan inte använda Azure Disk Encryption på den här krypterade virtuella datorn eftersom det inte är ett scenario som stöds, vilket innebär att det inte stöds att byta från AAD-program för den här krypterade virtuella datorn ännu.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Hur lägger jag till eller tar bort en nyckelkrypteringsnyckel om jag inte använde en?

Om du vill lägga till en nyckelkrypteringsnyckel anropar du kommandot Enable igen och skickar parametern för nyckelkrypteringsnyckeln. Om du vill ta bort en nyckelkrypteringsnyckel anropar du kommandot Enable igen utan parametern för nyckelkrypteringsnyckel.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Gör Azure Disk Encryption att du kan ta med din egen nyckel (BYOK)?

Ja, du kan ange dina egna nyckelkrypteringsnycklar. Dessa nycklar skyddas i Azure Key Vault, som är nyckelarkivet för Azure Disk Encryption. Mer information om stödscenarierna för nyckelkrypteringsnycklar finns i [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Kan jag använda en Azure-skapad nyckelkrypteringsnyckel?

Ja, du kan använda Azure Key Vault för att generera en nyckelkrypteringsnyckel för Azure-diskkrypteringsanvändning. Dessa nycklar skyddas i Azure Key Vault, som är nyckelarkivet för Azure Disk Encryption. Mer information om nyckelkrypteringsnyckeln finns i [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Kan jag använda en lokal nyckelhanteringstjänst eller HSM för att skydda krypteringsnycklarna?

Du kan inte använda den lokala nyckelhanteringstjänsten eller HSM för att skydda krypteringsnycklarna med Azure Disk Encryption. Du kan bara använda Azure Key Vault-tjänsten för att skydda krypteringsnycklarna. Mer information om nyckelkrypteringsnyckelns supportscenarier finns i [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Vilka är förutsättningarna för att konfigurera Azure Disk Encryption?

Det finns förutsättningar för Azure Disk Encryption. Se artikeln [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption för](disk-encryption-key-vault.md) att skapa ett nytt nyckelvalv eller konfigurera ett befintligt nyckelvalv för diskkrypteringsåtkomst för kryptering och skydda hemligheter och nycklar. Mer information om nyckelkrypteringsnyckelns supportscenarier finns i [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Vilka är förutsättningarna för att konfigurera Azure Disk Encryption med en Azure AD-app (tidigare version)?

Det finns förutsättningar för Azure Disk Encryption. Se [Azure Disk Encryption med Azure AD-innehåll](disk-encryption-windows-aad.md) för att skapa ett Azure Active Directory-program, skapa ett nytt nyckelvalv eller konfigurera ett befintligt nyckelvalv för diskkrypteringsåtkomst för kryptering och skydda hemligheter och nycklar. Mer information om nyckelkrypteringsnyckelns supportscenarier finns i [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption med Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Stöds Azure Disk Encryption med en Azure AD-app (tidigare version) fortfarande?
Ja. Diskkryptering med hjälp av en Azure AD-app stöds fortfarande. När du krypterar nya virtuella datorer rekommenderar vi dock att du använder den nya metoden i stället för att kryptera med en Azure AD-app. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Kan jag migrera virtuella datorer som krypterats med en Azure AD-app till kryptering utan en Azure AD-app?
  För närvarande finns det inte en direkt migreringssökväg för datorer som krypterades med en Azure AD-app till kryptering utan en Azure AD-app. Dessutom finns det inte en direkt sökväg från kryptering utan en Azure AD-app till kryptering med en AD-app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Vilken version av Azure PowerShell stöder Azure Disk Encryption?

Använd den senaste versionen av Azure PowerShell SDK för att konfigurera Azure Disk Encryption. Hämta den senaste versionen av [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption stöds *inte* av Azure SDK version 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Vad är skivan "Bek Volume" eller "/mnt/azure_bek_disk"?

"Bek-volymen" är en lokal datavolym som på ett säkert sätt lagrar krypteringsnycklarna för krypterade virtuella Azure-datorer.

> [!NOTE]
> Ta inte bort eller redigera något innehåll på den här disken. Avmontera inte disken eftersom krypteringsnyckelnärvaron behövs för alla krypteringsåtgärder på IaaS VM.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Vilken krypteringsmetod använder Azure Disk Encryption?

Azure Disk Encryption väljer krypteringsmetoden i BitLocker baserat på windowsversionen enligt följande:

| Windows-versioner                 | Version | Krypteringsmetod        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 eller senare  | >=1511 |XTS-AES 256 bitar           |
| Windows Server 2012, Windows 8, 8.1, 10 | < 1511 |AES 256 bitar *              |
| Windows Server 2008R2            |        |AES 256 bitars med diffusor |

\*AES 256-bitars med Diffusor stöds inte i Windows 2012 och senare.

Om du vill ta reda på Windows OS-versionen kör du verktyget "winver" i den virtuella datorn.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Om jag använder EncryptFormatAll och ange alla volymtyper, kommer det att radera data på de dataenheter som vi redan krypterat?
Nej, data raderas inte från dataenheter som redan är krypterade med Azure Disk Encryption. På samma sätt som EncryptFormatAll inte krypterade om OS-enheten, krypterar den inte om den redan krypterade dataenheten. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Kan jag säkerhetskopiera och återställa en krypterad virtuell dator? 

Azure Backup tillhandahåller en mekanism för säkerhetskopiering och återställning av krypterade virtuella datorer inom samma prenumeration och region.  Instruktioner finns i [Säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup](../../backup/backup-azure-vms-encryption.md).  Det går för närvarande inte att återställa en krypterad virtuell dator till en annan region.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Var kan jag gå för att ställa frågor eller ge feedback?

Du kan ställa frågor eller ge feedback på [Azure Disk Encryption-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig mer om de vanligaste frågorna om Azure Disk Encryption. Mer information om den här tjänsten finns i följande artiklar:

- [Översikt av Azure Disk Encryption](disk-encryption-overview.md)
- [Använda diskkryptering i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure-datakryptering i vila](../../security/fundamentals/encryption-atrest.md)
