---
title: 'Database-kryptering i vila: Azure Cosmos DB'
description: Lär dig hur Azure Cosmos DB tillhandahåller standard kryptering av alla data.
services: cosmos-db
author: rafats
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rafats
ms.openlocfilehash: c58d294707d347ce979af80f63e1edb7be1c1a40
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835520"
---
# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Azure Cosmos DB-databas-kryptering i vila

Vilande kryptering är en fras som ofta refererar till kryptering av data på nonvolatile lagringsenheter, t.ex. solid state-hårddiskar (SSD) och hårddiskar (HDD). Cosmos DB lagrar dess primära databaser på SSD-enheter. Dess media bifogade filer och säkerhetskopior lagras i Azure Blob storage, det stöds vanligtvis av hårddiskar. Med lanseringen av kryptering i vila för Cosmos DB krypteras alla databaser, media bifogade filer och säkerhetskopior. Dina data nu krypteras under överföring (via nätverket) och i vila (nonvolatile lagring), vilket ger dig kryptering från slutpunkt till slutpunkt.

Eftersom en PaaS-tjänst, Cosmos DB är mycket enkelt att använda. Eftersom alla data som lagras i Cosmos DB är krypterat i vila och under transport, har du inte vidta några åtgärder. Ett annat sätt att lägga till detta är att kryptering i vila är ”on” är standardvärdet. Det finns inga kontroller för att aktivera eller inaktivera. Vi ger den här funktionen medan vi fortsätter att uppfylla våra [serviceavtal för tillgänglighet och prestanda](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementering av kryptering i vila för Azure Cosmos DB

Kryptering i vila implementeras med hjälp av ett antal olika tekniker, inklusive säker nyckel lagringssystem, krypterade nätverk och kryptografiska API: er. System som kan dekryptera och bearbeta data måste kommunicera med system som hanterar nycklar. Diagrammet visar hur lagring av krypterade data och hantering av nycklar är åtskilda. 

![Design-diagram](./media/database-encryption-at-rest/design-diagram.png)

Det grundläggande flödet på användarbegäranden är följande:
- Användarkonto för databasen görs redo och lagringsnycklar hämtas via en begäran till Management Service-Resursprovider.
- En användare skapar en anslutning till Cosmos DB via HTTPS/säker transport. (SDK: erna abstrahera information.)
- Användaren skickar en JSON-dokument som ska lagras över tidigare skapade säker anslutning.
- JSON-dokument indexeras om inte användaren har inaktiverat indexering.
- Både JSON-dokument och index data skrivs till säker lagring.
- Med jämna mellanrum data läses från säker lagring och säkerhetskopieras till Azure krypterad Blob Store.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>F: hur mycket kostar Azure Storage om kryptering av lagringstjänst är aktiverat?
S: det finns ingen extra kostnad.

### <a name="q-who-manages-the-encryption-keys"></a>F: som hanterar krypteringsnycklarna?
S: nycklar som hanteras av Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>F: hur ofta roteras krypteringsnycklar?
S: Microsoft har en uppsättning interna riktlinjer för kryptering nyckelrotation som följer Cosmos DB. De specifika riktlinjerna för publiceras inte. Microsoft publicera den [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), som ses som en del av interna riktlinjer och har användbara Metodtips för utvecklare.

### <a name="q-can-i-use-my-own-encryption-keys"></a>F: kan jag använda min egen krypteringsnycklar?
S: cosmos DB är en PaaS-tjänst och vi har arbetat hårt för att hålla det enkelt att använda tjänsten. Vi har lagt märke till den här frågan begärs ofta som en proxy-fråga i syfte att uppfylla efterföljandekrav som PCI-DSS. Som en del av att skapa den här funktionen, som vi har samarbetat med efterlevnad granskare så att kunder som använder Cosmos DB uppfyller deras behov utan att behöva hantera nycklar själva.

### <a name="q-what-regions-have-encryption-turned-on"></a>F: på vilka regioner har kryptering aktiverad?
S: alla Azure Cosmos DB-regioner har kryptering aktiverad för alla användardata.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>F: kryptering påverkar prestanda, svarstid och dataflöde serviceavtal?
S: det finns ingen inverkan eller ändringar till prestanda serviceavtal nu när vilande kryptering är aktiverat för alla befintliga och nya konton. Du kan läsa mer på den [SLA för Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) och se de senaste garantierna.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>F: den lokala emulatorn stöder kryptering i vila?
S: emulatorn är ett fristående verktyg för utveckling/testning och använder inte Nyckelhanteringstjänster som hanterade Cosmos DB-tjänsten använder. Vår rekommendation är att aktivera BitLocker på enheter där du lagrar känsliga emulatorn testdata. Den [emulatorn stöder ändring av standarddatakatalogen](local-emulator.md) samt med hjälp av en känd plats.

## <a name="next-steps"></a>Nästa steg

En översikt över Cosmos DB säkerhets- och de senaste förbättringarna finns i [Azure Cosmos DB databassäkerhet](database-security.md).
Mer information om Microsoft-certifieringar finns i den [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).
