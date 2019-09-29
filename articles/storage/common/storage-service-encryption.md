---
title: Azure Storage kryptering för vilande data | Microsoft Docs
description: Azure Storage skyddar dina data genom att automatiskt kryptera dem innan du sparar dem i molnet. Alla data i en Azure Storage krypteras och dekrypteras transparent med 256-bitars AES-kryptering och är FIPS 140-2-kompatibel.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2abaa994bf507c3ffb65199af9ac609ece138ea4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673051"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage kryptering för vilande data

Azure Storage krypterar dina data automatiskt när de behålls i molnet. Kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES- [kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering liknar BitLocker-kryptering på Windows.

Azure Storage kryptering har Aktiver ATS för alla nya och befintliga lagrings konton och kan inte inaktive ras. Eftersom dina data är säkra som standard behöver du inte ändra koden eller programmen för att dra nytta av Azure Storage kryptering. 

Lagrings konton är krypterade oavsett prestanda nivå (standard eller Premium) eller distributions modell (Azure Resource Manager eller klassisk). Alla Azure Storage alternativ för redundans stöder kryptering och alla kopior av ett lagrings konto krypteras. Alla Azure Storage-resurser krypteras, inklusive blobbar, diskar, filer, köer och tabeller. Alla metadata för objekt krypteras också.

Kryptering påverkar inte Azure Storage prestanda. Det kostar inget extra att Azure Storage kryptering.

Mer information om de kryptografiska modulerna underliggande Azure Storage kryptering finns i [Cryptography API: Nästa generation @ no__t-0.

## <a name="key-management"></a>Nyckelhantering

Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av ditt lagrings konto, eller så kan du hantera kryptering med dina egna nycklar, tillsammans med Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Microsoft-hanterade nycklar

Som standard använder ditt lagrings konto Microsoft-hanterade krypterings nycklar. Du kan se krypterings inställningarna för ditt lagrings konto i avsnittet **kryptering** i [Azure Portal](https://portal.azure.com), som du ser i följande bild.

![Visa kontot krypterat med Microsoft-hanterade nycklar](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Kundhanterade nycklar

Du kan hantera Azure Storage kryptering med Kundhanterade nycklar. Kundhanterade nycklar ger dig större flexibilitet när du skapar, roterar, inaktiverar och återkallar åtkomst kontroller. Du kan också granska de krypterings nycklar som används för att skydda dina data. 

Använd Azure Key Vault för att hantera dina nycklar och granska din nyckel användning. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Lagrings kontot och nyckel valvet måste finnas i samma region, men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Azure Key Vault?](../../key-vault/key-vault-overview.md).

Information om hur du återkallar åtkomst till Kundhanterade nycklar finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) och [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Att återkalla åtkomsten på ett effektivt sätt blockerar åtkomsten till alla data i lagrings kontot, eftersom krypterings nyckeln inte är tillgänglig via Azure Storage.

Information om hur du använder Kundhanterade nycklar med Azure Storage finns i någon av följande artiklar:

- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från Azure-portalen](storage-encryption-keys-portal.md)
- [Konfigurera kundhanterade nycklar för Azure Storage-kryptering från PowerShell](storage-encryption-keys-powershell.md)
- [Använda Kundhanterade nycklar med Azure Storage kryptering från Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du överför en prenumeration från en Azure AD-katalog till en annan, uppdateras inte hanterade identiteter och Kundhanterade nycklar kanske inte fungerar längre. Mer information finns i **överföra en prenumeration mellan Azure AD-kataloger** i [vanliga frågor och svar och kända problem med hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

> [!NOTE]  
> Kundhanterade nycklar stöds inte för [Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage kryptering jämfört med disk kryptering

Med Azure Storage kryptering krypteras alla Azure Storage-konton och de resurser som de innehåller, inklusive de sid-blobbar som backar upp Azure Virtual Machine-diskar. Dessutom kan diskar för virtuella Azure-datorer krypteras med [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption använder branschstandardiserade [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) på Windows och [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla operativ systembaserade krypterings lösningar som är integrerade med Azure Key Vault.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md)
