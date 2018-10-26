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
ms.openlocfilehash: a17fe8ed47384ed248b339643be11269b8b9cdc0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092193"
---
# <a name="security-best-practices"></a>Rekommenderade säkerhetsmetoder

Azure Digital Twins security kan exakt åtkomst till specifika resurser och åtgärder i din IoT-graf. Detta sker genom detaljerade rollen och behörighet för hantering som kallas rollbaserad åtkomstkontroll.

Azure Digital Twins utnyttjar också andra säkerhetsfunktioner som finns på Azure IoT, inklusive Azure Active Directory. Därför finns när du konfigurerar Azure Digital Twins appen inkluderar använder många av samma [säkerhetsrutiner för Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/) rekommenderas.

Den här artikeln sammanfattas viktiga metodtips att följa.

> [!IMPORTANT]
> Granska ytterligare säkerhetsresurser (inklusive din enhetsleverantörer) för att säkerställa maximal säkerhet för din IoT-sida.

## <a name="iot-security-best-practices"></a>Metodtips för IoT-säkerhet

Vissa viktiga metoder för att på ett säkert sätt skydda dina IoT-enheter är:

> [!div class="checklist"]
> * Skydda varje enhet som är ansluten till din IoT-sida på ett manipuleringssäker sätt.
> * Begränsa rollen för varje enhet, sensor och person i din IoT-sida. Om de komprometteras, minimeras effekten.
> * Möjliga användning av enhetens IP-adress filtrering och porten begränsning.
> * Begränsa bandbredden för i/o och enheten att förbättra prestanda. Hastighetsbegränsande kan öka säkerheten genom att förhindra denial of service-attacker.
> * Håll enhetens inbyggda programvara uppdaterad.

Vissa viktiga metoder för att på ett säkert sätt skydda en IoT-utrymme är:

> [!div class="checklist"]
> * Kryptera data som sparas, lagrad eller beständig.
> * Kräv lösenord eller nycklar regelbundet ändras eller uppdateras.
> * Tänk på att begränsa åtkomst och behörighet genom rollen (se Role-Based Access Control metodtips nedan).
> * Använda kraftfulla kryptering. Det innebär att kräva långa lösenord, med hjälp av säkert protokoll och tvåfaktorsautentisering.

Övervakning av IoT-resurser kan du titta på för extremvärden, hot eller Resursparametrar som faller utanför intervallet för vanligt åtgärden hanteras via Azure Analytics.

> [!NOTE]
> Mer information om händelsen bearbetning och övervakning, se vår artikel på [händelse > routning](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Metodtips för Azure Active Directory

Azure Digital Twins använder Azure Active Directory för att autentisera användare och skydda program. Azure Active Directory stöder autentisering för en mängd olika moderna arkitekturer alla baserat på branschstandardprotokoll som OAuth 2.0 eller OpenID Connect. Några viktiga metoder för att skydda din IoT-sida för Azure Active Directory är:

> [!div class="checklist"]
> * Store Azure Active Directory apphemligheter och nycklar på en säker plats som [Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Använda ett certifikat som utfärdats av en betrodd [certifikatutfärdare](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started) i stället för apphemligheter för autentisering.
> * Begränsa omfånget för OAuth 2.0 för en token.
> * Kontrollera hur lång tid som en token är giltig och om en token förblir giltigt.
> * Ange lämpliga tidsperioder som token är giltig för.
> * Uppdatera utgångna token.

## <a name="role-based-access-control-best-practices"></a>Metodtips för rollbaserad åtkomstkontroll

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om metodtips för Azure IoT [IoT säkerhetsmetoder](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Mer information om rollbaserad åtkomstkontroll, läsa [Role-Based Access Control](./security-role-based-access-control.md).

Autentisering, läsa [autentisering med API: er](./security-authenticating-apis.md).
