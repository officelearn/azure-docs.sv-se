---
title: Azure Active Directory Graph API | Microsoft Docs
description: "En översikt och quickstart handbok för Graph API som ger programmatisk åtkomst till Azure AD via REST API-slutpunkter."
services: active-directory
documentationcenter: 
author: viv-liu
manager: mtillman
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: viviali
ms.custom: aaddev
ms.openlocfilehash: 815b9f75864ba3a08f623af12417391fba430f7d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API
> [!IMPORTANT]
> Vi rekommenderar starkt att du använder [Microsoft Graph](https://graph.microsoft.io/) i stället för Azure AD Graph API för att komma åt resurser i Azure Active Directory. Vårt utvecklingsarbete koncentreras nu till Microsoft Graph och inga fler förbättringar planeras för Azure AD Graph API. Det finns ett begränsat antal scenarier där Azure AD Graph API fortfarande kan vara lämpligt. Mer information finns i blogginlägget [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) (Microsoft Graph eller Azure AD Graph) i Office Dev Center.
> 
> 

Azure Active Directory Graph API ger programmatisk åtkomst till Azure AD via REST API-slutpunkter. Program kan använda Graph API för att utföra skapa, läsa, uppdatera och ta bort CRUD-åtgärder i katalogdata och objekt. Till exempel stöder Graph API följande vanliga åtgärder för ett användarobjekt:

* Skapa en ny användare i en katalog
* Hämta en användares detaljerade egenskaper, till exempel deras grupper
* Uppdatera egenskaperna för en användare, till exempel deras plats och telefonnummer, eller ändra sina lösenord
* Kontrollera en användares gruppmedlemskap för rollbaserad åtkomst
* Inaktiverar ett användarkonto eller ta bort den helt

Förutom användarobjekt, kan du utföra liknande åtgärder på andra objekt, till exempel grupper och program. Programmet måste registreras med Azure AD för att anropa Graph API för en katalog och konfigureras för att tillåta åtkomst till katalogen. Detta uppnås normalt flöde en användare eller administratör medgivande.

Om du vill börja använda Azure Active Directory Graph API, finns det [Snabbstartsguide för Graph API](active-directory-graph-api-quickstart.md), eller visa den [interaktiva Graph API-referensdokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funktioner
Graph API innehåller följande funktioner:

* **REST API-slutpunkter**: Graph API: N är en RESTful-tjänst består av slutpunkter som hämtas med hjälp av HTTP-begäranden som standard. Graph API stöder XML eller Javascript Object Notation (JSON) innehållstyper för begäranden och -svar. Mer information finns i [Azure AD Graph REST API-referens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Autentisering med Azure AD**: varje begäran för Graph API måste autentiseras genom att lägga till en JSON-Webbtoken (JWT) i Authorization-huvud i begäran. Denna token förvärvas genom att göra en begäran till Azure AD-token för slutpunkt och anger giltiga autentiseringsuppgifter. Du kan använda OAuth 2.0-klientautentiseringsuppgifter eller det flöde beviljat med auktoriseringskod att hämta en token för att anropa diagrammet. Mer information [OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Rollbaserad auktorisering (RBAC)**: säkerhetsgrupper används för att utföra RBAC i Graph API. Om du vill fastställa om en användare har åtkomst till en specifik resurs, programmet kan anropa exempelvis den [Kontrollera gruppmedlemskap (transitiva)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) åtgärd som returnerar true eller false.
* **Differentiella frågan**: Om du vill söka efter ändringar i en katalog mellan två tidsperioder utan att behöva göra ofta frågor för Graph API kan du göra en differentiell fråga. Den här typen av begäran returnerar bara de ändringar som gjorts mellan tidigare differentiell fråga och den aktuella begäranden. Mer information finns i [Azure AD Graph API differentiell frågan](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Katalogtillägg**: Om du utvecklar ett program som behövs för att läsa eller skriva unik egenskaper för katalogobjekt kan du registrera och använda tillägget värden med hjälp av Graph API. Om ditt program kräver en Skype-ID-egenskap för varje användare, kan du registrera den nya egenskapen i katalogen och den blir tillgänglig på alla användarobjekt. Mer information finns i [Azure AD Graph API Directory-schemautökningar](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Skyddas av behörighetsomfattningen**: AAD Graph API exponerar behörighetsomfattningen som aktiverar secure/godkänt åtkomst till AAD data och stöder många olika apptyper som klienten, inklusive:
  
  * de med ett användargränssnitt som ges begränsad åtkomst till data via tillstånd från (delegerad) inloggade användaren
  * de som använder definiera program – rollbaserad åtkomstkontroll, till exempel service-daemon klienter (app-roller)
    
    Både delegerade och app rollen behörighetsomfattningen representerar privilegium som exponeras av Graph-API och kan begäras av klientprogram via behörigheter för registrering av program [funktioner i Azure portal](https://portal.azure.com). Klienter kan verifiera behörighet scope beviljas till dem genom att inspektera omfång (”scp”)-anspråk som togs emot i åtkomsttoken för delegerade behörigheter och roller (”roller”) begära för app-rollbehörigheter. Lär dig mer om [Azure AD Graph API Behörighetsomfattningen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).

## <a name="scenarios"></a>Scenarier
Graph API kan många scenarier för programmet. Följande scenarier är de vanligaste:

* **Affärsprogram (enstaka klient)**: I det här scenariot fungerar en enterprise-utvecklare för en organisation som har en Office 365-prenumeration. Utvecklare skapar ett webbprogram som interagerar med Azure AD för att utföra uppgifter sådana tilldela en licens till en användare. Den här aktiviteten kräver åtkomst till Graph-API så developer registren enstaka program i Azure AD-klient och konfigurerar Läs- och skrivbehörighet för Graph API. Sedan är programmet konfigurerat för att använda sina egna autentiseringsuppgifter eller den av den aktuella inloggningen för att hämta en token för att anropa Graph API.
* **Programvara som en tjänst (flera innehavare)**: I det här scenariot en oberoende programvaruleverantör (ISV) utveckla värdbaserade flera innehavare webbprogram som ger användare hanteringsfunktioner för andra organisationer som använder Azure AD. Dessa funktioner kräver åtkomst till katalogobjekt och så programmet behöver anropa Graph API. Utvecklaren registrerar programmet i Azure AD, konfigureras för kräver Läs- och skrivbehörighet för Graph API och sedan aktiverar extern åtkomst så att andra organisationer kan samtycker till att använda programmet i sina kataloger. När en användare i en annan organisation autentiseras till programmet för första gången, visas en medgivande dialogruta med de behörigheter som programmet begär.  Ge ditt medgivande sedan ger programmet begärda de behörigheter för Graph API i katalogen för den användaren. Läs mer på medgivande framework [översikt av Framework medgivande](active-directory-integrating-applications.md).

## <a name="see-also"></a>Se även
[Snabbstartsguide för Azure AD Graph API](active-directory-graph-api-quickstart.md)

[AD Graph REST-dokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Utvecklarhandbok för Azure Active Directory](active-directory-developers-guide.md)

