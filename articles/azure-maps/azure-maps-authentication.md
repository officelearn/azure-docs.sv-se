---
title: Autentisering med Azure Maps | Microsoft Docs
description: Autentisering för användning av Azure Maps-tjänster.
author: walsehgal
ms.author: v-musehg
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 8a6bc8b71f5f8edda76faa1a8d1b20417dfba1d1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73478658"
---
# <a name="authentication-with-azure-maps"></a>Autentisering med Azure Maps

Azure Maps stöder två sätt att autentisera begär Anden: delad nyckel och Azure Active Directory (Azure AD). I den här artikeln beskrivs dessa autentiseringsmetoder för att hjälpa din implementering.

## <a name="shared-key-authentication"></a>Autentisering med delad nyckel

Autentisering med delad nyckel skickar nycklar som genereras av ett Azure Maps-konto med varje begäran om att Azure Maps.  Två nycklar skapas när ditt Azure Maps-konto skapas. För varje begäran om att Azure Maps tjänster måste prenumerations nyckeln läggas till som en parameter till URL: en.

> [!Tip]
> Vi rekommenderar att du återskapar nycklarna regelbundet. Du har två nycklar så att du kan underhålla anslutningar med en nyckel när du återskapar den andra. När du återskapar nycklar måste du uppdatera alla program som har åtkomst till kontot för att använda de nya nycklarna.

Information om hur du visar dina nycklar finns i [Visa information om autentisering](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Autentisering med Azure Active Directory (förhandsversion)

Azure Maps erbjuder nu [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) -integration för att autentisera begär anden för Azure Maps tjänster. Azure AD ger identitets baserad autentisering, inklusive [rollbaserad åtkomst kontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview), för att bevilja åtkomst på användar nivå, grupp nivå och program nivå till Azure Maps resurser. I avsnitten som följer får du hjälp att förstå begreppen och komponenterna i Azure Maps integrering med Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Autentisering med OAuth-åtkomsttoken

Azure Maps accepterar **OAuth 2,0** -åtkomsttoken för Azure AD-klienter som är associerade med en Azure-prenumeration som innehåller ett Azure Maps-konto. Azure Maps accepterar token för:

* Azure AD-användare. 
* Partner program som använder behörigheter som delegerats av användare.
* Hanterade identiteter för Azure-resurser.

Azure Maps skapar ett *unikt ID (klient-ID)* för varje Azure Maps-konto. När du kombinerar detta klient-ID med ytterligare parametrar kan du begära token från Azure AD genom att ange värdena i följande tabell, beroende på din Azure-miljö.

| Azure-miljö   | Azure AD-token-slutpunkt |
| --------------------|-------------------------|
| Offentlig Azure-        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Mer information om hur du konfigurerar Azure AD och begär token för Azure Maps finns i [hantera autentisering i Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Allmän information om hur du begär token från Azure AD finns i [Vad är autentisering?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Begär Azure Map-resurser med OAuth-token

När en token tas emot från Azure AD kan en begäran skickas till Azure Maps med följande två obligatoriska begär ande huvud uppsättningar:

| Begärandehuvud    |    Värde    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Auktorisering     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` är det Azure Maps kontobaserade GUID som visas på sidan Azure Maps autentisering.

Här är ett exempel på en Azure Maps Route-begäran som använder en OAuth-token:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Information om hur du visar ditt klient-ID finns i [Visa information om autentisering](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Kontrol lera åtkomst med RBAC

Med Azure AD kan du styra åtkomsten till skyddade resurser med RBAC. När du har skapat ditt Azure Maps-konto och registrerat ditt Azure Maps Azure AD-program i din Azure AD-klient kan du konfigurera RBAC för en användare, grupp, program eller Azure-resurs på Azure Maps-kontots Portal sida.

Azure Maps stöder Läs åtkomst kontroll för enskilda Azure AD-användare, grupper, program och Azure-tjänster via hanterade identiteter för Azure-resurser.

![Azure Maps data läsare (förhands granskning)](./media/azure-maps-authentication/concept.png)

Information om hur du visar dina RBAC-inställningar finns i [så här konfigurerar du RBAC för Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Hanterade identiteter för Azure-resurser och Azure Maps

[Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ger Azure-tjänster (Azure App Service, Azure Functions, Azure-Virtual Machines och så vidare) med en automatiskt hanterad identitet som kan beviljas åtkomst till Azure Maps tjänster.  

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du autentiserar ett program med Azure AD och Azure Maps finns i [hantera autentisering i Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Mer information om hur du autentiserar Azure Maps Kartkontroll och Azure AD finns i [använda Azure Maps Kartkontroll](https://aka.ms/amaadmc).