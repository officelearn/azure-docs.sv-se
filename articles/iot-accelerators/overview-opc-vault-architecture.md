---
title: OPC valv arkitektur – Azure | Microsoft Docs
description: Arkitektur för certifikat hanterings tjänst för OPC Vault
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9331473402ddd22180df3b404824969360d48164
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69995823"
---
# <a name="opc-vault-architecture"></a>Arkitektur för OPC Vault

Den här artikeln ger en översikt över **mikrotjänsten OPC Vault** och **OPC-valvet IoT Edge**.

## <a name="overview"></a>Översikt

OPC UA-program använder program instans certifikat för att tillhandahålla säkerhet på program nivå. En säker anslutning upprättas med asymmetrisk kryptering, för vilken program certifikaten tillhandahåller det offentliga och privata nyckel paret. Certifikaten kan vara självsignerade eller kan signeras av en certifikat utfärdare (CA).

Ett OPC UA-program innehåller en lista över betrodda certifikat som representerar de program som är betrodda. Dessa certifikat kan vara självsignerade, signerade av en certifikat utfärdare eller kan vara rot certifikat utfärdare eller en under certifikat utfärdare själva. Om ett betrott certifikat är en del av en större certifikat kedja, litar programmet på alla certifikat som kedjar upp till certifikatet i listan över betrodda certifikat, så länge som den fullständiga certifikat kedjan kan verifieras.

Den största skillnaden mellan att lita på självsignerade certifikat och att lita på ett CA-certifikat är den installations ansträngning som krävs för att distribuera och underhålla förtroende och ytterligare arbete för att vara värd för en företagsspecifik CA. 

För att distribuera förtroende för självsignerade certifikat för n-servrar med ett enda klient program måste alla n serverprogram certifikat installeras i listan över klient program och klient program certifikatet måste installeras på alla Listor över betrodda Server program. Den här administrativa ansträngningen är mycket betungande och lägger till ännu mer när certifikatets livstid måste beaktas och certifikaten förnyas.

Användningen av en företagsspecifik certifikat utfärdare kan avsevärt förenkla hanteringen av förtroende med flera servrar och klienter. I det här fallet genererar administratören ett CA signerat program instans certifikat en gång för varje klient och server som används. Dessutom installeras CA-certifikatet i varje program förtroende lista på alla servrar och klienter. Med den här metoden behöver endast utgångna certifikat förnyas och ersättas för de program som påverkas.

Azure-tjänsten för OPC UA-certifikat hantering är vår lösning för att hantera en företagsspecifik certifikat utfärdare för OPC UA-program som baseras på OPC-valvet.

OPC-valvet tillhandahåller en mikrotjänst som är värd för en företagsspecifik certifikat utfärdare i ett säkert moln, som backas upp av Azure AD-säkrade tjänster med Azure Key Vault med säkerhetsmoduler för maskin vara, Cosmos DB och även IoT Hub som program arkiv.

Mikrotjänsten i OPC Vault har utformats för att stödja rollbaserad arbets flöde där personliga begär Anden signerade program certifikat och där säkerhets administratörer och god kännare med signerings rättigheter i Azure Key Vault Godkänn eller avvisa dessa förfrågningar.

