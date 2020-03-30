---
title: Kryptering i vila i Azure Cosmos DB
description: Lär dig hur Azure Cosmos DB tillhandahåller kryptering av data i vila och hur den implementeras.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: db20388b5277e000ffe7055e9840742d6af7788e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062604"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Datakryptering i Azure Cosmos DB 

Kryptering i vila är en fras som ofta refererar till kryptering av data på icke-flyktiga lagringsenheter, till exempel SSD-enheter (Solid State Drives) och hårddiskar (HDD). Cosmos DB stores its primary databases on SSDs. Dess mediebilagor och säkerhetskopior lagras i Azure Blob-lagring, som vanligtvis backas upp av hårddiskar. Med krypteringens utgivning i vila för Cosmos DB krypteras alla databaser, mediebilagor och säkerhetskopior. Dina data är nu krypterade under överföring (över nätverket) och i vila (icke-flyktig lagring), vilket ger dig end-to-end-kryptering.

Som PaaS-tjänst är Cosmos DB mycket lätt att använda. Eftersom alla användardata som lagras i Cosmos DB krypteras i vila och under transport behöver du inte vidta några åtgärder. Ett annat sätt att uttrycka detta är att kryptering i vila är "på" som standard. Det finns inga kontroller för att stänga av eller slå på den. Azure Cosmos DB använder AES-256-kryptering på alla regioner där kontot körs. Vi tillhandahåller den här funktionen medan vi fortsätter att uppfylla våra [tillgänglighet och prestanda SLAs.](https://azure.microsoft.com/support/legal/sla/cosmos-db)

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementering av kryptering i vila för Azure Cosmos DB

Kryptering i vila implementeras med hjälp av ett antal säkerhetstekniker, inklusive säkra nyckellagringssystem, krypterade nätverk och kryptografiska API:er. System som dekrypterar och bearbetar data måste kommunicera med system som hanterar nycklar. Diagrammet visar hur lagring av krypterade data och hanteringen av nycklar separeras. 

![Diagram över design](./media/database-encryption-at-rest/design-diagram.png)

Det grundläggande flödet för en användarbegäran är följande:
- Användarkontot görs klart och lagringsnycklar hämtas via en begäran till Resursleverantören för hanteringstjänster.
- En användare skapar en anslutning till Cosmos DB via HTTPS/secure transport. (SDK:erna abstraherar detaljerna.)
- Användaren skickar ett JSON-dokument som ska lagras via den tidigare skapade säkra anslutningen.
- JSON-dokumentet indexeras om inte användaren har inaktiverat indexeringen.
- Både JSON-dokumentet och indexdata skrivs för att skydda lagringen.
- Med jämna mellanrum läs-data från säker lagring och säkerhetskopieras till Azure Encrypted Blob Store.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>F: Hur mycket mer kostar Azure Storage om Storage Service-kryptering är aktiverad?
S: Det finns ingen extra kostnad.

### <a name="q-who-manages-the-encryption-keys"></a>F: Vem hanterar krypteringsnycklarna?
S: Nycklarna hanteras av Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>F: Hur ofta roteras krypteringsnycklar?
S: Microsoft har en uppsättning interna riktlinjer för rotation av krypteringsnyckel, vilket Cosmos DB följer. De särskilda riktlinjerna offentliggörs inte. Microsoft publicerar [SDL (Security Development Lifecycle),](https://www.microsoft.com/sdl/default.aspx)som ses som en delmängd av intern vägledning och har användbara metodtips för utvecklare.

### <a name="q-can-i-use-my-own-encryption-keys"></a>F: Kan jag använda mina egna krypteringsnycklar?
S: Ja nu är den här funktionen tillgänglig för de nya kosmoskontona och detta bör göras när kontot skapas. Gå igenom [dokumentet Kundhanterade nycklar](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk) för mer information.

### <a name="q-what-regions-have-encryption-turned-on"></a>F: Vilka regioner har kryptering aktiverats?
S: Alla Azure Cosmos DB-regioner har kryptering aktiverad för alla användardata.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>F: Påverkar kryptering slas för prestandafördröjning och dataflöde?
S: Det finns ingen påverkan eller ändringar i prestanda-SLA:erna nu när kryptering i vila är aktiverad för alla befintliga och nya konton. Du kan läsa mer på [SLA för Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) sida för att se de senaste garantierna.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>F: Stöder den lokala emulatorn kryptering i vila?
S: Emulatorn är ett fristående dev/test-verktyg och använder inte de nyckelhanteringstjänster som den hanterade Cosmos DB-tjänsten använder. Vår rekommendation är att aktivera BitLocker på enheter där du lagrar känsliga emulatortestdata. [Emulatorn stöder ändring av standarddatakatalogen](local-emulator.md) samt användning av en välkänd plats.

## <a name="next-steps"></a>Nästa steg

En översikt över Cosmos DB-säkerhet och de senaste förbättringarna finns i [Azure Cosmos databassäkerhet](database-security.md).
Mer information om Microsoft-certifieringar finns i [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).
