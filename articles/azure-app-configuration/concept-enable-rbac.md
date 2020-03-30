---
title: Auktorisera åtkomst till Azure App-konfiguration med Azure Active Directory
description: Aktivera RBAC för att auktorisera åtkomst till din Azure App-konfigurationsinstans
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472627"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Auktorisera åtkomst till Azure App-konfiguration med Azure Active Directory
Azure App-konfigurationen stöder användning av Azure Active Directory (Azure AD) för att auktorisera begäranden till appkonfigurationsinstanser.  Med Azure AD kan du använda rollbaserad åtkomstkontroll (RBAC) för att bevilja behörigheter till ett säkerhetsobjekt.  Ett säkerhetsobjekt kan vara en användare eller ett [huvudnamn för programtjänsten](../active-directory/develop/app-objects-and-service-principals.md).  Mer information om roller och rolltilldelningar finns i [Förstå olika roller](../role-based-access-control/overview.md).

## <a name="overview"></a>Översikt
Begäranden som görs av säkerhetsobjekt (en användare eller ett program) för att komma åt en appkonfigurationsresurs måste auktoriseras.  Med Azure AD är åtkomst till en resurs en tvåstegsprocess.
1. Säkerhetsobjektets identitet autentiseras och en OAuth 2.0-token returneras.  Resursnamnet som ska begära `https://login.microsoftonline.com/{tenantID}` `{tenantID}` en token är där matchar Azure Active Directory-klient-ID som tjänstens huvudnamn tillhör.
2. Token skickas som en del av en begäran till tjänsten Appkonfiguration om du vill auktorisera åtkomst till den angivna resursen.

Autentiseringssteget kräver att en programbegäran innehåller en OAuth 2.0-åtkomsttoken vid körning.  Om ett program körs i en Azure-enhet, till exempel en Azure Functions-app, en Azure Web App eller en Azure VM, kan den använda en hanterad identitet för att komma åt resurserna.  Mer information om hur du autentiserar begäranden som gjorts av en hanterad identitet till Azure App-konfiguration finns i [Autentisera åtkomst till Azure App-konfigurationsresurser med Azure Active Directory och hanterade identiteter för Azure-resurser](howto-integrate-azure-managed-service-identity.md).

Auktoriseringssteget kräver att en eller flera RBAC-roller tilldelas säkerhetsobjektet. Azure App Configuration tillhandahåller RBAC-roller som omfattar behörighetsgrupper för appkonfigurationsresurser. De roller som tilldelas ett säkerhetsobjekt bestämmer de behörigheter som anges till huvudmannen. Mer information om RBAC-roller finns [i Inbyggda RBAC-roller för Azure App-konfiguration](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Tilldela RBAC-roller för åtkomsträttigheter
Azure Active Directory (Azure AD) godkänner åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md).

När en RBAC-roll tilldelas ett Azure AD-säkerhetsobjekt beviljar Azure åtkomst till dessa resurser för det säkerhetsobjektet. Åtkomsten begränsas till appkonfigurationsresursen. Ett Azure AD-säkerhetsobjekt kan vara en användare, ett programtjänsthuvudnamn eller en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Inbyggda RBAC-roller för Azure App-konfiguration
Azure tillhandahåller följande inbyggda RBAC-roller för att auktorisera åtkomst till appkonfigurationsdata med Azure AD och OAuth:

- Azure App Configuration Data Owner: Använd den här rollen för att ge läs-/skrivåtkomst till appkonfigurationsresurser.
- Azure App Configuration Data Reader: Använd den här rollen för att ge läsbehörighet till appkonfigurationsresurser.
- Deltagare: Använd den här rollen för att ge administratören åtkomst till tjänsten utan att bevilja åtkomst till data som lagras i appkonfigurationsinstansen.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du använder [hanterade identiteter](howto-integrate-azure-managed-service-identity.md) för att administrera tjänsten App Configuration.