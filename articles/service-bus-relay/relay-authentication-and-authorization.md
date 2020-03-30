---
title: Autentisering och auktorisering av Azure Relay | Microsoft-dokument
description: Den här artikeln innehåller en översikt över SAS-autentisering (Shared Access Signature) med Azure Relay-tjänsten.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: aac5c973a99b13d5918a0162feb7f1ede443463b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514586"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay-autentisering och auktorisering

Program kan autentisera till Azure Relay med SAS-autentisering (Shared Access Signature). SAS-autentisering gör det möjligt för program att autentisera till Azure Relay-tjänsten med hjälp av en åtkomstnyckel som konfigurerats på Relay-namnområdet. Du kan sedan använda den här nyckeln för att generera en signaturtoken för delad åtkomst som klienter kan använda för att autentisera till relay-tjänsten.

## <a name="shared-access-signature-authentication"></a>Autentisering av signatur för delad åtkomst

[MED SAS-autentisering](../service-bus-messaging/service-bus-sas.md) kan du bevilja en användare åtkomst till Azure Relay-resurser med specifika rättigheter. SAS-autentisering innebär konfiguration av en kryptografisk nyckel med associerade rättigheter på en resurs. Klienter kan sedan få åtkomst till den resursen genom att presentera en SAS-token, som består av att resursen URI används och en förfallodatum som har signerats med den konfigurerade nyckeln.

Du kan konfigurera nycklar för SAS på ett relay-namnområde. Till skillnad från Service Bus-meddelanden stöder [Relay Hybrid-anslutningar](relay-hybrid-connections-protocol.md) obehöriga eller anonyma avsändare. Du kan aktivera anonym åtkomst för entiteten när du skapar den, som visas i följande skärmbild från portalen:

![][0]

Om du vill använda SAS kan du konfigurera ett [SharedAccessAuthorizationRule-objekt](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) på ett Relay-namnområde som består av följande:

* *KeyName* som identifierar regeln.
* *PrimaryKey* är en kryptografisk nyckel som används för att signera/validera SAS-token.
* *SecondaryKey* är en kryptografisk nyckel som används för att signera/validera SAS-token.
* *Rättigheter* som representerar samlingen av rättigheter lyssna, Skicka eller Hantera som beviljats.

Auktoriseringsregler som konfigurerats på namnområdesnivå kan bevilja åtkomst till alla relay-anslutningar i ett namnområde för klienter med token signerade med motsvarande nyckel. Upp till 12 sådana auktoriseringsregler kan konfigureras på ett relay-namnområde. Som standard konfigureras en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) med alla rättigheter för varje namnområde när det först etableras.

För att komma åt en entitet kräver klienten en SAS-token som genereras med hjälp av en specifik [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS-token genereras med HMAC-SHA256 för en resurssträng som består av resurs-URI som åtkomst begärs till och en förfallodatum med en kryptografisk nyckel som är associerad med auktoriseringsregeln.

SAS-autentiseringsstöd för Azure Relay ingår i Azure .NET SDK version 2.0 och senare. SAS innehåller stöd för en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alla API:er som accepterar en anslutningssträng som en parameter innehåller stöd för SAS-anslutningssträngar.

## <a name="next-steps"></a>Nästa steg

- Fortsätt läsa [Service Bus-autentisering med signaturer](../service-bus-messaging/service-bus-sas.md) för delad åtkomst för mer information om SAS.
- Mer information om hybridanslutningsfunktionen finns i [protokollguiden för Azure Relay Hybrid Connections.](relay-hybrid-connections-protocol.md)
- Mer information om autentisering och auktorisering för Service Bus Messaging finns i [Autentisering och auktorisering för Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md)Messaging . 

[0]: ./media/relay-authentication-and-authorization/hcanon.png