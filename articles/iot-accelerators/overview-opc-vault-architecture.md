---
title: OPC Vault-arkitektur – Azure | Microsoft-dokument
description: Tjänstarkitektur för hantering av OPC Vault-certifikat
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1e08968034134e2b9ab3b8064387d18663d5c866
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "71200159"
---
# <a name="opc-vault-architecture"></a>OPC Vault-arkitektur

Den här artikeln innehåller en översikt över OPC Vault-mikrotjänsten och OPC Vault IoT Edge-modulen.

OPC UA-program använder programinstanscertifikat för att ge säkerhet på programnivå. En säker anslutning upprättas med hjälp av asymmetrisk kryptografi, för vilken programcertifikaten tillhandahåller det offentliga och privata nyckelparet. Certifikaten kan vara självsignerade eller signerade av en certifikatutfärdare.

Ett UA-program för OPC har en lista över betrodda certifikat som representerar de program som det litar på. Dessa certifikat kan vara självsignerade eller signerade av en certifikatutfärdare eller vara en rotcertifikatutfärdare eller en undercertifikatutfärdare själva. Om ett betrott certifikat ingår i en större certifikatkedja litar programmet på alla certifikat som kedjar upp till certifikatet i förtroendelistan. Detta gäller så länge hela certifikatkedjan kan valideras.

Den största skillnaden mellan att lita på självsignerade certifikat och att lita på ett CERTIFIKATUTfärdarcertifikat är den installationsinsats som krävs för att distribuera och upprätthålla förtroendet. Det finns också ytterligare ansträngningar för att vara värd för en företagsspecifik certifikatutfärdar. 

Om du vill distribuera förtroende för självsignerade certifikat för flera servrar med ett enda klientprogram måste du installera alla serverprogramcertifikat i listan över klientprogramsförtroende. Dessutom måste du installera klientprogramcertifikatet på alla förtroendelistor för serverprogram. Den här administrativa insatsen är en stor börda och ökar till och med när du måste överväga certifikatets livstider och förnya certifikat.

Användningen av en företagsspecifik certifikatutfärdare kan avsevärt förenkla hanteringen av förtroende med flera servrar och klienter. I det här fallet genererar administratören ett certifikat för certifikatutfärdare signerad programinstans en gång för varje klient och server som används. Dessutom installeras CA-certifikatet i varje programförtroendelista, på alla servrar och klienter. Med den här metoden behöver endast utgångna certifikat förnyas och ersättas för de berörda programmen.

Azure Industrial IoT OPC UA-certifikathanteringstjänst hjälper dig att hantera en företagsspecifik certifikatutfärdare för OPC UA-program. Den här tjänsten är baserad på OPC Vault-mikrotjänsten. OPC Vault tillhandahåller en mikrotjänst som är värd för en företagsspecifik certifikatutfärdar i ett säkert moln. Den här lösningen backas upp av tjänster som skyddas av Azure Active Directory (Azure AD), Azure Key Vault med Maskinvarusäkerhetsmoduler (HSM), Azure Cosmos DB och eventuellt IoT Hub som programarkiv.

OPC Vault-mikrotjänsten är utformad för att stödja rollbaserat arbetsflöde, där säkerhetsadministratörer och godkännare med signeringsrättigheter i Azure Key Vault godkänner eller avvisar begäranden.

För kompatibilitet med befintliga OPC UA-lösningar, tjänsterna inkluderar stöd för en OPC Vault microservice backas kantmodul. Detta implementerar **gränssnittet OPC UA Global Discovery Server och Certificate Management** för att distribuera certifikat och förtroendelistor enligt del 12 i specifikationen. 


## <a name="architecture"></a>Arkitektur

Arkitekturen är baserad på OPC Vault-mikrotjänsten, med en OPC Vault IoT Edge-modul för fabriksnätverket och ett webbexempel UX för att styra arbetsflödet:

