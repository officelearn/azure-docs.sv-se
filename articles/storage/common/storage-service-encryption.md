---
title: Azure Storage Service-kryptering av vilande Data | Microsoft Docs
description: "Funktionen Azure Storage Service-kryptering för att kryptera din Azure-Blobblagring på tjänstsidan när data lagrades och dekryptera den vid hämtning av data."
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: lakasa
ms.openlocfilehash: 29f6a38f7a7f0f107dab8c99e750a8dec803f6f0
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure Storage-tjänstens kryptering av vilande data

Azure Storage Service kryptering (SSE) för Data i vila hjälper dig att skydda och skydda dina data för att uppfylla din organisations säkerhet och efterlevnad åtaganden. Med den här funktionen Azure Storage automatiskt krypterar dina data före beständighet till Azure Storage och dekrypterar dem före hämtning. Hantering av kryptering, kryptering i vila, dekryptering och nyckelhantering som tillhandahålls av SSE är transparent för användarna. Alla data som skrivs till Azure Storage är krypterat med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste blocket chiffer tillgängliga.

SSE är aktiverat för alla nya och befintliga lagringskonton och kan inte inaktiveras. Eftersom dina data skyddas som standard, behöver du inte ändra din kod eller dra nytta av SSE-program.

 SSE krypteras automatiskt data i alla prestandanivåer (Standard och Premium), alla distributionsmodeller (Azure Resource Manager och klassisk) och alla Azure Storage-tjänster (Blob, kön, tabell och filen). 

Du kan använda Microsoft-hanterad krypteringsnycklarna med SSE eller du kan använda dina egna krypteringsnycklar. Mer information om hur du använder egna nycklar finns [Lagringstjänstens kryptering med hjälp av kunden hanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Visa krypteringsinställningar i Azure-portalen

Om du vill visa inställningarna för Storage Service-kryptering, logga in på den [Azure-portalen](https://portal.azure.com) och välj ett lagringskonto. På den **inställningar** bladet, klickar du på inställningen för kryptering.

![Skärmbild som visar Portal krypteringsalternativet](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Vanliga frågor om Storage Service-kryptering

**F: Jag har ett befintligt klassiska lagringskonto. Kan jag aktivera SSE på den?**

S: SSE är aktiverad som standard för alla lagringskonton (klassiskt och Resource Manager storage-konton).

**F: hur kan jag för att kryptera data i mitt klassiska storage-konto?**

S: med kryptering aktiverat som standard, krypteras automatiskt nya data med storage-tjänst. 

**F: Jag har ett befintligt lagringskonto i hanteraren för filserverresurser. Kan jag aktivera SSE på den?**

S: SSE är aktiverat som standard på alla befintliga Resource Manager-lagringskonton. Detta stöds för Blobbar, tabeller, fil och Queue storage. 

**F: Jag vill att kryptera den aktuella informationen i ett befintligt lagringskonto i Resource Manager?**

S: med SSE aktiverad som standard för alla lagringskonton - klassisk och Resource Manager storage-konton. Data som redan fanns kommer inte krypteras. För att kryptera befintliga data, kan du kopiera dem till ett annat namn eller en annan behållare och tar bort okrypterad versioner. 

**F: kan jag skapa ny storage-konton med SSE aktiverad med Azure PowerShell och Azure CLI?**

S: SSE aktiveras som standard vid tidpunkten för att skapa någon lagringskonto (klassiskt och Resource Manager-konton). Du kan kontrollera egenskaper med både Azure PowerShell och Azure CLI.

**F: hur mycket kostar Azure Storage om SSE aktiveras?**

S: finns utan extra kostnad.

**F: som hanterar krypteringsnycklarna?**

S: nycklarna hanteras av Microsoft.

**F: kan jag använda min egen krypteringsnycklar?**

S: Vi arbetar på att ge funktioner för kunder att ta sina egna krypteringsnycklar.

**F: kan jag återkalla åtkomst till krypteringsnycklarna?**

S: inte vid detta tillfälle. nycklarna är fullständigt hanteras av Microsoft.

**F: är SSE aktiverad som standard när jag skapar ett nytt lagringskonto?**

S: Ja är SSE med Microsoft Managed nycklar aktiverad som standard för alla lagringskonton - Azure Resource Manager och klassiska lagringskonton. Det är aktiverat för alla tjänster samt - Blob-, tabell, kö och fillagring.

**F: hur skiljer detta sig från Azure Disk Encryption?**

S: azure Disk Encryption används för att kryptera Operativsystemet och datadiskarna i IaaS-VM. Mer information finns våra [säkerhetsguiden för lagring](../storage-security-guide.md).

**F: Vad händer om jag inte aktivera Azure Disk Encryption på datadiskar Mina?**

S: Detta fungerar sömlöst. Data krypteras med båda metoderna.

**F: mitt lagringskonto har konfigurerats som ska replikeras geo-redundant. Med SSE, min redundant kopia även krypteras?**

S: Ja, krypteras alla kopior av lagringskontot och alla redundansalternativ för – lokalt Redundant lagring (LRS), zon-Redundant lagring (ZRS), Geo-Redundant lagring (GRS) och Geo-Redundant lagring med läsbehörighet (RA-GRS) – stöds.

**Fråga: Det går inte att inaktivera kryptering för storage-konto.**

S: kryptering är aktiverat som standard och skall inte att inaktivera kryptering för ditt lagringskonto. 

**F: SSE tillåts endast i vissa regioner?**

S: SSE är tillgänglig i alla regioner för alla tjänster. 

**F: hur kan jag få någon om jag har problem eller vill ge feedback?**

A: Kontakta [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) för eventuella problem som rör Storage Service-kryptering.

## <a name="next-steps"></a>Nästa steg
Azure Storage innehåller en omfattande uppsättning säkerhetsfunktioner för, vilket tillsammans ger utvecklare möjligheten att skapa säkra program. Mer information finns i [säkerhetsguiden för lagring](../storage-security-guide.md).
