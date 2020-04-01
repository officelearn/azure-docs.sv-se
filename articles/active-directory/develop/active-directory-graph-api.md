---
title: Azure Active Directory Graph API | Microsoft-dokument
description: En översikts- och snabbstartsguide för Azure AD Graph API, som tillåter programmatisk åtkomst till Azure AD via REST API-slutpunkter.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9515238449b3ece79815c9b3c616bd715b3b3400
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476537"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder [Microsoft Graph](https://developer.microsoft.com/graph) i stället för Azure AD Graph API för att komma åt Azure Active Directory (Azure AD) resurser. Vårt utvecklingsarbete koncentreras nu till Microsoft Graph och inga fler förbättringar planeras för Azure AD Graph API. Det finns ett mycket begränsat antal scenarier som Azure AD Graph API fortfarande kan vara lämpligt för. Mer information finns i [Microsoft Graph eller Azure AD Graph-blogginlägget](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) och migrera Azure AD [Graph-appar till Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Den här artikeln gäller Azure AD Graph API. Liknande information om Microsoft Graph API finns [i Använda Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api).

Med API:t Azure Active Directory Graph får du programmeringsbaserad åtkomst till Azure AD via REST-API-slutpunkter. Program kan använda Azure AD Graph API för att utföra åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) på katalogdata och objekt. Azure AD Graph API stöder till exempel följande vanliga åtgärder för ett användarobjekt:

* Skapa en ny användare i en katalog
* Hämta en användares detaljerade egenskaper, till exempel deras grupper
* Uppdatera en användares egenskaper, till exempel deras plats och telefonnummer, eller ändra deras lösenord
* Kontrollera en användares gruppmedlemskap för rollbaserad åtkomst
* Inaktivera en användares konto eller ta bort det helt

Dessutom kan du utföra liknande åtgärder på andra objekt, till exempel grupper och program. Om du vill anropa Azure AD Graph API i en katalog måste ditt program vara registrerat hos Azure AD. Ditt program måste också beviljas åtkomst till Azure AD Graph API. Den här åtkomsten uppnås normalt via ett användar- eller administratörssamtyckeflöde.

Information om hur du börjar använda Azure Active Directory Graph API läser du [snabbstartsguiden för Azure AD Graph API](active-directory-graph-api-quickstart.md)eller visar den interaktiva [referensdokumentationen för Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funktioner

Azure AD Graph API innehåller följande funktioner:

* **REST API-slutpunkter:** Azure AD Graph API är en RESTful-tjänst som består av slutpunkter som används med hjälp av vanliga HTTP-begäranden. Azure AD Graph API stöder JSON-innehållstyper (XML eller Javascript Object Notation) för begäranden och svar. Mer information finns i [Azure AD Graph REST API-referens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Autentisering med Azure AD:** Varje begäran till Azure AD Graph API måste autentiseras genom att lägga till en JWT (JWT) (JWT) i auktoriseringshuvudet för begäran. Den här token hämtas genom att göra en begäran till Azure AD:s tokenslutpunkt och tillhandahålla giltiga autentiseringsuppgifter. Du kan använda klientflödet för OAuth 2.0 eller tilldelningskodsbidragflödet för att hämta en token för att anropa diagrammet. Mer information finns [i OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Rollbaserad auktorisering (RBAC):** Säkerhetsgrupper används för att utföra RBAC i Azure AD Graph API. Om du till exempel vill avgöra om en användare har åtkomst till en viss resurs kan programmet anropa åtgärden [Kontrollera gruppmedlemskap (transitiv),](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) som returnerar sant eller falskt.
* **Differentiell fråga:** Med differentiell fråga kan du spåra ändringar i en katalog mellan två tidsperioder utan att behöva göra frekventa frågor till Azure AD Graph API. Den här typen av begäran returnerar endast de ändringar som gjorts mellan den tidigare begäran om differentiella frågor och den aktuella begäran. Mer information finns i [Azure AD Graph API differential query](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Katalogtillägg:** Du kan lägga till anpassade egenskaper i katalogobjekt utan att kräva ett externt datalager. Om ditt program till exempel kräver en Skype-ID-egenskap för varje användare kan du registrera den nya egenskapen i katalogen och den kommer att vara tillgänglig för användning på alla användarobjekt. Mer information finns i [Azure AD Graph API-katalogschematillägg](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Skyddad av behörighetsområden:** Azure AD Graph API visar behörighetsomfattningar som möjliggör säker åtkomst till Azure AD-data med OAuth 2.0. Den stöder en mängd olika klientapptyper, bland annat:
  
  * användargränssnitt som ges delegerad åtkomst till data via auktorisering från den inloggade användaren (delegerad)
  * tjänst-/demonprogram som fungerar i bakgrunden utan att en inloggad användare är närvarande och använder programdefinierad rollbaserad åtkomstkontroll
    
    Både delegerade och programbehörigheter representerar en behörighet som exponeras av Azure AD Graph API och kan begäras av klientprogram via funktioner för programregistreringsbehörigheter i [Azure-portalen](https://portal.azure.com). [Azure AD Graph API-behörighetsomfattningar](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) innehåller information om vad som är tillgängligt för användning av klientprogrammet.

## <a name="scenarios"></a>Scenarier

Azure AD Graph API aktiverar många programscenarier. Följande scenarier är de vanligaste:

* **Program för branschföretag (en enda klient)**: I det här fallet arbetar en företagsutvecklare för en organisation som har en Office 365-prenumeration. Utvecklaren bygger ett webbprogram som interagerar med Azure AD för att utföra uppgifter som att tilldela en licens till en användare. Den här uppgiften kräver åtkomst till Azure AD Graph API, så utvecklaren registrerar det enda klientprogrammet i Azure AD och konfigurerar läs- och skrivbehörigheter för Azure AD Graph API. Sedan är programmet konfigurerat för att använda antingen sina egna autentiseringsuppgifter eller de som används för den inloggade användaren för att hämta en token för att anropa Azure AD Graph API.
* **Programvara som ett tjänstprogram (flera innehavare):** I det här scenariot utvecklar en oberoende programvaruleverantör (ISV) ett värdbaserat webbprogram för flera innehavare som tillhandahåller användarhanteringsfunktioner för andra organisationer som använder Azure AD. Dessa funktioner kräver åtkomst till katalogobjekt, så programmet måste anropa Azure AD Graph API. Utvecklaren registrerar programmet i Azure AD, konfigurerar det för att kräva läs- och skrivbehörighet för Azure AD Graph API och aktiverar sedan extern åtkomst så att andra organisationer kan samtycka till att använda programmet i sin katalog. När en användare i en annan organisation autentiserar till programmet för första gången visas en dialogruta med medgivande med de behörigheter som programmet begär. Beviljande av medgivande ger sedan programmet de begärda behörigheterna till Azure AD Graph API i användarens katalog. Mer information om ramverket för samtycke finns i [Översikt över ramverket för samtycke](consent-framework.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du börjar använda Azure Active Directory Graph API finns i följande avsnitt:

* [Snabbstartsguide för Azure AD Graph API](active-directory-graph-api-quickstart.md)
* [Azure AD Graph REST-dokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
