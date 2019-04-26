---
title: Förstå Azure Digital Twins säkerhetsmetoder | Microsoft Docs
description: Azure Digital Twins säkerhetsmetoder.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: adgera
ms.openlocfilehash: 16bb148a0b3a424c9ba3aaae422f423ebd40793b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60536992"
---
# <a name="security-best-practices"></a>Metodtips för säkerhet

Azure Digital Twins security kan exakt åtkomst till specifika resurser och åtgärder i din IoT-graf. Detta sker via detaljerade roll- och behörighetshantering som kallas [rollbaserad åtkomstkontroll](./security-role-based-access-control.md).

Azure Digital Twins använder även andra säkerhetsfunktioner som finns i Azure IoT, inklusive Azure Active Directory (AD Azure). Därför, konfigurera och skydda program som bygger på Azure Digital Twins innebär att du använder många av samma [säkerhetsrutiner för Azure IoT](../iot-fundamentals/iot-security-best-practices.md) rekommenderas.

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
> * Noggrant begränsa åtkomst och behörighet genom rollen. Se avsnittet [Metodtips för rollbaserad åtkomstkontroll](#rbac) nedan.
> * Använda kraftfulla kryptering. Kräva långa lösenord och använder säkert protokoll och tvåfaktorsautentisering.

[Övervaka](./how-to-configure-monitoring.md) IoT-resurser för att bevaka avvikare eller hot Resursparametrar som faller utanför intervallet för vanligt igen. Använda Azure Analytics för att övervaka management.

> [!NOTE]
> Läs mer på händelsebearbetning och övervakning, [dirigera händelser och meddelanden med Azure Digital Twins](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Metodtips för Azure Active Directory

Azure Digital Twins använder Azure Active Directory för att autentisera användare och skydda program. Azure Active Directory stöder autentisering för en mängd olika moderna arkitekturer. De är alla baserade på branschstandardprotokoll som OAuth 2.0 eller OpenID Connect. Några viktiga metoder för att skydda din IoT-sida för Azure Active Directory är:

> [!div class="checklist"]
> * Store Azure Active Directory apphemligheter och nycklar på en säker plats som [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Använda ett certifikat som utfärdats av en betrodd [certifikatutfärdare](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) i stället för apphemligheter för autentisering.
> * Begränsa omfånget för OAuth 2.0 för en token.
> * Kontrollera hur lång tid som en token är giltig och om en token förblir giltigt.
> * Ange lämpliga tidsperioder som token är giltig för.
> * Uppdatera utgångna token.

<div id="rbac"></div>

## <a name="role-based-access-control-best-practices"></a>Metodtips för rollbaserad åtkomstkontroll

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Nästa steg

* Mer information om metodtips för Azure IoT [IoT säkerhetsmetoder](../iot-fundamentals/iot-security-best-practices.md).

* Mer information om rollbaserad åtkomstkontroll, läsa [rollbaserad åtkomstkontroll](./security-role-based-access-control.md).

* Mer information om autentisering, läsa [autentisera med API: er](./security-authenticating-apis.md).
