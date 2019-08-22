---
title: Förstå Azure Digitals dubbla säkerhets rekommendationer | Microsoft Docs
description: Azure Digitals dubblare säkerhets metod tips.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: v-adgera
ms.openlocfilehash: f211206bc2408fe55f6742622b5b0e56d1ab4d90
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656528"
---
# <a name="security-best-practices"></a>Metodtips för säkerhet

Azure Digitals dubbla säkerhet ger exakt åtkomst till specifika resurser och åtgärder i IoT-diagrammet. Det gör det genom detaljerad roll-och behörighets hantering som kallas [rollbaserad åtkomst kontroll](./security-role-based-access-control.md).

Azure Digital-dubbla finns också med andra säkerhetsfunktioner som finns i Azure IoT, inklusive Azure Active Directory (Azure AD). Av den anledningen rekommenderar vi att du använder många av samma [Azure IoT](../iot-fundamentals/iot-security-best-practices.md) -säkerhetspraxiser för att konfigurera och säkra program som bygger på Azures digitala dubbla.

I den här artikeln sammanfattas viktiga metod tips.

> [!IMPORTANT]
> Granska ytterligare säkerhets resurser för att säkerställa maximal säkerhet för ditt IoT-utrymme. Se till att ta med dina enhets leverantörer.

> [!TIP]
> Använd [Azure Security Center för IoT](https://docs.microsoft.com/azure/asc-for-iot/) för att identifiera IoT-säkerhetshot och sårbarheter.

## <a name="iot-security-best-practices"></a>Metodtips för IoT-säkerhet

Några viktiga metoder för att säkra dina IoT-enheter på ett säkert sätt är:

> [!div class="checklist"]
> * Skydda alla enheter som är anslutna till ditt IoT-utrymme på ett Manipulerings sätt.
> * Begränsa rollen för varje enhet, sensor och person inom ditt IoT-utrymme. Om den komprometteras är resultatet minimerat.
> * Överväg den potentiella användningen av enhets filtrering och port begränsning i enheten.
> * Begränsa I/O-och enhets bandbredden för att förbättra prestandan. Hastighets begränsning kan förbättra säkerheten genom att förhindra DoS-attacker (Denial-of-Service).
> * Se till att enhetens inbyggda program vara är uppdaterad.
> * Granska och granska regelbundet rekommenderade säkerhets metoder för enhet, nätverk och gateway när de fortsätter att förbättra och utveckla.

Några viktiga metoder för att säkert skydda ett IoT-utrymme är:

> [!div class="checklist"]
> * Kryptera sparade, lagrade eller beständiga data.
> * Kräv lösen ord eller nycklar som ska ändras regelbundet eller uppdateras.
> * Begränsa åtkomsten och behörigheten noggrant efter roll. Se metod tipsen för den [rollbaserade åtkomst kontrollen](#rbac) nedan.
> * Överväg en delad nätverkstopologi så att enheterna i varje nätverk isoleras från de andra.
> * Använd kraftfull kryptering. Kräv långa lösen ord och Använd säkra protokoll och tvåfaktorautentisering.

[Övervaka](./how-to-configure-monitoring.md) IoT-resurser för att se om det finns extrem värden, hot eller resurs parametrar som ligger utanför det normala drift intervallet. Använd Azure Analytics för övervakning av hantering.

> [!NOTE]
> Mer information om händelse bearbetning och övervakning finns i [dirigera händelser och meddelanden med Azure Digitals](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Metod tips för Azure Active Directory

Azure Digitals dubblare använder Azure Active Directory för att autentisera användare och skydda program. Azure Active Directory stöder autentisering för en mängd moderna arkitekturer. De är baserade på bransch standard protokoll som OAuth 2,0 eller OpenID Connect. Några viktiga metoder för att skydda IoT-utrymmet för Azure Active Directory är:

> [!div class="checklist"]
> * Lagra Azure Active Directory-appens hemligheter och nycklar på en säker plats, till exempel [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Använd ett certifikat som utfärdats av en betrodd [certifikat utfärdare](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) i stället för att program hemligheter ska autentiseras.
> * Begränsa OAuth 2,0-omfånget för åtkomst för en token.
> * Verifiera hur lång tid en token är giltig och om en token fortfarande är giltig.
> * Ange lämpliga tids längder som token är giltiga för.
> * Uppdatera utgångna token.

<div id="rbac"></div>

## <a name="role-based-access-control-best-practices"></a>Metod tips för rollbaserad åtkomst kontroll

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Nästa steg

* Läs mer om bästa praxis för Azure IoT i [metod tips för IoT-säkerhet](../iot-fundamentals/iot-security-best-practices.md).

* Läs [rollbaserad åtkomst kontroll](./security-role-based-access-control.md)om du vill veta mer om rollbaserad åtkomst kontroll.

* Läs [autentisera med API: er](./security-authenticating-apis.md)om du vill veta mer om autentisering.
