---
title: Förstå Azure Digital Twins säkerhetsmetoder | Microsoft Docs
description: Azure Digital Twins säkerhetsmetoder
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 6ca01523744dbce15f8fdb3bbe2d5a9b44510f3f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959485"
---
# <a name="security-best-practices"></a>Metodtips för säkerhet

Azure Digital Twins security kan exakt åtkomst till specifika resurser och åtgärder i din IoT-graf. Detta sker via detaljerade roll- och behörighetshantering som kallas rollbaserad åtkomstkontroll.

Azure Digital Twins använder även andra säkerhetsfunktioner som finns på Azure IoT, inklusive Azure Active Directory (AD Azure). Därför finns när du konfigurerar Azure Digital Twins appen inkluderar använder många av samma [säkerhetsrutiner för Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/) rekommenderas.

Den här artikeln sammanfattas viktiga metodtips att följa.

> [!IMPORTANT]
> Granska resurser för ytterligare säkerhet för att säkerställa maximal säkerhet för din IoT-sida. Se till att inkludera din enhetsleverantörer.

## <a name="iot-security-best-practices"></a>Metodtips för IoT-säkerhet

Vissa viktiga metoder för att på ett säkert sätt skydda dina IoT-enheter är:

> [!div class="checklist"]
> * Skydda varje enhet som är ansluten till din IoT-sida på ett manipuleringssäker sätt.
> * Begränsa rollen för varje enhet, sensor och person i din IoT-sida. Om de komprometteras, minimeras effekten.
> * Överväg att potentiella användning av enhetens IP adressen filtrering och porten begränsning.
> * Begränsa bandbredden för i/o och enheten att förbättra prestanda. Hastighetsbegränsande kan öka säkerheten genom att förhindra denial of service-attacker.
> * Kontinuerligt enhetens inbyggda programvara.

Vissa viktiga metoder för att på ett säkert sätt skydda en IoT-utrymme är:

> [!div class="checklist"]
> * Kryptera data som sparas, lagrad eller beständig.
> * Kräv lösenord eller nycklar regelbundet ändras eller uppdateras.
> * Noggrant begränsa åtkomst och behörighet genom rollen. Se avsnittet ”Role-based access control metodtips”.
> * Använda kraftfulla kryptering. Kräva långa lösenord och använder säkert protokoll och tvåfaktorsautentisering.

Övervaka IoT-resurser för att bevaka avvikare eller hot Resursparametrar som faller utanför intervallet för vanligt igen. Använda Azure Analytics för att hantera övervakning.

> [!NOTE]
> Läs mer på händelsebearbetning och övervakning, [dirigera händelser och meddelanden med Azure Digital Twins](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Metodtips för Azure Active Directory

Azure Digital Twins använder Azure AD för att autentisera användare och skydda program. Azure AD stöder autentisering för en mängd olika moderna arkitekturer. De är alla baserade på branschstandardprotokoll som OAuth 2.0 eller OpenID Connect. Några viktiga metoder för att skydda din IoT-sida för Azure AD är:

> [!div class="checklist"]
> * Store Azure AD apphemligheter och nycklar på en säker plats som [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Använda ett certifikat som utfärdats av en betrodd [certifikatutfärdare](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started) i stället för apphemligheter för autentisering.
> * Begränsa omfånget för OAuth 2.0 för en token.
> * Kontrollera hur lång tid som en token är giltig och om en token förblir giltigt.
> * Ange lämpliga tidsperioder som token är giltig för.
> * Uppdatera utgångna token.

## <a name="role-based-access-control-best-practices"></a>Metodtips för rollbaserad åtkomstkontroll

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om metodtips för Azure IoT [IoT säkerhetsmetoder](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Mer information om rollbaserad åtkomstkontroll, läsa [rollbaserad åtkomstkontroll](./security-role-based-access-control.md).

Mer information om autentisering, läsa [autentisera med API: er](./security-authenticating-apis.md).
