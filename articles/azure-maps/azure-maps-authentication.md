---
title: Autentisering med Azure Maps | Microsoft Docs
description: Autentisering för användning av Azure Maps-tjänster.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9dfe4024607e106565984d6d49de94d793bf7a8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799088"
---
# <a name="authentication-with-azure-maps"></a>Autentisering med Azure Maps

Azure Maps stöder två sätt att autentisera begäranden: Delad nyckel och Azure Active Directory (AD Azure). Den här artikeln förklarar dessa autentiseringsmetoder för att guida din implementering.

## <a name="shared-key-authentication"></a>Autentisering med delad nyckel

Autentisering med delad nyckel skickar nycklar som genererats av ett Azure Maps-konto med varje begäran om att Azure Maps.  Två nycklar genereras när ditt Azure Maps-konto skapas. Prenumerationsnyckeln måste läggas till som en parameter i URL: en för varje begäran till Azure Maps-tjänsterna.

> [!Tip]
> Vi rekommenderar att du återskapar nycklarna regelbundet. Du får se två nycklar så att du kan upprätthålla anslutningar med en nyckel medan den andra återskapas. När du återskapar dina nycklar kan behöva du uppdatera alla program som använder kontot för att använda de nya nycklarna.

Information om hur du visar dina nycklar finns i [visa information om autentisering](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Autentisering med Azure Active Directory (förhandsversion)

Azure Maps nu erbjuder [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) -integrering för autentisering av begäranden för Azure Maps-tjänsterna. Azure AD erbjuder identitet-baserad autentisering, inklusive [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview), för att bevilja användarnivå eller programnivå åtkomst till Azure Maps-resurser. Avsnitten som följer kan hjälpa dig att förstå begrepp och komponenter i Azure Maps-integrering med Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Autentisering med OAuth-åtkomsttoken

Azure Maps accepterar **OAuth 2.0** åtkomsttoken för Azure AD-klienter som är associerade med en Azure-prenumeration som innehåller ett Azure Maps-konto. Azure Maps accepterar token för:

* Azure AD-användare. 
* Partnerprogram som använder behörigheter som delegerats av användare.
* Hanterade identiteter för Azure-resurser.

Azure Maps genererar en *unika identifierare (klient-ID)* för varje Azure Maps-konto. När du kombinerar detta klient-ID med ytterligare parametrar kan begära du token från Azure AD genom att ange följande värde:

```
https://login.microsoftonline.com
```
Läs mer om hur du konfigurerar Azure AD och begära token för Azure Maps [hantera autentisering i Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Allmän information om hur du begär token från Azure AD finns i [vad är authentication?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Begär Azure Map resurser med OAuth-token

När en token har tagits emot från Azure AD, kan en begäran skickas till Azure Maps med följande två nödvändiga begäran rubriker:

| Begärandehuvud    |    Värde    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Auktorisering     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` är Azure Maps baserade GUID som visas på sidan för Azure Maps-autentisering.

Här är ett exempel på en begäran för Azure Maps väg som använder en OAuth-token:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Information om hur du visar klient-ID finns i [visa information om autentisering](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Kontrollera åtkomst med RBAC

Azure AD kan du styra åtkomsten till skyddade resurser med hjälp av RBAC. När du skapar ditt Azure Maps-konto och registrera ditt Azure Maps Azure AD-program i Azure AD-klienten, kan du konfigurera RBAC för en användare, ett program eller en Azure-resurs på portalsidan för Azure Maps-konto.

Azure Maps stöder Läs åtkomstkontroll för enskilda användare, program och Azure-tjänster via hanterade identiteter för Azure-resurser i Azure AD.

![Azure Maps-dataläsare (förhandsgranskning)](./media/azure-maps-authentication/concept.png)

Information om hur du visar RBAC-inställningar finns i [hur du konfigurerar RBAC för Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Hanterade identiteter för Azure-resurser och Azure Maps

[Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) tillhandahålla Azure-tjänster (Azure App Service, Azure Functions, Azure Virtual Machines och så vidare) med en automatiskt hanterade identitet som kan ha behörighet för åtkomst till Azure Maps-tjänsterna.  

## <a name="next-steps"></a>Nästa steg

* Mer information om autentisera ett program med Azure AD och Azure Maps, se [hantera autentisering i Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Mer information om autentisering Azure Maps-Kartkontroll och Azure AD finns [använder du Azure Maps-Kartkontroll](https://aka.ms/amaadmc).