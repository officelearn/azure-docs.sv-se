---
title: Identitets modell
titleSuffix: An Azure Communication Services concept
description: Lär dig mer om identiteter och åtkomsttoken
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 336e708334778e107331f5f393476c4b3dbb98a3
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507550"
---
# <a name="identity-model"></a>Identitets modell

Azure Communication Services är Identity oberoende-tjänsten. Den här designen har flera fördelar:
- Återanvänd befintliga identiteter från ditt identitets hanterings system
- Flexibilitet för integrerings scenarier
- Håller dina identiteter privata för Azure Communication Services

I stället för att duplicera befintlig information i systemet, kommer du att underhålla mappnings förhållandet som är specifika för affärs ärendet. Exempel: mappning av identiteter 1:1, 1: N, N:1, N:M. Externa identifierare (till exempel telefonnummer, användare, enheter, program och GUID) kan inte användas som Azure-kommunikations identitet. Åtkomsttoken som genereras för Azure Communication Service-identitet används för att få åtkomst till primitiver som chatt eller samtal. 

## <a name="identity"></a>Identitet

Identiteter skapas med administrations biblioteket för Azure Communication Service. Identiteten fungerar som identifierare i konversationerna och används för att skapa åtkomsttoken. Samma identitet kan ingå i flera samtidiga sessioner över flera enheter. Identiteten kan ha flera aktiva åtkomsttoken på samma gång. Om du tar bort identitet, resurs eller prenumeration får du ogiltig ogiltighet av alla dess åtkomsttoken och borttagning av alla data som lagras för den här identiteten. Borttagen identitet kan inte utfärda nya åtkomsttoken, varken komma åt tidigare lagrade data (till exempel chat-meddelanden). 

Du debiteras inte med antalet identiteter som du har, men med användning av primitiver. Antalet identiteter behöver inte begränsas, hur du mappar appens identiteter till Azure Communication Services-identiteter. Med kopplings friheten tillkommer ansvar i sekretess avtalet. När ditt programs användare vill tas bort från systemet måste du ta bort alla identiteter som har associerats med den användaren.

Azure Communication Services tillhandahåller inte särskilda identiteter för anonyma användare. Den behåller inte mappningen mellan användare och identiteter, men den förstår inte om identiteten är anonym. Du kan utforma konceptet så att det passar dina behov. Vi rekommenderar att du skapar en ny identitet för varje programs anonyma användare. Med den giltiga åtkomsttoken kan vem som helst få åtkomst till identitetens innehåll som inte har tagits bort. Till exempel chat-meddelanden som skickas av användaren. Åtkomsten är begränsad enbart till omfattningar, som är en del av åtkomsttoken. Mer information om omfattningar finns i avsnittet åtkomst- *token*.

### <a name="mapping-of-identities"></a>Mappning av identiteter

Azure Communication Services replikerar inte funktionerna i IMS. Det ger inte kunderna möjlighet att använda kundspecifika identiteter. Till exempel telefonnummer eller e-postadress. I stället innehåller den unika identifierare som du kan tilldela dina program identiteter. Azure Communication Services lagrar ingen typ av information som kan avslöja användarnas verkliga identitet.

I stället för duplicering rekommenderar vi att du utformar, hur användare från din identitets domän kommer att mappas till Azure Communication Service-identiteter. Du kan följa alla typer av mönster 1:1, 1: N, N:1 eller M:N. Du kan bestämma om en enskild användare ska mappas till en enskild identitet eller till flera identiteter. När en ny identitet skapas, uppmanas du att lagra mappningen av den här identiteten till programmets användare eller användare. Eftersom identiteter kräver åtkomst-token för användning av primitiver måste identiteten vara känd för programmets användare eller användare.

Om du använder Relations databas för lagring av användare kan implementeringen variera beroende på ditt mappnings scenario. För scenarier med mappning 1:1 eller N:1 kan du lägga till en *CommunicationServicesId* -kolumn i tabellen för att lagra din Azure Communication Services-identitet. I scenarier med relation 1: N eller N:M kan du överväga att skapa en separat tabell i Relations databasen.

## <a name="access-token"></a>Åtkomsttoken

Åtkomsttoken är en JWT-token som kan användas för att få åtkomst till Azure Communication Service-primitiver. Utfärdad åtkomsttoken har integritets skydd, dess anspråk kan inte ändras efter utfärdande. Det vill säga att den manuella ändringen av egenskaper, till exempel identitet, förfallo datum eller omfång, blir åtkomsttoken ogiltig. Användningen av primitiver med ogiltiga token leder till att åtkomsten till primitiven nekas. 

Egenskaperna för åtkomsttoken är: *identitet, förfallo datum* och *omfång*. Åtkomsttoken är alltid giltig i 24 timmar. Efter den här tiden är åtkomsttoken ogiltig och kan inte användas för att få åtkomst till någon primitiv. Identiteten måste ha ett sätt, så här begär du en ny åtkomsttoken från Server sidan. Parameter *omfånget* definierar en icke-tom uppsättning primitiver som kan användas. Azure Communication Services stöder följande omfång för åtkomsttoken:

|Name|Beskrivning|
|---|---|
|Chatt|  Ger möjlighet att delta i en chatt|
|VoIP|  Ger möjlighet att anropa identiteter och telefonnummer|


Om du vill återkalla åtkomst-token innan det går ut kan du göra det med hjälp av Azure Communication Service administrations bibliotek. Återkallande av token inte omedelbart och tar upp till 15 minuter att sprida. Borttagning av identitet, resurs eller prenumeration kommer att orsaka åter kallelse av alla åtkomsttoken. Om du vill ta bort en användares möjlighet att få åtkomst till vissa funktioner kan du återkalla alla åtkomsttoken. Utfärda sedan en ny åtkomsttoken med en mer begränsad uppsättning omfång.
Rotationen av åtkomst nycklar för Azure Communication Service kommer att orsaka återkallande av alla aktiva åtkomsttoken som skapats med tidigare åtkomst nyckel. Alla identiteter kommer att förlora åtkomst till Azure Communication service och krävs för att utfärda nya åtkomsttoken. 

Vi rekommenderar att du utfärdar åtkomsttoken på Server sidan och inte i klient programmet. Anledningen är att den utfärdande nyckeln kräver åtkomst nyckel eller som ska hanteras av identiteten. Det rekommenderas inte av säkerhets skäl att dela åtkomst nycklarna med klient programmet. Klient programmet bör använda den betrodda tjänst slut punkten som kan autentisera dina klienter och utfärda åtkomsttoken för deras räkning. Mer information om arkitekturen hittar du [här](./client-and-server-architecture.md).

Om du cachelagrar åtkomsttoken till en lagrings plats rekommenderar vi att du använder kryptering. Åtkomsttoken är känsliga data och kan användas för skadlig aktivitet om den inte är skyddad. Med hjälp av åtkomsttoken kan du initiera SDK och få åtkomst till API: et. Det tillgängliga API: t är endast begränsat baserat på omfattningar, som åtkomsttoken har. Vi rekommenderar att du endast utfärdar åtkomsttoken med omfattningar, som krävs.