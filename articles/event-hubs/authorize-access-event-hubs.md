---
title: Ge åtkomst till Azure Event Hubs
description: Den här artikeln innehåller information om olika alternativ för att tillåta åtkomst till Azure Event Hubs-resurser.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: f44be4e1d3d1186f0122bd4669ae800ab42e31d6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521295"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Ge åtkomst till Azure Event Hubs
Varje gång du publicerar eller använder händelser/data från en händelsehubb försöker klienten komma åt Event Hubs-resurser. Varje begäran till en säker resurs måste auktoriseras så att tjänsten kan se till att klienten har de behörigheter som krävs för att publicera/använda data. 

Azure Event Hubs erbjuder följande alternativ för att godkänna åtkomst till säkra resurser:

- Azure Active Directory
- Signatur för delad åtkomst

> [!NOTE]
> Den här artikeln gäller både eventhubbar och [Apache Kafka-scenarier.](event-hubs-for-kafka-ecosystem-overview.md) 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (Azure AD) integration för Event Hubs-resurser ger rollbaserad åtkomstkontroll (RBAC) för detaljerad kontroll över en klients åtkomst till resurser. Du kan använda rollbaserad åtkomstkontroll (RBAC) för att bevilja behörigheter till säkerhetsobjekt, som kan vara en användare, en grupp eller ett huvudnamn för programtjänsten. Säkerhetsobjektet autentiseras av Azure AD för att returnera en OAuth 2.0-token. Token kan användas för att auktorisera en begäran om åtkomst till en eventhubs-resurs.

Mer information om hur du autentisering med Azure AD finns i följande artiklar:

- [Autentisera begäranden till Azure Event Hubs med Azure Active Directory](authenticate-application.md)
- [Auktorisera åtkomst till eventhubbar-resurser med Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst 
SAS-resurser (Shared Access Signatures) för Event Hubs ger begränsad delegerad åtkomst till eventhubbar-resurser. Genom att lägga till begränsningar för tidsintervall som signaturen är giltig för eller om behörigheter som den ger ger flexibilitet i hanteringen av resurser. Mer information finns i [Autentisera med signaturer för delad åtkomst (SAS)](authenticate-shared-access-signature.md). 

Att auktorisera användare eller program med en OAuth 2.0-token som returneras av Azure AD ger överlägsen säkerhet och användarvänlighet över SIGNATURER för delad åtkomst (SAS). Med Azure AD behöver du inte lagra åtkomsttoken med din kod och riskera potentiella säkerhetsproblem. Även om du kan fortsätta att använda SIGNATURER för delad åtkomst (SAS) för att bevilja finkornig åtkomst till Event Hubs-resurser, erbjuder Azure AD liknande funktioner utan att behöva hantera SAS-token eller oroa dig för att återkalla en komprometterad SAS. 

Som standard är alla Event Hubs-resurser säkrade och är endast tillgängliga för kontoägaren. Även om du kan använda någon av de auktoriseringsstrategier som beskrivs ovan för att ge klienter åtkomst till Event Hub-resurser. Microsoft rekommenderar att du använder Azure AD när det är möjligt för maximal säkerhet och användarvänlighet.

Mer information om auktorisering med SAS finns i [Auktorisera åtkomst till eventhubbar-resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Nästa steg
- Granska [RBAC-exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) som publicerats i vårt GitHub-arkiv. 
- Se följande artiklar:
    - [Autentisera begäranden till Azure Event Hubs från ett program med Azure Active Directory](authenticate-application.md)
    - [Autentisera en hanterad identitet med Azure Active Directory för att komma åt eventhubbarresurser](authenticate-managed-identity.md)
    - [Autentisera begäranden till Azure Event Hubs med signaturer för delad åtkomst](authenticate-shared-access-signature.md)
    - [Auktorisera åtkomst till eventhubbar-resurser med Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Auktorisera åtkomst till eventhubbar-resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md)

