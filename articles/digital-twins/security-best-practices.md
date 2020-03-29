---
title: Förstå metodtips för säkerhet – Azure Digital Twins | Microsoft-dokument
description: Läs mer om metodtips för säkerhet för Azure Digital Twins och Sakernas Internet.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122567"
---
# <a name="azure-digital-twins-security-best-practices"></a>Metodtips för Azure Digital Twins-säkerhet

Azure Digital Twins säkerhet möjliggör exakt åtkomst till specifika resurser och åtgärder i din IoT-graf. Det gör det genom detaljerad roll och behörighetshantering som kallas [rollbaserad åtkomstkontroll](./security-role-based-access-control.md).

Azure Digital Twins använder även andra säkerhetsfunktioner som finns i Azure IoT, inklusive Azure Active Directory (Azure AD). Därför innebär att konfigurera och skydda program som bygger på Azure Digital Twins att använda många av samma [Azure IoT-säkerhetspraxis](../iot-fundamentals/iot-security-best-practices.md) som för närvarande rekommenderas.

I den här artikeln sammanfattas de viktigaste metodtipsen som ska följas.

> [!IMPORTANT]
> För att säkerställa maximal säkerhet för ditt IoT-utrymme, granska ytterligare säkerhetsresurser. Se till att inkludera enhetsleverantörerna.

> [!TIP]
> Använd [Azure Security Center för IoT för](https://docs.microsoft.com/azure/asc-for-iot/) att identifiera IoT-säkerhetshot och säkerhetsproblem.

## <a name="iot-security-best-practices"></a>Metodtips för IoT-säkerhet

Några viktiga metoder för att säkert skydda dina IoT-enheter är:

> [!div class="checklist"]
> * Skydda varje enhet som är ansluten till ditt IoT-utrymme på ett manipuleringssäkert sätt.
> * Begränsa rollen för varje enhet, sensor och person inom ditt IoT-utrymme. Om den komprometterats minimeras effekten.
> * Tänk på den potentiella användningen av enhetens IP-adressfiltrering och portbegränsning.
> * Begränsa I/O- och enhetens bandbredd för att förbättra prestanda. Hastighetsbegränsning kan förbättra säkerheten genom att förhindra dos-attacker.
> * Håll enhetens inbyggda programvara, operativsystem och programvara uppdaterad.
> * Granska och granska regelbundet bästa praxis för enhet, programvara, nätverk och gateway när de fortsätter att förbättras och utvecklas.
> * Använd betrodda, certifierade och kompatibla säkerhetssystem, programvara och enheter. Granska till exempel [efterlevnadserbjudanden för](https://azure.microsoft.com/overview/trusted-cloud/compliance/) Azure Cloud.

Några viktiga metoder för att säkert säkra ett IoT-utrymme är:

> [!div class="checklist"]
> * Kryptera sparade, lagrade eller beständiga data.
> * Kräv att lösenord eller nycklar ändras eller uppdateras regelbundet.
> * Begränsa åtkomst och behörigheter efter roll noggrant. Läs avsnittet [Bästa praxis för rollbaserad åtkomstkontroll](#role-based-access-control-best-practices) nedan.
> * Överväg en delad nätverkstopologi så att enheter i varje nätverk isoleras från de andra.
> * Använd kraftfull kryptering. Kräv långa lösenord, använd säkra protokoll och [multifaktorautentisering](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

[Övervaka](./how-to-configure-monitoring.md) IoT-resurser för att hålla utkik efter extremvärden, hot eller resursparametrar som ligger utanför intervallet för den vanliga åtgärden. Använd Azure Analytics för övervakningshantering.

> [!IMPORTANT]
> Läs metodtips för Azure [IoT-säkerhet](../iot-fundamentals/iot-security-best-practices.md) för att påbörja en omfattande IoT-säkerhetsstrategi.

> [!NOTE]
> Mer information om händelsebearbetning och övervakning finns i [Dirigera händelser och meddelanden med Azure Digital Twins](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Metodtips för Azure Active Directory

Azure Digital Twins använder [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) för att autentisera användare och skydda program. Azure Active Directory stöder autentisering för en mängd moderna arkitekturer. De är alla baserade på branschstandardprotokoll som OAuth 2.0 eller OpenID Connect. Några viktiga metoder för att skydda ditt IoT-utrymme för Azure Active Directory är:

> [!div class="checklist"]
> * Lagra hemligheter och nycklar för Azure Active Directory-appar på en säker plats, till exempel [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Använd ett certifikat som utfärdats av en betrodd [certifikatutfärdare](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) i stället för apphemligheter för att autentisera.
> * Begränsa OAuth 2.0-åtkomstomfånget för en token.
> * Kontrollera hur länge en token är giltig och om en token förblir giltig.
> * Ange lämpliga tidsperioder som token är giltiga för. Uppdatera utgångna token.
> * Ta bort oanvända **url:er** och behörigheter per [rollbaserad metod för åtkomstkontroll](#role-based-access-control-best-practices).

## <a name="role-based-access-control-best-practices"></a>Metodtips för rollbaserad åtkomstkontroll

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Nästa steg

* Mer information om metodtips för Azure IoT finns i [metodtips för IoT-säkerhet](../iot-fundamentals/iot-security-best-practices.md).

* Om du vill veta mer om rollbaserad åtkomstkontroll läser du [Rollbaserad åtkomstkontroll](./security-role-based-access-control.md).

* Om du vill veta mer om autentisering läser du [Autentisera med API:er](./security-authenticating-apis.md).
