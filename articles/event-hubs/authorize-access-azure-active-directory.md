---
title: Ge åtkomst med Azure Active Directory
description: Den här artikeln innehåller information om hur du auktoriserar åtkomst till Event Hubs resurser med Azure Active Directory.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 0d8d1b37e7f5ebb6eef1c76e4324041c48ab8986
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995732"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Ge åtkomst till Event Hubs resurser med Azure Active Directory
Azure Event Hubs stöder användning av Azure Active Directory (Azure AD) för att godkänna begär anden till Event Hubs resurser. Med Azure AD kan du använda rollbaserad åtkomst kontroll (RBAC) för att bevilja behörighet till ett säkerhets objekt, som kan vara en användare eller ett huvud namn för en program tjänst. Mer information om roller och roll tilldelningar finns i [förstå de olika rollerna](../role-based-access-control/overview.md).

## <a name="overview"></a>Översikt
När ett säkerhets objekt (en användare eller ett program) försöker få åtkomst till en Event Hubs-resurs måste begäran vara auktoriserad. Med Azure AD är åtkomst till en resurs en två stegs process. 

 1. Först autentiseras säkerhets objektets identitet och en OAuth 2,0-token returneras. Resurs namnet för att begära en token `https://eventhubs.azure.net/`är.
 1. Därefter skickas token som en del av en begäran till tjänsten Event Hubs för att ge åtkomst till den angivna resursen.

Autentiserings steget kräver att en programbegäran innehåller en OAuth 2,0-åtkomsttoken vid körning. Om ett program körs i en Azure-entitet, till exempel en virtuell Azure-dator, en skalnings uppsättning för virtuella datorer eller en Azure Function-app, kan den använda en hanterad identitet för att få åtkomst till resurserna. Information om hur du autentiserar begär Anden som görs av en hanterad identitet i Event Hubs-tjänsten finns i [autentisera åtkomst till azure Event Hubs-resurser med Azure Active Directory och hanterade identiteter för Azure-resurser](authenticate-managed-identity.md). 

Auktoriserings steget kräver att en eller flera RBAC-roller tilldelas säkerhets objekt. Azure Event Hubs tillhandahåller RBAC-roller som omfattar uppsättningar med behörigheter för Event Hubs resurser. Rollerna som tilldelas ett säkerhets objekt bestämmer vilka behörigheter som huvud kontot ska ha. Mer information om RBAC-roller finns i [Inbyggda RBAC-roller för Azure Event Hubs](#built-in-rbac-roles-for-azure-event-hubs). 

Interna program och webb program som gör förfrågningar till Event Hubs kan också auktoriseras med Azure AD. Information om hur du begär en åtkomsttoken och använder den för att auktorisera begär Anden för Event Hubs resurser finns i [autentisera åtkomst till Azure Event Hubs med Azure AD från ett program](authenticate-application.md). 

## <a name="assign-rbac-roles-for-access-rights"></a>Tilldela RBAC-roller för åtkomst rättigheter
Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [rollbaserad åtkomst kontroll (RBAC)](../role-based-access-control/overview.md). Azure Event Hubs definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga uppsättningar av behörigheter som används för att komma åt Event Hub-data och du kan även definiera anpassade roller för åtkomst till data.

När en RBAC-roll tilldelas till ett säkerhets objekt i Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen, Event Hubs namn området eller någon annan resurs under den. Ett säkerhets objekt i Azure AD kan vara en användare eller ett huvud namn för program tjänsten eller en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Inbyggda RBAC-roller för Azure Event Hubs
Azure tillhandahåller följande inbyggda RBAC-roller för att auktorisera åtkomst till Event Hubs data med hjälp av Azure AD och OAuth:

- [Azure Event Hubs data ägare](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Använd den här rollen för att ge fullständig åtkomst till Event Hubs resurser.
- [Azure Event Hubs data avsändare](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Använd den här rollen för att ge åtkomst till Event Hubs-resurser.
- [Azure Event Hubs data mottagare](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Använd den här rollen för att ge den förbrukar/får åtkomst till Event Hubs resurser.

## <a name="resource-scope"></a>Resurs omfång 
Innan du tilldelar en RBAC-roll till ett säkerhets objekt bör du bestämma omfattningen av åtkomsten som säkerhets objekt ska ha. Bästa praxis är att bestämma att det alltid är bäst att bara bevilja det begränsande möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Event Hubs resurser, från och med det smala omfång:

- **Konsumentgrupp**: I det här omfånget gäller roll tilldelning endast för den här entiteten. För närvarande har Azure Portal inte stöd för att tilldela en RBAC-roll till ett säkerhets objekt på den här nivån. 
- **Händelsehubben**: Roll tilldelningen gäller för entiteten Event Hub och konsument gruppen under den.
- **Namnrymd**: Roll tilldelningen sträcker sig över hela topologin av Event Hubs under namn området och till den konsument grupp som är kopplad till den.
- **Resursgrupp**: Roll tilldelningen gäller för alla Event Hubs resurser under resurs gruppen.
- **Prenumeration**: Roll tilldelningen gäller för alla Event Hubs resurser i alla resurs grupper i prenumerationen.

> [!NOTE]
> Tänk på att det kan ta upp till fem minuter att sprida RBAC-roll tilldelningar. 

Mer information om hur inbyggda roller definieras finns i [förstå roll definitioner](../role-based-access-control/role-definitions.md#management-and-data-operations). Information om hur du skapar anpassade RBAC-roller finns i [skapa anpassade roller för Azure Role-baserade Access Control](../role-based-access-control/custom-roles.md).

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du tilldelar en inbyggd-RBAC-roll till ett säkerhets objekt finns i [autentisera åtkomst till Event Hubs resurser med Azure Active Directory](authenticate-application.md).
- Lär dig [hur du skapar anpassade roller med RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Lär dig [hur du använder Azure Active Directory med händelsehubbnamnområde](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Se följande relaterade artiklar:

- [Autentisera begär anden till Azure Event Hubs från ett program med Azure Active Directory](authenticate-application.md)
- [Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Event Hubs resurser](authenticate-managed-identity.md)
- [Autentisera begär anden till Azure Event Hubs med signaturer för delad åtkomst](authenticate-shared-access-signature.md)
- [Ge åtkomst till Event Hubs resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md)