För kompatibilitet med befintliga OPC UA GDS-baserade lösnings lösningar är tjänsterna stöd för en OPC Vault-modul för storskaliga tjänster som implementerar *OPC UA global Discovery Server and Certificate Management* Interface för att distribuera listor över certifikat och förtroende enligt artikel 12 i specifikationen. Men från och med vår kunskap används inte detta GDS-server gränssnitt ännu och har ännu begränsade funktioner (läsar roll). [På begäran kommer vi att förbättra upplevelsen av kund förfrågningen (*)](#yet-unsupported-features).

## <a name="architecture"></a>Arkitektur

Arkitekturen baseras på OPC Vault mikrotjänst med en OPC-valv IoT Edge modul för fabriks nätverket och ett webb exempel UX för att kontrol lera arbets flödet:

![OPCVault-arkitektur](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Mikrotjänst för OPC Vault

Mikrotjänsten OPC Vault består av följande gränssnitt för att implementera arbets flödet för att distribuera och hantera en företagsspecifik certifikat utfärdare för OPC UA-program:

### <a name="application"></a>Program 
- Ett "OPC UA-program" kan vara en server eller en klient eller både och. OPC-valvet hanterar i detta fall en program registrerings utfärdare. 
- Bredvid de grundläggande åtgärderna för att registrera, uppdatera och avregistrera program finns det också gränssnitt för att hitta och fråga efter program med Sök uttryck. 
- Certifikat begär Anden måste referera till ett giltigt program för att kunna bearbeta en begäran och utfärda ett signerat certifikat med alla OPC UA-specifika tillägg. 
- Program tjänsten backas upp av en CosmosDB-databas eller [OpcTwin Device Registry (*)](#yet-unsupported-features), beroende på kund konfigurationen.

### <a name="certificate-group"></a>Certifikat grupp
- En certifikat grupp är en entitet som lagrar en rot certifikat utfärdare eller ett certifikat från en certifikat UTFÄRDAre, inklusive den privata nyckeln för att signera certifikat. 
- RSA-nyckelns längd, SHA-2-hash-längd och livstider kan konfigureras för både utfärdare och signerade program certifikat. 
- Flera grupper kan finnas i en enda tjänst för att tillåta framtida tillägg med https-, User-eller ECC-algoritmens certifikat grupper [(*)](#yet-unsupported-features). 
- CA-certifikaten lagras i Azure Key Vault med FIPS 140-2-nivå 2-HCL-moduler. Den privata nyckeln lämnar aldrig det skyddade lagrings utrymmet eftersom signeringen utförs av en AzureAD säker Key Vault-åtgärd. 
- CA-certifikaten kan förnyas med tiden och förbli fortfarande i säkert lagrings utrymme på grund av Key Vault historik. 
- Listan över återkallade certifikat för varje CA-certifikat lagras också i Key Vault som en hemlighet. När ett program har avregistrerats återkallas även program certifikatet i listan över återkallade certifikat av en administratör.
- Satsvis och återkallning av enstaka certifikat stöds.

### <a name="certificate-request"></a>Certifikatbegäran
En certifikatbegäran implementerar arbets flödet för att generera ett nytt nyckel par eller signerat certifikat med hjälp av en "certifikat signerings förfrågan" (CSR) för ett OPC UA-program. 
- Begäran lagras i en databas med tillhör ande information som ämne eller CSR och en referens till OPC UA-programmet. 
- Affärs logiken i tjänsten verifierar begäran mot den information som lagras i program databasen. Till exempel måste program-URI: n i databasen matcha program-URI i CSR.
- En säkerhets administratör med signerings rättigheter (god kännare roll) godkänner eller avvisar begäran. Om begäran godkänns genereras ett nytt nyckel par och/eller ett signerat certifikat. Den nya privata nyckeln lagras på ett säkert sätt i nyckel valvet medan det nya signerade offentliga certifikatet lagras i databasen för certifikatbegäran.
- Beställaren kan avsöka status för begäran tills den godkänns eller återkallas. Om begäran har godkänts kan den privata nyckeln och certifikatet hämtas och installeras i certifikat arkivet för OPC UA-programmet.
- Beställaren kan nu acceptera begäran om att ta bort onödig information från begär ande databasen. 

Under livs längden för ett signerat certifikat kan ett program tas bort eller så kan en nyckel bli komprometterad. I sådana fall kan CA Manager:
- Ta bort ett program, som samtidigt tar bort alla väntande och godkända certifikat begär Anden för appen. 
- Ett annat alternativ är att bara ta bort en enskild certifikat förfrågan om bara en nyckel förnyas eller komprometteras.
- Nu har komprometterats godkända och godkända certifikat begär Anden marker ATS som borttagna.
- En chef kan regelbundet köra en förnyelse av CRL för utfärdare av certifikat utfärdare. Vid förnyelse tiden återkallas alla borttagna certifikat begär Anden och certifikat serie numren läggs till i listan över återkallade certifikat. Återkallade certifikat begär Anden markeras som återkallade.
- I brådskande händelser kan enskilda certifikat begär Anden också återkallas.
- Slutligen är de uppdaterade listorna över återkallade certifikat tillgängliga för distribution till de deltagande OPC UA-klienter och-servrar.

Mer information om API: et för OPC Vault mikroservice finns i Swagger-dokumentationen för mikrotjänsten.

## <a name="opc-vault-iot-edge-module"></a>OPC-valv IoT Edge modul
För att stödja en global identifierings Server för fabriks nätverk kan OPC Vault-modulen distribueras på gränsen, köras som ett lokalt .net Core-program eller kan startas i en Docker-behållare. På grund av brist på stöd för Auth2-autentisering i den aktuella OPC UA .net-standardstacken är funktionerna i OPC Vault-modulen begränsad till en läsar roll, eftersom en användare inte kan personifieras från Edge-modulen till Micro service med OPC UA GDS standard  gränssnitt. Endast åtgärder som inte kräver rollen skrivare, administratör eller god kännare tillåts i den här punkten[(*)](#yet-unsupported-features). 

## <a name="yet-unsupported-features"></a>Funktioner som ännu inte stöds

**(*)** stöds inte ännu.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om OPC Vault-arkitekturen, här är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Bygg och distribuera OPC-valv](howto-opc-vault-deploy.md)
