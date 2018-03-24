---
title: 'Databaskryptering vilande: Azure Cosmos DB | Microsoft Docs'
description: Lär dig hur Azure Cosmos DB tillhandahåller standard kryptering av alla data.
services: cosmos-db
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: ''
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: voellm
ms.openlocfilehash: df793fb0511d1289b927caad200eb8252f2b64e6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Azure DB Cosmos databaskryptering i vila

Kryptering i vila är en fras som ofta refererar till kryptering av data på nonvolatile lagringsenheter, t.ex. solid-state-hårddiskar (SSD) och hårddiskenheter (HDD). Cosmos DB lagrar primära databaserna på SSD-enheter. Dess media bilagor och säkerhetskopior lagras i Azure Blob storage som vanligtvis har säkerhetskopierats av hårddiskar. Med lanseringen av kryptering i vila för Cosmos DB krypteras alla databaser, media bilagor och säkerhetskopieringar. Informationen är krypterad under överföringen (över nätverket) och i vila (nonvolatile lagring), vilket ger dig kryptering från slutpunkt till slutpunkt.

Eftersom det är mycket enkelt att använda en PaaS-tjänst, Cosmos DB. Eftersom alla data som lagras i Cosmos-databasen är krypterat i vila och under transport, som du inte behöver göra något. Ett annat sätt att placera det är att kryptering i vila är ”på” som standard. Det finns inga kontroller för att aktivera den eller inaktivera. Vi använder den här funktionen medan vi fortsätter att uppfylla våra [tillgänglighet och prestanda SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementering av kryptering i vila för Azure Cosmos DB

Kryptering i vila implementeras med hjälp av ett antal säkerhetstekniker, inklusive system för säker lagring av nycklar, krypterade nätverk och kryptografiska API: er. System som kan dekryptera och bearbeta data måste kommunicera med system som hanterar nycklar. Diagrammet visar hur lagring av krypterade data och hantering av nycklar är åtskilda. 

![Design diagram](./media/database-encryption-at-rest/design-diagram.png)

Det grundläggande flödet av en användarbegäran är följande:
- Databasen användarkontot görs klar och lagringsnycklar hämtas via en begäran om att Resursprovidern Management-tjänsten.
- En användare skapar en anslutning till Cosmos DB via HTTPS/secure transport. (SDK: erna abstrakt information).
- Användaren skickar en JSON-dokumentet lagras över tidigare skapade säker anslutning.
- JSON-dokument indexeras om användaren har inaktiverat indexering.
- Både JSON-dokumentet och index data skrivs till säker lagring.
- Med jämna mellanrum data läses från säker lagring och säkerhetskopiera till Azure krypterade Blob Store.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>F: hur mycket kostar Azure Storage om Storage Service-kryptering aktiveras?
S: finns utan extra kostnad.

### <a name="q-who-manages-the-encryption-keys"></a>F: som hanterar krypteringsnycklarna?
S: nycklarna hanteras av Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>F: hur ofta roteras krypteringsnycklar?
S: Microsoft har interna riktlinjer för kryptering viktiga rotation, vilket följer Cosmos DB. De specifika riktlinjerna för publiceras inte. Microsoft publicera den [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), som ses som en del av interna riktlinjer och har användbara Metodtips för utvecklare.

### <a name="q-can-i-use-my-own-encryption-keys"></a>F: kan jag använda min egen krypteringsnycklar?
A: Vi har arbetat hårt hålla tjänsten lättanvända cosmos DB är en PaaS-tjänst. Vi har lagt märke till den här frågan är ofta tillfrågas som en proxy-fråga för att uppfylla efterföljandekrav som PCI-DSS. Skapa den här funktionen arbetat, vi med efterlevnad granskare så att kunder som använder Cosmos DB uppfyller deras behov utan att behöva hantera nycklar sig själva.

### <a name="q-what-regions-have-encryption-turned-on"></a>F: vad regioner har kryptering aktiverat?
S: alla Azure Cosmos DB regioner har kryptering aktiverat för alla användardata.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>F: kryptering påverkar prestanda, svarstid och genomströmning SLA: er?
S: det finns ingen inverkan eller ändringar till prestanda SLA nu att kryptering i vila är aktiverad för alla nya och befintliga konton. Kan du läsa mer om den [SLA för Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) sidan för att visa de senaste garantierna.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>F: lokala emulatorn stöder kryptering i vila?
S: emulatorn är ett fristående verktyg för utveckling och testning och använda inte Nyckelhanteringstjänster som den hantera Cosmos-DB-tjänsten använder. Vår rekommendation är att aktivera BitLocker på enheter där du lagrar känslig emulatorn testdata. Den [emulator stöder ändring av data standardkatalogen](local-emulator.md) samt med en känd plats.

## <a name="next-steps"></a>Nästa steg

En översikt över Cosmos DB säkerhets- och de senaste förbättringarna finns [Azure Cosmos DB databassäkerhet](database-security.md).
Mer information om Microsoft-certifikat finns i [Azure Säkerhetscenter](https://azure.microsoft.com/en-us/support/trust-center/).