![Diagram över OPC Vault-arkitektur](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>OPC Vault-mikrotjänst

OPC Vault-mikrotjänsten består av följande gränssnitt för att implementera arbetsflödet för att distribuera och hantera en företagsspecifik certifikatutfärdare för OPC UA-program.

### <a name="application"></a>Program 
- Ett UA-program för OPC kan vara en server eller en klient, eller båda. OPC Vault fungerar i detta fall som en ansökan registreringsmyndighet. 
- Förutom de grundläggande åtgärderna för att registrera, uppdatera och avregistrera program finns det också gränssnitt för att hitta och söka efter program med sökuttryck. 
- Certifikatbegäranden måste referera till ett giltigt program för att kunna behandla en begäran och utfärda ett signerat certifikat med alla OPC UA-specifika tillägg. 
- Programtjänsten backas upp av en databas i Azure Cosmos DB.

### <a name="certificate-group"></a>Gruppen Certifikat
- En certifikatgrupp är en entitet som lagrar en rotcertifikatutfärdare eller ett undercertifikatutfärdarcertifikat, inklusive den privata nyckeln för att signera certifikat. 
- RSA-nyckellängden, SHA-2-hash-längden och livslängden kan konfigureras för både Utfärdarens certifikatutfärdare och signerade programcertifikat. 
- Du lagrar certifikatutfärdarens certifikat i Azure Key Vault, som backas upp med FIPS 140-2 Nivå 2 HSM. Den privata nyckeln lämnar aldrig den säkra lagringen, eftersom signering görs av en Key Vault-åtgärd som skyddas av Azure AD. 
- Du kan förnya certifikatutfärdarens certifikat över tid och låta dem vara i säker lagring på grund av Key Vault-historiken. 
- Återkallningslistan för varje certifikatutfärdarcertifikat lagras också i Key Vault som en hemlighet. När ett program är avregistrerat återkallas programcertifikatet också i listan över återkallade certifikat (CRL) av en administratör.
- Du kan återkalla enskilda certifikat samt batchade certifikat.

### <a name="certificate-request"></a>Begäran om certifikat
En certifikatbegäran implementerar arbetsflödet för att generera ett nytt nyckelpar eller ett signerat certifikat med hjälp av en CSR -begäran (Certificate Signing Request) för ett UA-program för Certifikatsignering. 
- Begäran lagras i en databas med medföljande information, som ämnet eller en kundtjänstrepresentant, och en hänvisning till OPC UA-programmet. 
- Affärslogiken i tjänsten validerar begäran mot den information som lagras i programdatabasen. Programmet Uri i databasen måste till exempel matcha programmet Uri i kundtjänstrepresentanten.
- En säkerhetsadministratör med signeringsrättigheter (det vill säga rollen Godkännare) godkänner eller avvisar begäran. Om begäran godkänns genereras ett nytt nyckelpar eller signerat certifikat (eller båda). Den nya privata nyckeln lagras säkert i Key Vault och det nya signerade offentliga certifikatet lagras i databasen för certifikatbegäran.
- Beställaren kan avsöka begäran status tills den godkänns eller återkallas. Om begäran godkändes kan den privata nyckeln och certifikatet hämtas och installeras i certifikatarkivet för OPC UA-programmet.
- Beställaren kan nu acceptera begäran om att ta bort onödig information från databasen för begäran. 

Under en signerad certifikats livstid kan ett program tas bort eller en nyckel äventyras. I sådana fall kan en CA-chef:
- Ta bort ett program som också tar bort alla väntande och godkända certifikatbegäranden för appen. 
- Ta bara bort en enskild certifikatbegäran om bara en nyckel förnyas eller komprometteras.

Nu komprometterade godkända och accepterade certifikatbegäranden markeras som borttagna.

En chef kan regelbundet förnya Utfärdaren CA CRL. Vid förnyelsetiden återkallas alla borttagna certifikatbegäranden och certifikatserienumren läggs till i listan över återkallade återkallade certifikat. Återkallade certifikatbegäranden markeras som återkallade. I brådskande händelser kan även enskilda certifikatbegäranden återkallas.

Slutligen är de uppdaterade CRL:erna tillgängliga för distribution till de deltagande OPC UA-klienterna och servrarna.

## <a name="opc-vault-iot-edge-module"></a>OPC Vault IoT Edge-modul
Om du vill stödja ett globalt identifieringsserver för fabriksnätverk kan du distribuera OPC Vault-modulen på gränsen. Kör den som ett lokalt .NET Core-program eller starta det i en Docker-behållare. Observera att på grund av brist på stöd för Auth2-autentisering i den aktuella OPC UA .NET Standard-stacken är funktionerna i OPC Vault-kantmodulen begränsad till en Reader-roll. En användare kan inte personifieras från kantmodulen till mikrotjänsten med hjälp av OPC UA GDS-standardgränssnittet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om OPC Vault-arkitekturen kan du:

> [!div class="nextstepaction"]
> [Skapa och distribuera OPC Vault](howto-opc-vault-deploy.md)
