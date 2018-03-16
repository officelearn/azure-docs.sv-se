---
title: Azure Storage Service-kryptering av vilande Data | Microsoft Docs
description: "Funktionen Azure Storage Service-kryptering för att kryptera Azure Blob storage på tjänstsidan när data lagrades och dekryptera den vid hämtning av data."
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/14/2018
ms.author: lakasa
ms.openlocfilehash: d9df2218acc218a796e502fa4e3b94573af86ca8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure Storage-tjänstens kryptering av vilande data

Azure Storage Service-kryptering för Data i vila hjälper dig att skydda dina data för att uppfylla din organisations säkerhet och efterlevnad åtaganden. Med den här funktionen Azure Storage automatiskt krypterar dina data innan beständighet till Azure Storage och dekrypterar data innan hämtning. Hantering av kryptering och kryptering i vila, dekryptering och nyckelhantering i Storage Service-kryptering är transparent för användarna. Krypteras alla data som skrivs till Azure Storage via 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste blocket chiffer tillgängliga.

Lagringstjänstens kryptering är aktiverat för alla nya och befintliga lagringskonton och kan inte inaktiveras. Eftersom dina data skyddas som standard, behöver du inte ändra din kod eller ett program för att dra nytta av Storage Service-kryptering.

Funktionen krypteras automatiskt data i:

- Båda prestandanivåer (Standard och Premium).
- Båda distributionsmodellerna (Azure Resource Manager och klassisk).
- Alla Azure Storage-tjänster (Blob storage, Queue storage, Table storage och Azure-filer). 

Lagringstjänstens kryptering påverkar inte prestanda för Azure Storage.

Du kan använda Microsoft-hanterad krypteringsnycklarna med Storage Service-kryptering eller du kan använda dina egna krypteringsnycklar. Mer information om hur du använder egna nycklar finns [Lagringstjänstens kryptering med kundhanterad nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Visa krypteringsinställningar i Azure-portalen

Om du vill visa inställningarna för Storage Service-kryptering, logga in på den [Azure-portalen](https://portal.azure.com) och välj ett lagringskonto. I den **inställningar** väljer den **kryptering** inställningen.

![Portalen skärmbild som visar inställningen kryptering](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Vanliga frågor om Storage Service-kryptering

**F: Jag har en klassiska storage-konto. Kan jag aktivera Storage Service-kryptering på den?**

S: Storage Service-kryptering är aktiverat som standard för alla storage-konton (klassiska och Resource Manager).

**F: hur kan jag för att kryptera data i mitt klassiska storage-konto?**

A: Azure Storage krypterar automatiskt den nya informationen med kryptering aktiverat som standard. 

**F: Jag har en Resource Manager storage-konto. Kan jag aktivera Storage Service-kryptering på den?**

S: storage Service-kryptering är aktiverat som standard på alla befintliga Resource Manager-lagringskonton. Detta stöds för Blob storage, Table storage, Queue storage och Azure-filer. 

**F: hur kryptera data i en Resource Manager storage-konto?**

S: Storage Service-kryptering är aktiverat som standard för alla lagringskonton--klassiska och Resource Manager. Befintliga data är inte krypterad. För att kryptera befintliga data, kan du kopiera den till ett annat namn eller en annan behållare och tar bort okrypterad versioner. 

**F: kan jag skapa storage-konton med Lagringstjänstens kryptering aktiverat med hjälp av Azure PowerShell och Azure CLI?**

S: Storage Service-kryptering är aktiverat som standard vid tidpunkten för att skapa storage-konto (klassiska eller Resource Manager). Du kan kontrollera egenskaper genom att använda både Azure PowerShell och Azure CLI.

**F: hur mycket kostar Azure Storage om Storage Service-kryptering aktiveras?**

S: finns utan extra kostnad.

**F: kan jag använda min egen krypteringsnycklar?**

S: Ja, kan du använda dina egna krypteringsnycklar. Mer information finns i [Lagringstjänstens kryptering med kundhanterad nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**F: kan jag återkalla åtkomst till krypteringsnycklarna?**

S: Ja, om du [använder egna krypteringsnycklar](storage-service-encryption-customer-managed-keys.md) i Azure Key Vault.

**F: är Lagringstjänstens kryptering aktiverat som standard när jag skapar ett lagringskonto?**

S: Ja är Storage Service-kryptering (med Microsoft-hanterad nycklar) aktiverat som standard för alla lagringskonton--Azure Resource Manager och klassisk. Den har aktiverats för alla tjänster samt--Blob storage, Table storage, Queue storage och Azure-filer.

**F: hur skiljer detta sig från Azure Disk Encryption?**

S: azure Disk Encryption används för att kryptera Operativsystemet och datadiskarna i IaaS-VM. Mer information finns i [säkerhetsguiden för lagring](../storage-security-guide.md).

**F: Vad händer om jag inte aktivera Azure Disk Encryption på datadiskar Mina?**

S: Detta fungerar sömlöst. Båda metoderna ska kryptera dina data.

**F: mitt lagringskonto har konfigurerats som ska replikeras geo-redundant. Med Storage Service-kryptering, min redundant kopia även krypteras?**

S: Ja, krypteras alla kopior av lagringskontot. Alla redundans alternativ stöds--lokalt redundant lagring, zonredundant lagring, geo-redundant lagring och geo-redundant lagring med läsbehörighet.

**F: kan jag inaktivera kryptering för storage-konto?**

S: kryptering är aktiverat som standard och skall inte att inaktivera kryptering för ditt lagringskonto. 

**F: Lagringstjänstens kryptering tillåts endast i vissa regioner?**

S: storage Service-kryptering är tillgänglig i alla regioner för alla tjänster. 

**F: hur kan jag få någon om jag har problem eller vill ge feedback?**

A: Kontakta [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) för eventuella problem eller feedback som är relaterade till Storage Service-kryptering.

## <a name="next-steps"></a>Nästa steg
Azure Storage tillhandahåller en omfattande uppsättning säkerhetsfunktioner som tillsammans hjälper utvecklare build säkra program. Mer information finns i [säkerhetsguiden för lagring](../storage-security-guide.md).
