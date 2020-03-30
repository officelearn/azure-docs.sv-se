---
title: Ge åtkomst med Azure Active Directory
description: Den här artikeln innehåller information om hur du godkänner åtkomst till eventhubbar-resurser med Azure Active Directory.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 1fa8f7a48c03ead7e939185b23834b3049b3e21c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064868"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Auktorisera åtkomst till eventhubbar-resurser med Azure Active Directory
Azure Event Hubs stöder användning av Azure Active Directory (Azure AD) för att auktorisera begäranden till eventhubbar-resurser. Med Azure AD kan du använda rollbaserad åtkomstkontroll (RBAC) för att bevilja behörigheter till ett säkerhetsobjekt, som kan vara en användare, eller ett programtjänsthuvudnamn. Mer information om roller och rolltilldelningar finns i [Förstå de olika rollerna](../role-based-access-control/overview.md).

## <a name="overview"></a>Översikt
När ett säkerhetsobjekt (en användare eller ett program) försöker komma åt en Event Hubs-resurs måste begäran auktoriseras. Med Azure AD är åtkomst till en resurs en tvåstegsprocess. 

 1. Först autentiseras säkerhetsobjektets identitet och en OAuth 2.0-token returneras. Resursnamnet som begär en `https://eventhubs.azure.net/`token är . För Kafka-klienter är `https://<namespace>.servicebus.windows.net`resursen som begär en token .
 1. Därefter skickas token som en del av en begäran till tjänsten Event Hubs för att auktorisera åtkomst till den angivna resursen.

Autentiseringssteget kräver att en programbegäran innehåller en OAuth 2.0-åtkomsttoken vid körning. Om ett program körs inom en Azure-entitet, till exempel en Azure VM, en skaluppsättning för virtuella datorer eller en Azure Function-app, kan den använda en hanterad identitet för att komma åt resurserna. Mer information om hur du autentiserar begäranden som gjorts av en hanterad identitet till eventhubbar-tjänsten finns i [Autentisera åtkomst till Azure Event Hubs-resurser med Azure Active Directory och hanterade identiteter för Azure-resurser](authenticate-managed-identity.md). 

Auktoriseringssteget kräver att en eller flera RBAC-roller tilldelas säkerhetsobjektet. Azure Event Hubs tillhandahåller RBAC-roller som omfattar behörighetsgrupper för eventhubbar-resurser. De roller som tilldelas ett säkerhetsobjekt avgör vilka behörigheter huvudmannen ska ha. Mer information om RBAC-roller finns [i Inbyggda RBAC-roller för Azure Event Hubs](#built-in-rbac-roles-for-azure-event-hubs). 

Inbyggda program och webbprogram som gör förfrågningar till eventhubbar kan också auktorisera med Azure AD. Mer information om hur du begär en åtkomsttoken och använder den för att auktorisera begäranden om eventhubbar-resurser finns i [Autentisera åtkomst till Azure Event Hubs med Azure AD från ett program](authenticate-application.md). 

## <a name="assign-rbac-roles-for-access-rights"></a>Tilldela RBAC-roller för åtkomsträttigheter
Azure Active Directory (Azure AD) godkänner åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). Azure Event Hubs definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga uppsättningar med behörigheter som används för att komma åt händelsehubbdata och du kan också definiera anpassade roller för åtkomst till data.

När en RBAC-roll tilldelas ett Azure AD-säkerhetsobjekt beviljar Azure åtkomst till dessa resurser för det säkerhetsobjektet. Åtkomst kan begränsas till prenumerationsnivån, resursgruppen, namnområdet Event Hubs eller någon resurs under den. Ett Azure AD-säkerhetsobjekt kan vara en användare, ett programtjänsthuvudnamn eller en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Inbyggda RBAC-roller för Azure Event Hubs
Azure tillhandahåller följande inbyggda RBAC-roller för att auktorisera åtkomst till eventhubbardata med Azure AD och OAuth:

- [Azure Event Hubs Data ägare](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Använd den här rollen för att ge fullständig åtkomst till Event Hubs-resurser.
- [Azure Event Hubs Data sender](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Använd den här rollen för att ge skicka åtkomst till event hubs-resurser.
- [Azure Event Hubs Data receiver](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Använd den här rollen för att ge tidskrävande/ta emot åtkomst till eventhubbar-resurser.

## <a name="resource-scope"></a>Resursomfattning 
Innan du tilldelar en RBAC-roll till ett säkerhetsobjekt bestämmer du omfattningen av åtkomsten som säkerhetsobjektet ska ha. Bästa praxis dikterar att det alltid är bäst att bara bevilja minsta möjliga omfattning.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till resurser för eventhubbar, med början med det smalaste omfånget:

- **Konsumentgrupp**: I det här omfånget gäller rolltilldelning endast för den här entiteten. Azure-portalen stöder för närvarande inte tilldelning av en RBAC-roll till ett säkerhetsobjekt på den här nivån. 
- **Händelsenav**: Rolltilldelningen gäller för eventhub-entiteten och konsumentgruppen under den.
- **Namnområde**: Rolltilldelningen sträcker sig över hela topologin för händelsehubbar under namnområdet och till den konsumentgrupp som är associerad med den.
- **Resursgrupp**: Rolltilldelning gäller för alla resurser för händelsehubbar under resursgruppen.
- **Prenumeration**: Rolltilldelning gäller för alla eventhubbarresurser i alla resursgrupper i prenumerationen.

> [!NOTE]
> - Tänk på att RBAC-rolltilldelningar kan ta upp till fem minuter att sprida. 
> - Det här innehållet gäller både eventhubbar och eventhubbar för Apache Kafka. Mer information om stöd för Event Hubs för Kafka finns i [Event Hubs for Kafka – säkerhet och autentisering](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication).


Mer information om hur inbyggda roller definieras finns i [Förstå rolldefinitioner](../role-based-access-control/role-definitions.md#management-and-data-operations). Information om hur du skapar anpassade RBAC-roller finns i [Skapa anpassade roller för Azure Role-Based Access Control](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>Exempel
- [Exempel på Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Dessa exempel använder det gamla **Microsoft.Azure.EventHubs-biblioteket,** men du kan enkelt uppdatera det till det senaste **Azure.Messaging.EventHubs-biblioteket.** Information om hur du flyttar exemplet från att använda det gamla biblioteket till ett nytt finns i [guiden för att migrera från Microsoft.Azure.EventHubs till Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md).
- [Exempel på Azure.Messaging.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Det här exemplet har uppdaterats för att använda det senaste **Azure.Messaging.EventHubs-biblioteket.**
- [Event Hubs för Kafka - OAuth-exempel](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Nästa steg
- Lär dig hur du tilldelar en inbyggd RBAC-roll till ett säkerhetsobjekt, se [Autentisera åtkomst till eventhubbar-resurser med Azure Active Directory](authenticate-application.md).
- Lär dig hur du [skapar anpassade roller med RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Lär dig hur du [använder Azure Active Directory med EH](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Se följande relaterade artiklar:

- [Autentisera begäranden till Azure Event Hubs från ett program med Azure Active Directory](authenticate-application.md)
- [Autentisera en hanterad identitet med Azure Active Directory för att komma åt eventhubbarresurser](authenticate-managed-identity.md)
- [Autentisera begäranden till Azure Event Hubs med signaturer för delad åtkomst](authenticate-shared-access-signature.md)
- [Auktorisera åtkomst till eventhubbar-resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md)
