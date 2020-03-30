---
title: Autentiseringsmetoder | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om Azure Active Directory (Azure AD) och autentisering med delad nyckel. Båda används för Microsoft Azure Maps-tjänster. Läs om hur du skaffar Azure Maps-prenumerationsnyckel.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335706"
---
# <a name="authentication-with-azure-maps"></a>Autentisering med Azure Maps

Azure Maps stöder två sätt att autentisera begäranden: Autentisering av delad nyckel och Azure Active Directory-autentisering. I den här artikeln beskrivs de här autentiseringsmetoderna för att styra implementeringen av Azure Maps-tjänster.

> [!NOTE]
> För att förbättra säker kommunikation med Azure Maps stöder vi nu Transport Layer Security (TLS) 1.2 och vi drar tillbaka supporten för TLS 1.0 och 1.1. För att undvika avbrott i tjänsten **uppdaterar du servrarna och programmen så att de använder TLS 1.2 före den 2 april 2020**.  Om du för närvarande använder TLS 1.x utvärderar du beredskapen TLS 1.2 och utvecklar en migreringsplan med testningen som beskrivs i [Lösa TLS 1.0-problemet](https://docs.microsoft.com/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Autentisering av delad nyckel

 Primära och sekundära nycklar genereras när Azure Maps-kontot har skapats. Du uppmanas att använda den primära nyckeln som prenumerationsnyckel när du anropar Azure Maps med delad nyckelautentisering. Autentisering med delad nyckel skickar en nyckel som genereras av ett Azure Maps-konto till en Azure Maps-tjänst. Lägg till *prenumerationsnyckeln* som en parameter i URL:en för varje begäran till Azure Maps-tjänster. Den sekundära nyckeln kan användas i scenarier som rullande nyckeländringar.  

Information om hur du visar nycklarna i Azure-portalen finns i [Hantera autentisering](https://aka.ms/amauthdetails).

> [!Tip]
> Vi rekommenderar att du återskapar nycklarna regelbundet. Du får två nycklar, så att du kan underhålla anslutningar med en nyckel när du återskapar den andra. När du återskapar nycklarna måste du uppdatera alla program som kommer åt ditt konto med de nya nycklarna.

## <a name="authentication-with-azure-active-directory-preview"></a>Autentisering med Azure Active Directory (förhandsversion)

Azure Maps erbjuder nu begäranden autentisering för Azure Maps-tjänster med [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Azure AD tillhandahåller identitetsbaserad autentisering, inklusive [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). RBAC används för att bevilja åtkomst på användarnivå, gruppnivå eller programnivå till Azure Maps-resurser. I nästa avsnitt beskrivs begrepp och komponenter i Azure Maps-integrering med Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Autentisering med OAuth-åtkomsttoken

Azure Maps accepterar **OAuth 2.0-åtkomsttoken** för Azure AD-klienter som är associerade med en Azure-prenumeration som innehåller ett Azure Maps-konto. Azure Maps accepterar även token för:

* Azure AD-användare
* Partnerprogram som använder behörigheter som delegerats av användare
* Hanterade identiteter för Azure-resurser

Azure Maps genererar en *unik identifierare (klient-ID)* för varje Azure Maps-konto. Du kan begära token från Azure AD när du kombinerar det här klient-ID:t med ytterligare parametrar. Om du vill begära en token anger du värdena i följande tabell baserat på din Azure-miljö.

| Azure-miljö   | Slutpunkt för Azure AD-token |
| --------------------|-------------------------|
| Azure, offentlig        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Mer information om hur du konfigurerar Azure AD och begär token för Azure Maps finns [i Hantera autentisering i Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Allmän information om hur du begär token från Azure AD finns i [Vad är autentisering?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Begär Azure Map-resurser med OAuth-token

När Azure AD har tagit emot en token skickar Azure Maps en begäran med följande uppsättning obligatoriska begäranden:

| Begärandehuvud    |    Värde    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Auktorisering     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id`är det Azure Maps-kontobaserade GUID som visas på autentiseringssidan för Azure Maps.

Här är ett exempel på en Azure Maps-begäran om kartor som använder en OAuth-token:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Information om hur du visar klient-ID finns i [Visa autentiseringsinformation](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Kontrollera åtkomst med RBAC

I Azure AD använder du RBAC för att styra åtkomsten till skyddade resurser. Konfigurera ditt Azure Maps-konto och registrera din Azure Maps Azure AD-klientorganisation. Azure Maps stöder läsåtkomstkontroll för enskilda Azure AD-användare, grupper, program, Azure-resurser och Azure-tjänster via hanterade identiteter för Azure-resurser. På portalsidan i Azure Maps kan du konfigurera RBAC för dina valda roller.

![Dataläsare för Azure Maps (förhandsversion)](./media/azure-maps-authentication/concept.png)

Information om hur du visar RBAC-inställningarna finns i [Konfigurera RBAC för Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Hanterade identiteter för Azure-resurser och Azure Maps

[Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ger Azure-tjänster en automatiskt hanterad identitet, som kan auktoriseras för att komma åt Azure Maps-tjänster. Några exempel på hanterade identiteter, är: Azure App Service, Azure Functions och Azure Virtual Machines.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du autentisering av ett program med Azure AD och Azure Maps finns [i Hantera autentisering i Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Mer information om hur du kontrollerar Azure Maps Map Control och Azure AD finns [i Använda Azure Maps Map Control](https://aka.ms/amaadmc).
