---
title: Azure Active Directory Graph API | Microsoft Docs
description: En översikt och snabb starts guide för Azure AD Graph API, vilket ger programmatisk åtkomst till Azure AD via REST API slut punkter.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30c1b5a3600c48dc548561df3cd2f955347a7e64
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533038"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder [Microsoft Graph](https://developer.microsoft.com/graph) i stället för Azure AD Graph API för att få åtkomst till Azure Active Directory (Azure AD)-resurser. Vårt utvecklingsarbete koncentreras nu till Microsoft Graph och inga fler förbättringar planeras för Azure AD Graph API. Det finns ett begränsat antal scenarier där Azure AD Graph API fortfarande är lämpligt. Mer information finns i blogg inlägget [Microsoft Graph eller Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) och [migrera Azure AD Graph-appar till Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Den här artikeln gäller Azure AD Graph API. Liknande information som rör Microsoft Graph-API finns i [använda Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api).

Azure Active Directory Graph API ger program mässig åtkomst till Azure AD via REST API slut punkter. Program kan använda Azure AD Graph API för att utföra åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) på katalog data och objekt. Till exempel stöder Azure AD Graph API följande vanliga åtgärder för ett användar objekt:

* Skapa en ny användare i en katalog
* Hämta en användares detaljerade egenskaper, till exempel deras grupper
* Uppdatera en användares egenskaper, till exempel deras plats och telefonnummer, eller ändra lösen ordet
* Kontrol lera en användares grupp medlemskap för rollbaserad åtkomst
* Inaktivera ett användar konto eller ta bort det helt

Dessutom kan du utföra liknande åtgärder på andra objekt, till exempel grupper och program. Om du vill anropa Azure AD Graph API i en katalog måste ditt program vara registrerat med Azure AD. Ditt program måste också beviljas åtkomst till Azure AD Graph API. Den här åtkomsten uppnås normalt genom ett flöde för användare eller administratörs godkännande.

Om du vill börja använda Azure Active Directory Graph API, se [snabb starts guiden för Azure ad Graph API](active-directory-graph-api-quickstart.md)eller Visa den [interaktiva dokumentationen för Azure AD Graph API-referens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funktioner

Azure AD Graph API innehåller följande funktioner:

* **REST API slut punkter**: Azure AD Graph API är en RESTful-tjänst som består av slut punkter som nås med http-standardbegäranden. Azure AD Graph API stöder XML-eller JSON-innehålls typer (Java Script Object Notation) för förfrågningar och svar. Mer information finns i [referens för Azure AD Graph REST API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Autentisering med Azure AD**: varje begäran till azure AD Graph API måste autentiseras genom att lägga till en JSON Web token (JWT) i Authorization-huvudet för begäran. Denna token hämtas genom att göra en begäran till Azure ADs token-slutpunkt och tillhandahålla giltiga autentiseringsuppgifter. Du kan använda flödet OAuth 2,0-klientautentiseringsuppgifter eller auktoriseringskod för att hämta en token för att anropa grafen. Mer information finns [i OAuth 2,0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Rollbaserad auktorisering (RBAC)** : säkerhets grupper används för att utföra RBAC i Azure AD Graph API. Om du till exempel vill fastställa om en användare har åtkomst till en specifik resurs kan programmet anropa åtgärden för att [kontrol lera grupp medlemskap (transitiv)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) , som returnerar true eller false.
* **Differentiell fråga**: differentiell fråga gör att du kan spåra ändringar i en katalog mellan två tids perioder utan att behöva göra vanliga frågor till Azure AD Graph API. Den här typen av begäran kommer bara att returnera ändringar som gjorts mellan den tidigare differentiella frågan och den aktuella begäran. Mer information finns i [Azure AD Graph API differentiell fråga](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Katalog tillägg**: du kan lägga till anpassade egenskaper till katalog objekt utan att kräva ett externt data lager. Om ditt program till exempel kräver ett Skype-ID-egenskap för varje användare kan du registrera den nya egenskapen i katalogen så att den blir tillgänglig för användning på alla användar objekt. Mer information finns i [tillägg för Azure AD Graph API Directory-schema](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Skyddas av behörighets omfattningar**: Azure AD Graph API exponerar behörighets omfattningar som ger säker åtkomst till Azure AD-data med OAuth 2,0. Det stöder flera olika typer av klient program, inklusive:
  
  * användar gränssnitt som har fått delegerad åtkomst till data via auktorisering från den inloggade användaren (delegerad)
  * service/daemon-program som körs i bakgrunden utan att en inloggad användare finns och använder programdefinierad rollbaserad åtkomst kontroll
    
    Både delegerade och program behörigheter representerar ett privilegium som exponeras av Azure AD-Graph API och kan begäras av klient program via behörighets funktionerna för program registrering i [Azure Portal](https://portal.azure.com). [Azure AD Graph API behörighets omfattningar](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) innehåller information om vad som är tillgängligt för användning av klient programmet.

## <a name="scenarios"></a>Scenarier

Azure AD Graph API möjliggör många program scenarier. Följande scenarier är de vanligaste:

* **Program för branschspecifika program**: i det här scenariot arbetar en företags utvecklare för en organisation som har en Office 365-prenumeration. Utvecklaren skapar ett webb program som interagerar med Azure AD för att utföra uppgifter som att tilldela en licens till en användare. Den här uppgiften kräver åtkomst till Azure AD-Graph API, så att utvecklaren registrerar det enda klient programmet i Azure AD och konfigurerar Läs-och Skriv behörighet för Azure AD Graph API. Sedan konfigureras programmet att använda antingen sina egna autentiseringsuppgifter eller de för tillfället inloggade användaren för att hämta en token för att anropa Azure AD-Graph API.
* **Program vara som ett tjänst program (flera klient organisationer)** : i det här scenariot utvecklar en oberoende program varu leverantör (ISV) ett värdbaserat webb program för flera innehavare som tillhandahåller funktioner för användar hantering för andra organisationer som använder Azure AD. Dessa funktioner kräver åtkomst till katalog objekt, så programmet måste anropa Azure AD-Graph API. Utvecklaren registrerar programmet i Azure AD, konfigurerar det så att det kräver Läs-och Skriv behörighet för Azure AD-Graph API och aktiverar sedan extern åtkomst så att andra organisationer kan använda programmet i sin katalog. När en användare i en annan organisation autentiserar sig för programmet för första gången visas ett medgivande-dialog ruta med de behörigheter som programmet begär. Att bevilja medgivande ger sedan programmet de begärda behörigheterna till Azure AD Graph API i användarens katalog. Mer information om medgivande ramverket finns i [Översikt över medgivande ramverket](consent-framework.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du börjar använda Azure Active Directory Graph API finns i följande avsnitt:

* [Snabb starts guide för Azure AD Graph API](active-directory-graph-api-quickstart.md)
* [REST-dokumentation för Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
