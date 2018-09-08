---
title: Azure Storage Service Encryption för data i vila | Microsoft Docs
description: Funktionen Azure Storage Service Encryption för att kryptera Azure Managed Disks, Azure Blob storage, Azure Files, Azure Queue storage och Azure Table storage på serversidan vid lagring av data och dekryptera det vid hämtning av data.
services: storage
author: lakasa
ms.service: storage
ms.topic: article
ms.date: 08/01/2018
ms.author: lakasa
ms.component: common
ms.openlocfilehash: c6001b0c95186dff53f33c73833b4cea30688db2
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094900"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure Storage Service Encryption för vilande data
Azure Storage Service Encryption för vilande data kan du skydda dina data för att uppfylla organisationens säkerhets- och efterlevnadsbestämmelser. Med den här funktionen krypterar Azure storage-plattformen automatiskt dina data före beständig Azure Managed Disks, Azure Blob, kö eller Table storage eller Azure Files och dekrypterar data innan hämtning. Hantering av kryptering, kryptering på rest, dekryptering och nyckelhantering på kryptering av lagringstjänst är transparent för användarna. Alla data som skrivs till Azure storage-plattformen krypteras med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste blockchiffer som finns.

Kryptering av lagringstjänst är aktiverat för alla nya och befintliga lagringskonton och kan inte inaktiveras. Eftersom dina data är skyddade som standard, behöver du inte ändra din kod eller ett program för att dra nytta av kryptering av lagringstjänst.

Funktionen krypterar automatiskt data på:

- Azure storage-tjänster:
    - Azure Managed Disks
    - Azure Blob Storage
    - Azure Files
    - Azure-kölagring
    - Azure Table storage.  
- Båda prestandanivåer (Standard och Premium).
- Båda distributionsmodellerna (Azure Resource Manager och klassisk).

Kryptering av lagringstjänst påverkar inte prestanda för Azure storage-tjänster.

Du kan använda Microsoft-hanterade krypteringsnycklarna med Storage Service Encryption eller du kan använda dina egna krypteringsnycklar. Mer information om hur du använder dina egna nycklar finns i [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Visa krypteringsinställningar i Azure portal
Om du vill visa inställningarna för kryptering av lagringstjänst, logga in på den [Azure-portalen](https://portal.azure.com) och välj ett lagringskonto. I den **inställningar** väljer den **kryptering** inställningen.

![Portalen skärmbild som visar inställningarna för lösenordskryptering](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Vanliga frågor och svar för kryptering av lagringstjänst
**Hur jag för att kryptera data i en Resource Manager-konto?**  
Kryptering av lagringstjänst är aktiverat för alla lagringskonton – klassiska och Resource Manager, alla befintliga filer i lagringskontot som skapades före kryptering har aktiverats kommer retroaktivt hämta krypterats av en bakgrundskrypteringsprocess.

**Aktiveras kryptering av lagringstjänst som standard när jag skapar ett lagringskonto?**  
Ja, kryptering av lagringstjänst är aktiverat för alla lagringskonton och för alla Azure storage-tjänster.

**Jag har ett Resource Manager-lagringskonto. Kan jag aktivera kryptering av lagringstjänst på den?**  
Kryptering av lagringstjänst är aktiverat som standard på alla befintliga Resource Manager-lagringskonton. Detta stöds för Azure Blob storage, Azure Files, Azure Queue storage, Table storage. 

**Kan jag inaktivera kryptering på mitt lagringskonto?**  
Kryptering är aktiverat som standard och det finns inga etablera att inaktivera kryptering för ditt lagringskonto. 

**Hur mycket kostar Azure Storage om kryptering av lagringstjänst är aktiverat?**  
Det finns ingen extra kostnad.

**Kan jag använda min egen krypteringsnycklar?**  
För Azure Blob storage och Azure Files, Ja, kan du använda dina egna krypteringsnycklar. Kundhanterade nycklar stöds inte för närvarande av Azure Managed Disks. Mer information finns i [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**Kan jag återkalla åtkomst till krypteringsnycklarna?**  
Ja, om du [använda era egna krypteringsnycklar](storage-service-encryption-customer-managed-keys.md) i Azure Key Vault.

**Hur skiljer sig kryptering av lagringstjänst från Azure Disk Encryption?**  
Azure Disk Encryption innehåller integrering mellan OS-baserade lösningar, till exempel BitLocker och DM-Crypt och Azure KeyVault. Kryptering av lagringstjänst tillhandahåller kryptering internt i Azure storage-plattformen lager, under den virtuella datorn.

**Jag har ett klassiskt lagringskonto. Kan jag aktivera kryptering av lagringstjänst på den?**  
Kryptering av lagringstjänst är aktiverat för alla lagringskonton (klassiska och Resource Manager).

**Hur kan jag för att kryptera data i mitt klassiska lagringskonto?**  
Med kryptering aktiverat som standard, krypteras automatiskt data som lagras i Azure storage-tjänster. 

**Kan jag skapa storage-konton med Lagringstjänstens med hjälp av Azure PowerShell och Azure CLI?**  
Kryptering av lagringstjänst är aktiverat som standard vid tidpunkten för att skapa alla lagringskonton (klassiska eller Resource Manager). Du kan kontrollera kontoegenskaperna med hjälp av både Azure PowerShell och Azure CLI.

**Mitt lagringskonto har ställts in på att replikeras geo-redundant. Med kryptering av lagringstjänst kommer min redundant kopia även krypteras?**  
Ja, krypteras alla kopior av storage-konto. Alla redundans stöds – lokalt redundant lagring, zonredundant lagring, geo-redundant lagring och läsåtkomst till geografiskt redundant lagring.

**Kryptering av lagringstjänst är tillåtet i specifika regioner?**  
Kryptering av lagringstjänst är tillgänglig i alla regioner.

**Är Storage Service Encryption FIPS 140-2 kompatibelt?**  
Ja, kryptering av lagringstjänst är FIPS 140-2-kompatibel.

**Hur kontaktar jag någon om jag har problem eller vill ge feedback?**  
Kontakta [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) för eventuella problem eller feedback som rör kryptering av lagringstjänst.

## <a name="next-steps"></a>Nästa steg
Azure Storage tillhandahåller en omfattande uppsättning funktioner för säkerhet som tillsammans bidrar utvecklare skapa säkra program. Mer information finns i den [lagringssäkerhetsguide](../storage-security-guide.md).
