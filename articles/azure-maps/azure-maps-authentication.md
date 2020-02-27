---
title: Autentiseringsmetoder | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om Azure Active Directory (Azure AD) och autentisering med delad nyckel. Båda används för Microsoft Azure Maps-tjänster. Lär dig hur du hämtar Azure Maps prenumerations nyckel.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 171219c001f43137a52f29b282fb1705b3d836aa
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649831"
---
# <a name="authentication-with-azure-maps"></a>Autentisering med Azure Maps

Azure Maps stöder två sätt att autentisera begär Anden: autentisering med delad nyckel och Azure Active Directory autentisering. I den här artikeln beskrivs dessa autentiseringsmetoder för att hjälpa dig att hjälpa din implementering av Azure Maps tjänster.

> [!NOTE]
> För att förbättra säker kommunikation med Azure Maps stöder vi nu Transport Layer Security (TLS) 1,2 och vi drar tillbaka support för TLS 1,0 och 1,1. Om du vill undvika avbrott i tjänsten **uppdaterar du dina servrar och program så att de använder TLS 1,2 före 2 April 2020**.  Om du för närvarande använder TLS 1. x kan du utvärdera din TLS 1,2-beredskap och utveckla en migrations plan med de tester som beskrivs i [lösa TLS 1,0-problemet](https://docs.microsoft.com/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Autentisering med delad nyckel

 Primära och sekundära nycklar genereras efter att Azure Maps kontot har skapats. Du uppmanas att använda den primära nyckeln som prenumerations nyckel när du anropar Azure Maps med hjälp av autentisering med delad nyckel. Autentisering med delad nyckel skickar en nyckel som genereras av ett Azure Maps-konto till en Azure Maps-tjänst. Lägg till *prenumerations nyckeln* som en parameter till URL: en för varje begäran om att Azure Maps tjänster. Den sekundära nyckeln kan användas i scenarier som löpande nyckel ändringar.  

Information om hur du visar dina nycklar i Azure Portal finns i [hantera autentisering](https://aka.ms/amauthdetails).

> [!Tip]
> Vi rekommenderar att du återskapar nycklarna regelbundet. Du har två nycklar, så att du kan underhålla anslutningar med en nyckel när du återskapar den andra. När du återskapar dina nycklar måste du uppdatera alla program som har åtkomst till ditt konto med de nya nycklarna.

## <a name="authentication-with-azure-active-directory-preview"></a>Autentisering med Azure Active Directory (förhandsversion)

Azure Maps erbjuder nu autentisering med begär Anden för Azure Maps tjänster som använder [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Azure AD ger identitets-baserad autentisering, inklusive [rollbaserad åtkomst kontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). RBAC används för att bevilja åtkomst till Azure Maps resurser på användar nivå, grupp nivå eller program nivå. I nästa avsnitt beskrivs begrepp och komponenter i Azure Maps integrering med Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Autentisering med OAuth-åtkomsttoken

Azure Maps accepterar **OAuth 2,0** -åtkomsttoken för Azure AD-klienter som är associerade med en Azure-prenumeration som innehåller ett Azure Maps-konto. Azure Maps accepterar också token för:

* Azure AD-användare
* Partner program som använder behörigheter som delegerats av användare
* Hanterade identiteter för Azure-resurser

Azure Maps skapar ett *unikt ID (klient-ID)* för varje Azure Maps-konto. Du kan begära token från Azure AD när du kombinerar det här klient-ID: t med ytterligare parametrar. Om du vill begära en token anger du värdena i följande tabell baserat på din Azure-miljö.

| Azure Environment   | Azure AD-token-slutpunkt |
| --------------------|-------------------------|
| Azure Public        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Mer information om hur du konfigurerar Azure AD och begär token för Azure Maps finns i [hantera autentisering i Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Allmän information om hur du begär token från Azure AD finns i [Vad är autentisering?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Begär Azure Map-resurser med OAuth-token

När Azure AD tar emot en token skickar Azure Maps en begäran med följande uppsättningar av obligatoriska begärandehuvuden:

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

I Azure AD använder du RBAC för att kontrol lera åtkomsten till skyddade resurser. Konfigurera ditt Azure Maps konto och registrera din Azure Maps Azure AD-klient. Azure Maps stöder Läs åtkomst kontroll för enskilda Azure AD-användare, grupper, program, Azure-resurser och Azure-tjänster via hanterade identiteter för Azure-resurser. På sidan Azure Maps Portal kan du konfigurera RBAC för dina valda roller.

![Azure Maps data läsare (förhands granskning)](./media/azure-maps-authentication/concept.png)

Information om hur du visar dina RBAC-inställningar finns i [så här konfigurerar du RBAC för Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Hanterade identiteter för Azure-resurser och Azure Maps

[Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ger Azure-tjänster en automatiskt hanterad identitet, som kan ha behörighet att komma åt Azure Maps tjänster. Några exempel på hanterade identiteter, inklusive: Azure App Service, Azure Functions och Azure Virtual Machines.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du autentiserar ett program med Azure AD och Azure Maps finns i [hantera autentisering i Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Mer information om hur du autentiserar Azure Maps Kartkontroll och Azure AD finns i [använda Azure Maps Kartkontroll](https://aka.ms/amaadmc).
