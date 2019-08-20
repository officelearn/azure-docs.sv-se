---
title: Kryptering i vila i Azure Cosmos DB
description: Lär dig hur Azure Cosmos DB tillhandahåller kryptering av vilande data och hur den har implementerats.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: f406f008e2c377b39deb8d151855ce7315616701
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616862"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Data kryptering i Azure Cosmos DB 

Vilande kryptering är en fras som ofta refererar till kryptering av data på nonvolatile lagringsenheter, t.ex. solid state-hårddiskar (SSD) och hårddiskar (HDD). Cosmos DB lagrar dess primära databaser på SSD-enheter. Dess media bifogade filer och säkerhetskopior lagras i Azure Blob storage, det stöds vanligtvis av hårddiskar. Med lanseringen av kryptering i vila för Cosmos DB krypteras alla databaser, media bifogade filer och säkerhetskopior. Dina data nu krypteras under överföring (via nätverket) och i vila (nonvolatile lagring), vilket ger dig kryptering från slutpunkt till slutpunkt.

Eftersom en PaaS-tjänst, Cosmos DB är mycket enkelt att använda. Eftersom alla data som lagras i Cosmos DB är krypterat i vila och under transport, har du inte vidta några åtgärder. Ett annat sätt att lägga till detta är att kryptering i vila är ”on” är standardvärdet. Det finns inga kontroller för att aktivera eller inaktivera. Azure Cosmos DB använder AES-256-kryptering i alla regioner där kontot körs. Vi ger den här funktionen medan vi fortsätter att uppfylla våra [serviceavtal för tillgänglighet och prestanda](https://azure.microsoft.com/support/legal/sla/cosmos-db).

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

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>F: Hur mycket mer kostar Azure Storage kostnad om Kryptering för lagringstjänst är aktiverat?
S: Det kostar inget extra.

### <a name="q-who-manages-the-encryption-keys"></a>F: Vem hanterar krypterings nycklarna?
S: Nycklarna hanteras av Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>F: Hur ofta roteras krypterings nycklar?
S: Microsoft har en uppsättning interna rikt linjer för rotation av krypterings nyckel, vilket Cosmos DB följande. De specifika riktlinjerna för publiceras inte. Microsoft publicera den [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), som ses som en del av interna riktlinjer och har användbara Metodtips för utvecklare.

### <a name="q-can-i-use-my-own-encryption-keys"></a>F: Kan jag använda mina egna krypterings nycklar?
S: Cosmos DB är en PaaS-tjänst och vi har arbetat hårt för att hålla tjänsten lätt att använda. Vi har lagt märke till den här frågan begärs ofta som en proxy-fråga i syfte att uppfylla efterföljandekrav som PCI-DSS. Som en del av att skapa den här funktionen, som vi har samarbetat med efterlevnad granskare så att kunder som använder Cosmos DB uppfyller deras behov utan att behöva hantera nycklar själva.

### <a name="q-what-regions-have-encryption-turned-on"></a>F: Vilka regioner har kryptering aktiverat?
S: Alla Azure Cosmos DB regioner har kryptering aktiverat för alla användar data.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>F: Påverkar kryptering prestanda latens och data flödes service avtal?
S: Det finns ingen inverkan på eller ändringar i prestanda service avtal nu som kryptering i Vila har Aktiver ATS för alla befintliga och nya konton. Du kan läsa mer på den [SLA för Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) och se de senaste garantierna.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>F: Stöder den lokala emulatorn kryptering vid vila?
S: Emulatorn är ett fristående verktyg för utveckling och testning och använder inte de nyckel hanterings tjänster som den hanterade Cosmos DB tjänsten använder. Vår rekommendation är att aktivera BitLocker på enheter där du lagrar känsliga emulatorn testdata. Den [emulatorn stöder ändring av standarddatakatalogen](local-emulator.md) samt med hjälp av en känd plats.

## <a name="next-steps"></a>Nästa steg

En översikt över Cosmos DB säkerhet och de senaste förbättringarna finns i [säkerhet för Azure Cosmos Database](database-security.md).
Mer information om Microsoft-certifieringar finns i den [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).
