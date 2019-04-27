---
title: Azure Storage-kryptering av vilande data | Microsoft Docs
description: Azure Storage skyddar dina data genom att automatiskt kryptera det före beständig lagring till molnet. Alla data i Azure Storage i blobbar, diskar, filer, köer och tabeller, krypteras och dekrypteras transparent med 256-bitars AES-kryptering och är FIPS 140-2-kompatibel.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 211cfeb3aba29245e154f4a7db86fb4a3659c36f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730844"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-kryptering av vilande data

Azure Storage krypterar dina data automatiskt när spara till molnet. Kryptering skyddar dina data och hjälper dig att uppfyller organisationens säkerhet och efterlevnad. Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), ett av de starkaste blockchiffer som finns och är FIPS 140-2-kompatibel. Azure Storage kryptering liknar BitLocker-kryptering på Windows.

Azure Storage-kryptering är aktiverat för alla nya och befintliga lagringskonton och kan inte inaktiveras. Eftersom dina data är skyddade som standard, behöver du inte ändra din kod eller att dra nytta av Azure Storage kryptering. Storage-konton är krypterade oavsett prestandanivå (standard eller premium) eller distributionsmodellen (Azure Resource Manager eller klassisk). Redundansalternativ för alla Azure Storage stöder kryptering, och alla kopior av ett lagringskonto är krypterade. Alla resurser i Azure Storage krypteras, inklusive blobar, diskar, filer, köer och tabeller.

Azure Storage-prestanda påverkas inte av kryptering. Det finns ingen extra kostnad för Azure Storage kryptering.

Mer information om kryptografiska moduler som underliggande encryption för Azure Storage finns i [Cryptography API: Nästa generations](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="key-management"></a>Nyckelhantering

Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av ditt storage-konto eller du kan hantera kryptering med dina egna nycklar, tillsammans med Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Microsoft-hanterade nycklar

Som standard använder Microsoft-hanterade krypteringsnycklarna i ditt storage-konto. Du kan se krypteringsinställningarna för ditt lagringskonto i den **kryptering** delen av den [Azure-portalen](https://portal.azure.com), enligt följande bild.

![Visa konto som är krypterade med Microsoft-hanterade nycklar](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Kundhanterade nycklar

Du kan hantera Azure Storage kryptering med Kundhanterade nycklar. Kundhanterade nycklar ger mer flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomstkontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data. 

Använda Azure Key Vault för att hantera dina nycklar och granska din nyckelanvändning. Du kan skapa dina egna nycklar och lagra dem i ett nyckelvalv, eller du kan använda Azure Key Vault-API: er för att generera nycklar. Storage-konto och nyckelvalvet måste vara i samma region, men de kan vara i olika prenumerationer. Läs mer om Azure Key Vault, [vad är Azure Key Vault?](../../key-vault/key-vault-overview.md).

Om du vill återkalla åtkomst till Kundhanterade nycklar finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) och [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Återkalla åtkomst effektivt blockerar åtkomsten till alla data i storage-kontot eftersom krypteringsnyckeln är otillgänglig av Azure Storage.

Läs hur du använder Kundhanterade nycklar med Azure Storage i någon av följande artiklar:

- [Konfigurera Kundhanterade nycklar för kryptering av Azure Storage från Azure portal](storage-encryption-keys-portal.md)
- [Konfigurera Kundhanterade nycklar för kryptering av Azure Storage från PowerShell](storage-encryption-keys-powershell.md)
- [Använda Kundhanterade nycklar med Azure Storage kryptering från Azure CLI](storage-encryption-keys-cli.md)

> [!NOTE]  
> Kundhanterade nycklar stöds inte för [Azure hanterade diskar](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage kryptering jämfört med diskkryptering

Med Azure Storage kryptering alla Azure Storage-konton och resurser som de innehåller krypteras, inklusive sidblobar som stöder Azure-datordiskar. Dessutom Azure-datordiskar kan krypteras med [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption används vanliga [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) på Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) på Linux för att tillhandahålla systembaserad krypteringslösningar som är integrerade med Azure Key Vault.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md)
