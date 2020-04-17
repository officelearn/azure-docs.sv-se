---
title: Vanliga frågor och svar – Azure Disk-kryptering för virtuella Linux-datorer
description: Den här artikeln innehåller svar på vanliga frågor om virtuella Linux-IaaS-datorer.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 753d606e1fc2dc966c970a210cf6fc5066d5ed83
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460145"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>Vanliga frågor och svar om Azure Disk Encryption för virtuella Linux-datorer

Den här artikeln innehåller svar på vanliga frågor och svar om Azure Disk Encryption för virtuella Linux-datorer (VMs). Mer information om den här tjänsten finns i [Översikt över Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>Vad är Azure Disk Encryption för virtuella Linux-datorer?

Azure Disk Encryption for Linux VIRTUELLA datorer använder dm-crypt-funktionen i Linux för att tillhandahålla fullständig diskkryptering av OS-disken* och datadiskar. Dessutom ger det kryptering av den tillfälliga resursdisken när du använder [funktionen EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms). Innehållet flödar krypterat från den virtuella datorn till lagrings-backend. Därmed ger slutpunkt till slutpunkt kryptering med en kundhanterad nyckel.
 
Se [Virtuella datorer och operativsystem som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Var är Azure Disk Encryption i allmän tillgänglighet (GA)?

Virtuella Azure Disk-kryptering för virtuella Linux-datorer är i allmän tillgänglighet i alla offentliga Azure-regioner.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Vilka användarupplevelser är tillgängliga med Azure Disk Encryption?

Azure Disk Encryption GA stöder Azure Resource Manager-mallar, Azure PowerShell och Azure CLI. De olika användarupplevelserna ger dig flexibilitet. Du har tre olika alternativ för att aktivera diskkryptering för dina virtuella datorer. Mer information om användarupplevelsen och steg-för-steg-vägledning som finns i Azure Disk Encryption finns i [Azure Disk Encryption-scenarier för Linux](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Hur mycket kostar Azure Disk Encryption?

Det finns ingen avgift för att kryptera VM-diskar med Azure Disk Encryption, men det finns avgifter som är associerade med användningen av Azure Key Vault. Mer information om Azure Key Vault-kostnader finns på [prissidan för Key Vault.](https://azure.microsoft.com/pricing/details/key-vault/)

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hur kan jag börja använda Azure Disk Encryption?

Information om hur du kommer igång läser du [översikten över Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Vilka vm-storlekar och operativsystem stöder Azure Disk Encryption?

I översiktsartikeln [för Azure Disk Encryption](disk-encryption-overview.md) visas de [vm-storlekar](disk-encryption-overview.md#supported-vms) och [vm-operativsystem](disk-encryption-overview.md#supported-operating-systems) som stöder Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kan jag kryptera både start- och datavolymer med Azure Disk Encryption?

Ja, du kan kryptera både start- och datavolymer, eller så kan du kryptera datavolymen utan att först behöva kryptera OS-volymen. 

När du har krypterat OS-volymen stöds inte inaktivering av kryptering på OS-volymen. För virtuella Linux-datorer i en skalningsuppsättning kan endast datavolymen krypteras.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Kan jag kryptera en omonterad volym med Azure Disk Encryption?

Nej, Azure Disk Encryption krypterar bara monterade volymer.

## <a name="what-is-storage-server-side-encryption"></a>Vad är kryptering på lagringsserversidan?

Kryptering på lagringsserversidan krypterar Azure-hanterade diskar i Azure Storage. Hanterade diskar krypteras som standard med serverkryptering med en plattformshanterad nyckel (från och med den 10 juni 2017). Du kan hantera kryptering av hanterade diskar med dina egna nycklar genom att ange en kundhanterad nyckel. Mer information finns i: [Kryptering på serversidan av Azure-hanterade diskar](disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Hur skiljer sig Azure Disk Encryption från kryptering på lagringsserversidan med kundhanterad nyckel och när ska jag använda varje lösning?

Azure Disk Encryption tillhandahåller end-to-end-kryptering för OS-disken, datadiskar och den tillfälliga resursdisken med en kundhanterad nyckel.
- Om dina krav omfattar kryptering av alla ovanstående och end-to-end-kryptering använder du Azure Disk Encryption. 
- Om dina krav omfattar kryptering endast av data i vila med kundhanterad nyckel använder du [kryptering på serversidan med kundhanterade nycklar](disk-encryption.md). Du kan inte kryptera en disk med både Azure Disk Encryption och Storage server-side kryptering med kundhanterade nycklar. 
- Om din Linux-distribution inte visas under [operativsystem som stöds för Azure Disk Encryption](disk-encryption-overview.md#supported-operating-systems) eller om du använder ett scenario som anropas i [scenarierna som inte stöds för Windows,](disk-encryption-linux.md#unsupported-scenarios)bör du överväga [serverkryptering med kundhanterade nycklar](disk-encryption.md).
- Om din organisations princip tillåter dig att kryptera innehåll i vila med en Azure-hanterad nyckel behövs ingen åtgärd - innehållet krypteras som standard. För hanterade diskar krypteras innehållet i lagringen som standard med kryptering på serversidan med plattformshanterad nyckel. Nyckeln hanteras av Azure Storage-tjänsten. 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hur roterar jag hemligheter eller krypteringsnycklar?

Om du vill rotera hemligheter anropar du bara samma kommando som du använde ursprungligen för att aktivera diskkryptering och anger ett annat Nyckelvalv. Om du vill rotera nyckelkrypteringsnyckeln anropar du samma kommando som du använde ursprungligen för att aktivera diskkryptering och anger den nya nyckelkrypteringen. 

>[!WARNING]
> - Om du tidigare har använt [Azure Disk Encryption med Azure AD-app](disk-encryption-linux-aad.md) genom att ange Azure AD-autentiseringsuppgifter för att kryptera den här virtuella datorn, måste du fortsätta använda det här alternativet för att kryptera din virtuella dator. Du kan inte använda Azure Disk Encryption på den här krypterade virtuella datorn eftersom det inte är ett scenario som stöds, vilket innebär att det inte stöds att byta från AAD-program för den här krypterade virtuella datorn ännu.

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

Det finns förutsättningar för Azure Disk Encryption. Se [Azure Disk Encryption med Azure AD-innehåll](disk-encryption-linux-aad.md) för att skapa ett Azure Active Directory-program, skapa ett nytt nyckelvalv eller konfigurera ett befintligt nyckelvalv för diskkrypteringsåtkomst för kryptering och skydda hemligheter och nycklar. Mer information om nyckelkrypteringsnyckelns supportscenarier finns i [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption med Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Stöds Azure Disk Encryption med en Azure AD-app (tidigare version) fortfarande?
Ja. Diskkryptering med hjälp av en Azure AD-app stöds fortfarande. När du krypterar nya virtuella datorer rekommenderar vi dock att du använder den nya metoden i stället för att kryptera med en Azure AD-app. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Kan jag migrera virtuella datorer som krypterats med en Azure AD-app till kryptering utan en Azure AD-app?
  För närvarande finns det inte en direkt migreringssökväg för datorer som krypterades med en Azure AD-app till kryptering utan en Azure AD-app. Dessutom finns det inte en direkt sökväg från kryptering utan en Azure AD-app till kryptering med en AD-app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Vilken version av Azure PowerShell stöder Azure Disk Encryption?

Använd den senaste versionen av Azure PowerShell SDK för att konfigurera Azure Disk Encryption. Hämta den senaste versionen av [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption stöds *inte* av Azure SDK version 1.1.0.

> [!NOTE]
> Förhandstillägget för Azure-diskkrypteringskryptering "Microsoft.OSTCExtension.AzureDiskEncryptionForLinux" är inaktuellt. Det här tillägget har publicerats för förhandsversionen av Azure-diskkryptering. Du bör inte använda förhandsversionen av tillägget i din testning eller produktionsdistribution.

> För distributionsscenarier som Azure Resource Manager (ARM), där du har ett behov av att distribuera Azure-diskkrypteringstillägg för Linux VM för att aktivera kryptering på din Virtuella Linux IaaS-dator, måste du använda tillägget för Azure-diskkrypteringsproduktion som stöds av tillägget "Microsoft.Azure.Security.AzureDiskEncryptionForLinux".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Kan jag använda Azure Disk Encryption på min anpassade Linux-avbildning?

Du kan inte använda Azure Disk Encryption på din anpassade Linux-avbildning. Endast galleriet Linux-avbildningar för de distributioner som stöds som tidigare har kallats ut stöds stöds. Anpassade Linux-avbildningar stöds för närvarande inte.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Kan jag installera uppdateringar på en virtuell linux-dator med Röda hatten som använder yum-uppdateringen?

Ja, du kan utföra en yum-uppdatering på en Red Hat Linux VM.  Mer information finns i [Azure Disk Encryption i ett isolerat nätverk](disk-encryption-isolated-network.md).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Vilket är det rekommenderade Azure-diskkrypteringsarbetsflödet för Linux?

Följande arbetsflöde rekommenderas för att få bästa resultat på Linux:
* Börja från den oförändrade stock galleri bild som motsvarar den nödvändiga OS distributioner och version
* Säkerhetskopiera alla monterade enheter som krypteras.  Den här säkerhetskopieringen möjliggör återställning om det finns ett fel, till exempel om den virtuella datorn startas om innan krypteringen har slutförts.
* Kryptera (kan ta flera timmar eller till och med dagar beroende på vm-egenskaper och storleken på alla anslutna datadiskar)
* Anpassa och lägg till programvara i bilden efter behov.

Om det här arbetsflödet inte är möjligt kan det vara ett alternativ till fullständig diskkryptering att förlita sig på SSE [(Storage Service Encryption)](../../storage/common/storage-service-encryption.md) på plattformslagringskontolagslagret.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Vad är skivan "Bek Volume" eller "/mnt/azure_bek_disk"?

"Bek-volymen" är en lokal datavolym som på ett säkert sätt lagrar krypteringsnycklarna för krypterade virtuella Azure-datorer.
> [!NOTE]
> Ta inte bort eller redigera något innehåll på den här disken. Avmontera inte disken eftersom krypteringsnyckelnärvaron behövs för alla krypteringsåtgärder på IaaS VM.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Vilken krypteringsmetod använder Azure Disk Encryption?

Azure Disk Encryption använder dekrypteringsstandarden för aes-xts-plain64 med en 256-bitars volymhuvudnyckel.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Om jag använder EncryptFormatAll och ange alla volymtyper, kommer det att radera data på de dataenheter som vi redan krypterat?
Nej, data raderas inte från dataenheter som redan är krypterade med Azure Disk Encryption. På samma sätt som EncryptFormatAll inte krypterade om OS-enheten, krypterar den inte om den redan krypterade dataenheten. Mer information finns i [villkoren EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>Stöds XFS-filsystem?
Kryptering av XFS OS-diskar stöds.

Kryptering av XFS-datadiskar stöds endast när parametern EncryptFormatAll används. Detta kommer att formatera om volymen, radera alla data tidigare där. Mer information finns i [villkoren EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Kan jag säkerhetskopiera och återställa en krypterad virtuell dator? 

Azure Backup tillhandahåller en mekanism för säkerhetskopiering och återställning av krypterade virtuella datorer inom samma prenumeration och region.  Instruktioner finns i [Säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).  Det går för närvarande inte att återställa en krypterad virtuell dator till en annan region.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Var kan jag gå för att ställa frågor eller ge feedback?

Du kan ställa frågor eller ge feedback på [Azure Disk Encryption-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig mer om de vanligaste frågorna om Azure Disk Encryption. Mer information om den här tjänsten finns i följande artiklar:

- [Översikt av Azure Disk Encryption](disk-encryption-overview.md)
- [Använda diskkryptering i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure-datakryptering i vila](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
