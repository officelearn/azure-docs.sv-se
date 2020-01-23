---
title: Kryptering i vila i Azure Cosmos DB
description: Lär dig hur Azure Cosmos DB tillhandahåller kryptering av vilande data och hur den har implementerats.
author: markjbrown
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: 366a8cc2d8b08c9508053eaeb8bf70622fd870cf
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548109"
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

## <a name="frequently-asked-questions"></a>Vanliga frågor

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>F: hur mycket kostar Azure Storage om kryptering av lagringstjänst är aktiverat?
S: det finns ingen extra kostnad.

### <a name="q-who-manages-the-encryption-keys"></a>F: som hanterar krypteringsnycklarna?
S: nycklar som hanteras av Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>F: hur ofta roteras krypteringsnycklar?
S: Microsoft har en uppsättning interna riktlinjer för kryptering nyckelrotation som följer Cosmos DB. De specifika riktlinjerna för publiceras inte. Microsoft publicera den [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), som ses som en del av interna riktlinjer och har användbara Metodtips för utvecklare.

### <a name="q-can-i-use-my-own-encryption-keys"></a>F: kan jag använda min egen krypteringsnycklar?
A: Ja, nu är den här funktionen tillgänglig för de nya Cosmos-kontona och detta bör göras när kontot skapas. Mer information finns i dokumentet med [hanterade nycklar för kund](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk) .

### <a name="q-what-regions-have-encryption-turned-on"></a>F: på vilka regioner har kryptering aktiverad?
S: alla Azure Cosmos DB-regioner har kryptering aktiverad för alla användardata.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>F: kryptering påverkar prestanda, svarstid och dataflöde serviceavtal?
S: det finns ingen inverkan eller ändringar till prestanda serviceavtal nu när vilande kryptering är aktiverat för alla befintliga och nya konton. Du kan läsa mer på den [SLA för Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) och se de senaste garantierna.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>F: den lokala emulatorn stöder kryptering i vila?
S: emulatorn är ett fristående verktyg för utveckling/testning och använder inte Nyckelhanteringstjänster som hanterade Cosmos DB-tjänsten använder. Vår rekommendation är att aktivera BitLocker på enheter där du lagrar känsliga emulatorn testdata. Den [emulatorn stöder ändring av standarddatakatalogen](local-emulator.md) samt med hjälp av en känd plats.

## <a name="next-steps"></a>Nästa steg

En översikt över Cosmos DB säkerhet och de senaste förbättringarna finns i [säkerhet för Azure Cosmos Database](database-security.md).
Mer information om Microsoft-certifieringar finns i den [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).
