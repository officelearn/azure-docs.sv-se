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
ms.openlocfilehash: b97c303433eb8fadcda51257d37447f052ce4a3b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118845"
---
# <a name="authentication-with-azure-maps"></a>Autentisering med Azure Maps

Azure Maps stöder två olika sätt att autentisera begäranden. Delad nyckel eller Azure Active Directory (Azure AD) erbjuder olika metoder för att auktorisera varje begäran som skickas till Azure Maps. Syftet med den här artikeln är att förklara båda autentiseringsmetoderna för att hjälpa dig med implementeringen av autentisering.

## <a name="shared-key-authentication"></a>Autentisering med delad nyckel

Vid autentisering med delad nyckel skickas Azure Maps-kontogenererade nycklar med varje begäran till Azure Maps.  Två nycklar genereras när ditt Azure Maps-konto skapas.  Varje begäran till Azure Maps-tjänster kräver att prenumerationsnyckeln läggs till som en parameter i URL:en.

> [!Tip]
> Vi rekommenderar att du återskapar nycklarna regelbundet. Du får två nycklar så att du kan upprätthålla anslutningar med hjälp av en nyckel medan den andra återskapas. När du återskapar nycklarna måste du uppdatera alla program som använder det här kontot till att använda de nya nycklarna.

Om du vill visa dina nycklar läser du [Autentiseringinformation](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Autentisering med Azure Active Directory (förhandsversion)

Azure Maps erbjuder nu [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)-integrering för autentisering av begäranden för Azure Maps-tjänster.  Azure AD tillhandahåller identifieringsbaserad autentisering, däribland [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) för att bevilja åtkomst på användarnivå eller programnivå till Azure Maps-resurser. Syftet med den här artikeln är att hjälpa dig förstå begrepp och komponenter i Azure Maps Azure AD-integrering.

## <a name="authentication-with-oauth-access-tokens"></a>Autentisering med OAuth-åtkomsttoken

Azure Maps accepterar **OAuth 2.0**-åtkomsttoken för Azure AD-klientorganisationer som är associerade med Azure-prenumerationer som innehåller ett Azure Maps-konto.  Azure Maps accepterar token för:

* Azure AD-användare 
* Program från tredje part som använder behörigheter som delegeras av användare
* Hanterade identiteter för Azure-resurser

Azure Maps genererar ett `unique identifier (client ID)` för varje Azure Maps-konto.  När klient-ID kombineras med ytterligare parametrar kan token begäras från Azure AD genom värdet nedan anges:

```
https://login.microsoftonline.com
```
Mer information om hur du konfigurerar Azure AD och begär token för Azure Maps finns i avsnittet om [hur du hanterar autentisering](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Allmän information om att begära token från Azure AD finns i [Grundläggande om autentisering i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="requesting-azure-map-resources-with-oauth-tokens"></a>Begär Azure Map-resurser med OAuth-token

När en token tas emot från Azure AD kan en begäran skickas till Azure Maps med följande två nödvändiga begäranderubriker angivna:

| Begärandehuvud    |    Värde    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Auktorisering     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` är den Azure Maps-kontobaserade GUID som visas på Azure Maps-autentiseringssidan

Nedan visas exemplet för Azure Maps-dirigeringsbegäran med hjälp av OAuth-token:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Om du vill visa ditt klient-ID läser du [Autentiseringinformation](https://aka.ms/amauthdetails).

## <a name="control-access-with-role-based-access-control-rbac"></a>Styra åtkomst med rollbaserad åtkomstkontroll (RBAC)

En viktig funktion i Azure AD är att styra åtkomst till skyddade resurser via RBAC. När ditt Azure Maps-konto har skapats och du registrerar Azure Maps Azure AD-programmet i din Azure AD-klientorganisation kan du nu konfigurera RBAC för en användare, ett program eller en Azure-resurs på portalsidan för Azure Map-kontot. 

Azure Maps stöder för närvarande läsbehörighetskontroll för enskilda Azure AD-användare, -program eller Azure-tjänster via hanterade identiteter för Azure-resurser.

![begrepp](./media/azure-maps-authentication/concept.png)

Om du vill visa RBAC-inställningarna kan du läsa avsnittet om [hur du konfigurerar RBAC för Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Hanterade identiteter för Azure-resurser och Azure Maps

[Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) tillhandahåller Azure-tjänster (Azure App Service, Azure Functions, Virtual Machines osv.) med en automatiskt hanterad identitet som kan auktoriseras för åtkomst till Azure Maps-tjänster.  

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du autentiserar ett program med Azure AD och Azure Maps finns i avsnittet om [hur du hanterar autentisering](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Mer information om hur du autentiserar Azure Maps, Kartkontroll och Azure AD finns i avsnittet om [Azure AD och Kartkontroll i Azure Maps](https://aka.ms/amaadmc).