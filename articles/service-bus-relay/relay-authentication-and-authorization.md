---
title: Azure Relay autentisering och auktorisering | Microsoft Docs
description: Översikt över delade signatur åtkomst (SAS)-autentisering i Azure-relä
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 86a9cf2c1106180ba5c8c65849042784bfd2afcd
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
ms.locfileid: "28018125"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay-autentisering och auktorisering

Program kan autentisera till Azure-relä med delade signatur åtkomst (SAS)-autentisering. SAS-autentisering gör det möjligt för program att autentisera till Azure vidarebefordrande tjänsten med snabbtangent som konfigurerats på Relay-namnområdet. Du kan sedan använda denna nyckel för att skapa en signatur för delad åtkomst-token som klienter kan använda för att autentisera till den vidarebefordrande tjänsten.

## <a name="shared-access-signature-authentication"></a>Autentisering med delad Åtkomstsignatur

[SAS-autentisering](../service-bus-messaging/service-bus-sas.md) gör att du kan ge en användaråtkomst till Azure Relay resurser med specifika rättigheter. SAS-autentisering innebär att konfigurationen av en krypteringsnyckel med associerade behörigheter på en resurs. Klienterna kan sedan komma åt resursen genom att presentera en SAS-token som består av resurs-URI som används och en utgången signeras med den konfigurerade nyckeln.

Du kan konfigurera nycklar för SAS för en Relay-namnområdet. Till skillnad från Service Bus-meddelanden, [Relay Hybridanslutningar](relay-hybrid-connections-protocol.md) stöder obehörig eller anonym avsändare. Du kan aktivera anonym åtkomst för enheten när du skapar den, som visas i följande skärmbild visar från portalen:

![][0]

Om du vill använda SAS kan du konfigurera en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objekt på en Relay-namnområde som består av följande:

* *KeyName* som identifierar regeln.
* *PrimaryKey* är en krypteringsnyckel som används för att logga/Validera SAS-token.
* *Sekundär nyckel* är en krypteringsnyckel som används för att logga/Validera SAS-token.
* *Rättigheter* som representerar mängden lyssna skicka eller hantera rättigheter.

Auktoriseringsregler som konfigurerats på namnområdesnivån kan bevilja åtkomst till alla relay-anslutningar i ett namnområde för klienter med token som signerats med motsvarande nyckel. Upp till 12 auktorisering kan regler konfigureras på en Relay-namnrymd. Som standard en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) alla rättigheter som är konfigurerad för varje namnområde när det först har etablerats.

Om du vill få åtkomst till en entitet måste klienten kräver en SAS-token som skapats med en specifik [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS-token genereras med en krypteringsnyckel som är kopplade till auktoriseringsregeln HMAC-SHA256 resurssträngen som består av resurs-URI som åtkomst begärs och en upphör att gälla.

SAS autentiseringsstöd för Azure Relay ingår i Azure .NET SDK version 2.0 och senare. SAS har stöd för en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). API: er som accepterar en anslutningssträng som en parameter har stöd för SAS-anslutningssträngar.

## <a name="next-steps"></a>Nästa steg

- Läs [Service Bus-autentisering med signaturer för delad åtkomst](../service-bus-messaging/service-bus-sas.md) för mer information om SAS.
- Finns det [Azure Relay Hybridanslutningar protokollet guiden](relay-hybrid-connections-protocol.md) detaljerad information om den Hybridanslutningar kapaciteten.
- Motsvarande information om Service Bus-meddelanden autentisering och auktorisering finns [Service Bus-autentisering och auktorisering](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png