---
title: Azure Relay-autentisering och auktorisering | Microsoft Docs
description: Översikt över signatur för delad åtkomst (SAS)-autentisering i Azure Relay
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 206cca95c590a01f69d3664fb87398bc2fcb4ad9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445797"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay-autentisering och auktorisering

Program kan autentisera till Azure Relay med signatur för delad åtkomst (SAS)-autentisering. SAS-autentisering gör det möjligt för program att autentisera till Azure Relay-tjänsten med en åtkomstnyckel som konfigurerats på Relay-namnområde. Du kan sedan använda den här nyckeln för att skapa en signatur för delad åtkomst-token som klienter kan använda för att autentisera till den vidarebefordrande tjänsten.

## <a name="shared-access-signature-authentication"></a>Autentisering med delad Åtkomstsignatur

[SAS-autentisering](../service-bus-messaging/service-bus-sas.md) gör det möjligt att ge en användaråtkomst till Azure Relay-resurser med specifika rättigheter. SAS-autentisering innebär att konfigurationen av en kryptografisk nyckel med associerade behörigheter på en resurs. Klienterna kan sedan få åtkomst till resursen genom att presentera en SAS-token som består av resurs-URI som används och ett förfallodatum som signeras med den konfigurera nyckeln.

Du kan konfigurera nycklar för Signaturen för ett Relay-namnområde. Till skillnad från Service Bus-meddelanden, [Relay-Hybridanslutningar](relay-hybrid-connections-protocol.md) stöder obehörig eller anonyma avsändare. Du kan aktivera anonym åtkomst för entiteten när du skapar den, enligt följande skärmbild från portalen:

![][0]

Om du vill använda SAS kan du konfigurera en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektet på ett Relay-namnområde som består av följande:

* *KeyName* som identifierar regeln.
* *PrimaryKey* är en krypteringsnyckel som används för att logga/Validera SAS-token.
* *Sekundär nyckel* är en krypteringsnyckel som används för att logga/Validera SAS-token.
* *Rättigheter* som representerar uppsättningen lyssna, skicka, eller hantera rättigheter beviljas.

Auktoriseringsregler som konfigurerats på namnområdesnivå kan bevilja åtkomst till alla relay-anslutningar i ett namnområde för klienter med token som signerats med motsvarande nyckel. Upp till 12 auktorisering konfigureras regler på ett Relay-namnområde. Som standard en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) med alla rättigheter som konfigureras för varje namnområde när den först har etablerats.

Om du vill få åtkomst till en entitet måste klienten kräver en SAS-token som genererats med hjälp av en specifik [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS-token genereras med en kryptografisk nyckel som är associerade med auktoriseringsregeln HMAC-SHA256 av en resurssträng som består av resurs-URI som åtkomst begärs och upphöra att gälla.

Support för SAS-autentisering för Azure Relay är inkluderade i Azure .NET SDK-version 2.0 och senare. SAS har stöd för en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alla API: er som accepterar en anslutningssträng som en parameter har stöd för SAS-anslutningssträngar.

## <a name="next-steps"></a>Nästa steg

- Fortsätt att läsa [Service Bus-autentisering med signaturer för delad åtkomst](../service-bus-messaging/service-bus-sas.md) för mer information om SAS.
- Se den [Azure Relay-Hybridanslutningar protokollguide](relay-hybrid-connections-protocol.md) detaljerad information om funktionen för Hybridanslutningar.
- Motsvarande information om Service Bus-meddelanden autentisering och auktorisering finns i [Service Bus, autentisering och auktorisering](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png