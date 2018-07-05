---
title: Azure Storage Service Encryption för Data i vila | Microsoft Docs
description: Funktionen Azure Storage Service Encryption för att kryptera Azure Blob-lagring på serversidan vid lagring av data och dekryptera det vid hämtning av data.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: lakasa
ms.openlocfilehash: d469dfb5092f1269a6600ee8ee2f81778fd83b96
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450327"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure Storage-tjänstens kryptering av vilande data

Azure Storage Service Encryption för vilande Data kan du skydda dina data för att uppfylla organisationens säkerhets- och efterlevnadsbestämmelser. Med den här funktionen krypterar Azure Storage automatiskt dina data före beständig den Azure-lagring och dekrypterar data innan hämtning. Hantering av kryptering, kryptering på rest, dekryptering och nyckelhantering på kryptering av lagringstjänst är transparent för användarna. Alla data som skrivs till Azure Storage krypteras med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste blockchiffer som finns.

Kryptering av lagringstjänst är aktiverat för alla nya och befintliga lagringskonton och kan inte inaktiveras. Eftersom dina data är skyddade som standard, behöver du inte ändra din kod eller ett program för att dra nytta av kryptering av lagringstjänst.

Funktionen krypterar automatiskt data på:

- Båda prestandanivåer (Standard och Premium).
- Båda distributionsmodellerna (Azure Resource Manager och klassisk).
- Alla Azure Storage-tjänster (Blob storage, Queue storage, Table storage och Azure Files). 

Kryptering av lagringstjänst påverkar inte prestanda för Azure Storage.

Du kan använda Microsoft-hanterade krypteringsnycklarna med Storage Service Encryption eller du kan använda dina egna krypteringsnycklar. Mer information om hur du använder dina egna nycklar finns i [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Visa krypteringsinställningar i Azure portal

Om du vill visa inställningarna för kryptering av lagringstjänst, logga in på den [Azure-portalen](https://portal.azure.com) och välj ett lagringskonto. I den **inställningar** väljer den **kryptering** inställningen.

![Portalen skärmbild som visar inställningarna för lösenordskryptering](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Vanliga frågor och svar för kryptering av lagringstjänst

**F: Jag har ett klassiskt lagringskonto. Kan jag aktivera kryptering av lagringstjänst på den?**

S: kryptering av lagringstjänst är aktiverat för alla lagringskonton (klassiska och Resource Manager).

**F: hur kan jag för att kryptera data i mitt klassiska lagringskonto?**

S: med kryptering aktiverat som standard, krypterar Azure Storage automatiskt dina nya data. 

**F: Jag har ett Resource Manager-lagringskonto. Kan jag aktivera kryptering av lagringstjänst på den?**

S: kryptering av lagringstjänst är aktiverat som standard på alla befintliga Resource Manager-lagringskonton. Detta stöds för Blob storage, Table storage, Queue storage och Azure Files. 

**F: hur jag för att kryptera data i en Resource Manager-konto?**

S: kryptering av lagringstjänst är aktiverat för alla lagringskonton – klassiska och Resource Manager, alla befintliga filer i lagringskontot som skapades före kryptering har aktiverats kommer retroaktivt hämta krypterats av en bakgrundskrypteringsprocess.

**F: kan jag skapa storage-konton med Lagringstjänstens med hjälp av Azure PowerShell och Azure CLI?**

S: kryptering av lagringstjänst är aktiverat som standard vid tidpunkten för att skapa alla lagringskonton (klassiska eller Resource Manager). Du kan kontrollera kontoegenskaperna med hjälp av både Azure PowerShell och Azure CLI.

**F: hur mycket kostar Azure Storage om kryptering av lagringstjänst är aktiverat?**

S: det finns ingen extra kostnad.

**F: kan jag använda min egen krypteringsnycklar?**

S: Ja, kan du använda dina egna krypteringsnycklar. Mer information finns i [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**F: kan jag återkalla åtkomst till krypteringsnycklarna?**

S: Ja, om du [använda era egna krypteringsnycklar](storage-service-encryption-customer-managed-keys.md) i Azure Key Vault.

**F: är kryptering av lagringstjänst aktiverad som standard när jag skapar ett lagringskonto?**

S: Ja, kryptering av lagringstjänst är aktiverat för alla lagringskonton och för alla Azure Storage-tjänster.

**F: hur skiljer sig detta från Azure Disk Encryption?**

S: azure Disk Encryption används för att kryptera OS och datadiskar i virtuella IaaS-datorer. Mer information finns i den [lagringssäkerhetsguide](../storage-security-guide.md).

**F: Vad händer om jag inte aktivera Azure Disk Encryption på min datadiskar?**

S: Detta fungerar smidigt. Båda metoderna krypteras dina data.

**F: mitt lagringskonto har ställts in på att replikeras geo-redundant. Med kryptering av lagringstjänst kommer min redundant kopia även krypteras?**

S: Ja, krypteras alla kopior av storage-konto. Alla redundans stöds – lokalt redundant lagring, zonredundant lagring, geo-redundant lagring och läsåtkomst till geografiskt redundant lagring.

**F: kan jag inaktivera kryptering på mitt lagringskonto?**

S: kryptering är aktiverat som standard och det finns inga etablera att inaktivera kryptering för ditt lagringskonto. 

**F: Storage Service Encryption tillåts endast i vissa regioner?**

S: storage Service Encryption är tillgänglig i alla regioner för alla tjänster.

**F: är kryptering av lagringstjänst FIPS 140-2-kompatibel?**

S: Ja, kryptering av lagringstjänst är FIPS 140-2-kompatibel.

**F: hur kontaktar jag någon om jag har problem eller vill ge feedback?**

S: Kontakta [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) för eventuella problem eller feedback som rör kryptering av lagringstjänst.

## <a name="next-steps"></a>Nästa steg
Azure Storage tillhandahåller en omfattande uppsättning funktioner för säkerhet som tillsammans bidrar utvecklare skapa säkra program. Mer information finns i den [lagringssäkerhetsguide](../storage-security-guide.md).
