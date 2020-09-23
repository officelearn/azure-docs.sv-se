---
title: inkludera fil
description: inkludera fil
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948158"
---
## <a name="object-model"></a>Objekt modell

Med token för användar åtkomst kan du ge dina program användare möjlighet att ansluta direkt till Azure Communication Services-resursen. Du genererar dessa tokens på servern, skickar dem tillbaka till klient programmet och använder dem för att initiera klient biblioteken för kommunikations tjänster. För att åstadkomma detta bör du skapa en betrodd tjänst slut punkt som kan autentisera dina användare och utfärda token för användar åtkomst. Den här tjänsten bör ha en beständig mappning mellan dina programs användar identiteter och Azure Communication Services-användare.

CommunicationIdentityClient-klassen innehåller alla funktioner för användar-och åtkomst-token. Du instansierar den med en anslutnings sträng och använder den för att hantera användare och utfärda åtkomsttoken.

> [!WARNING]
> Tokens är känsliga data, eftersom de beviljar åtkomst till en användares resurser. Därför är det viktigt att skydda token från att komprometteras. Du bör skapa en tjänst slut punkt för betrodd tjänst som bara utfärdar åtkomsttoken till behöriga användare av ditt program. Om du cachelagrar användar åtkomst-token till en lagrings plats, rekommenderar vi starkt att du använder kryptering.

### <a name="access-token-scopes"></a>Åtkomstscope för åtkomsttoken

Med omfattningar kan du ange de exakta funktionerna i Azure Communications Services som en åtkomsttoken kan auktorisera. Omfattningar tillämpas på enskilda åtkomsttoken för användare. Azure Communication Services stöder följande omfattningar för åtkomsttoken för användare:

| Name   | Beskrivning                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | Ger möjlighet att delta i en chatt                                         |
| `VoIP` | Ger möjlighet att göra och ta emot VOIP-anrop med hjälp av det anropande klient biblioteket * |
| `Pstn` | Ger möjlighet att göra PSTN-anrop med hjälp av det anropande klient biblioteket *           |

\* Funktionen stöds inte ännu och kommer snart att vara tillgänglig

Om du vill ta bort en användares möjlighet att få åtkomst till vissa särskilda funktioner bör du först [återkalla alla befintliga åtkomsttoken](#revoke-user-access-tokens) som kan innehålla oönskade omfattningar innan du utfärdar en ny token med en mer begränsad uppsättning omfång.
