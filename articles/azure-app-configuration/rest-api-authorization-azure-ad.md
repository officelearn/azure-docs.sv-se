---
title: Azure App konfiguration REST API-Azure Active Directory auktorisering
description: Använd Azure Active Directory för auktorisering mot Azure App konfiguration med hjälp av REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bebab7c06062726f7b5c7868f984cadda3b4c98e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424411"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory Authorization-REST API referens

När Azure Active Directory (Azure AD)-autentisering används hanteras auktoriseringen av en Azure Role-baserad Access Control (RBAC). Azure RBAC kräver att användare tilldelas roller för att kunna bevilja åtkomst till resurser. Varje roll innehåller en uppsättning åtgärder som användare som tilldelats rollen kan utföra.

## <a name="roles"></a>Roller

Följande roller är inbyggda roller som är tillgängliga i Azure-prenumerationer som standard:

- **Azure App ägare av konfigurations data** : den här rollen ger fullständig åtkomst till alla åtgärder.
- **Azure App konfigurations data läsare** : den här rollen aktiverar Läs åtgärder.

## <a name="actions"></a>Åtgärder

Roller innehåller en lista med åtgärder som användare som tilldelats rollen kan utföra. Azure App konfiguration stöder följande åtgärder:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Den här åtgärden tillåter Läs åtkomst till konfigurations nyckel – värde resurser som/kv och/Labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Den här åtgärden tillåter skriv åtkomst till konfigurations nyckel värde resurser för appar.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Den här åtgärden tillåter att konfigurations nyckel resurser för appar tas bort. Obs! Om du tar bort en resurs returneras det nyckel värde som har tagits bort.

## <a name="errors"></a>Fel

```http
HTTP/1.1 403 Forbidden
```

**Orsak:** Huvudobjektet som gör begäran har inte de behörigheter som krävs för att utföra den begärda åtgärden.
**Lösning:** Tilldela den roll som krävs för att utföra den begärda åtgärden till det huvud konto som gör begäran.

## <a name="managing-role-assignments"></a>Hantera roll tilldelningar

Hantering av roll tilldelningar görs med hjälp av [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) -procedurer som är standard i alla Azure-tjänster. Det är möjligt att göra detta via Azure CLI, PowerShell, Azure Portal med mera. Officiell dokumentation om hur du kan göra roll tilldelningar finns [här](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
