---
title: Azure Active Directory Graph API | Microsoft Docs
description: En översikt och quickstart handbok för Azure AD Graph API som ger programmatisk åtkomst till Azure AD via REST API-slutpunkter.
services: active-directory
documentationcenter: ''
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
ms.openlocfilehash: 1d1cfed6782ae2ea93f350aa11993d257800ce7b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API
> [!IMPORTANT]
> Vi rekommenderar starkt att du använder [Microsoft Graph](https://graph.microsoft.io/) i stället för Azure AD Graph API för att komma åt resurser i Azure Active Directory. Vårt utvecklingsarbete koncentreras nu till Microsoft Graph och inga fler förbättringar planeras för Azure AD Graph API. Det finns ett begränsat antal scenarier där Azure AD Graph API fortfarande kan vara lämpligt. Mer information finns i blogginlägget [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) (Microsoft Graph eller Azure AD Graph) i Office Dev Center.
> 
> 

Azure Active Directory Graph API ger programmatisk åtkomst till Azure AD via REST API-slutpunkter. Program kan använda Azure AD Graph API för att utföra skapa, läsa, uppdatera och ta bort CRUD-åtgärder i katalogdata och objekt. Azure AD Graph API stöder till exempel följande vanliga åtgärder för ett användarobjekt:

* Skapa en ny användare i en katalog
* Hämta en användares detaljerade egenskaper, till exempel deras grupper
* Uppdatera egenskaperna för en användare, till exempel deras plats och telefonnummer, eller ändra sina lösenord
* Kontrollera en användares gruppmedlemskap för rollbaserad åtkomst
* Inaktiverar ett användarkonto eller ta bort den helt

Dessutom kan du utföra samma åtgärder på andra objekt, till exempel grupper och program. Programmet måste registreras med Azure AD för att anropa Azure AD Graph API för en katalog. Programmet måste också ha åtkomst till Azure AD Graph API. Denna åtkomst uppnås normalt flöde en användare eller administratör medgivande.

Om du vill börja använda Azure Active Directory Graph API, finns det [Azure AD Graph API Snabbstartsguide](active-directory-graph-api-quickstart.md), eller visa den [interaktiva Azure AD Graph API-referensdokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funktioner
Azure AD Graph API innehåller följande funktioner:

* **REST API-slutpunkter**: Azure AD Graph API är en RESTful-tjänst består av slutpunkter som hämtas med hjälp av HTTP-begäranden som standard. Azure AD Graph API stöder XML eller Javascript Object Notation (JSON) innehållstyper för begäranden och -svar. Mer information finns i [Azure AD Graph REST API-referens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Autentisering med Azure AD**: alla begäranden till Azure AD Graph API måste autentiseras genom att lägga till en JSON-Webbtoken (JWT) i Authorization-huvud i begäran. Denna token förvärvas genom att göra en begäran till Azure AD-token för slutpunkt och anger giltiga autentiseringsuppgifter. Du kan använda OAuth 2.0-klientautentiseringsuppgifter eller det flöde beviljat med auktoriseringskod att hämta en token för att anropa diagrammet. Mer information [OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Rollbaserad auktorisering (RBAC)**: säkerhetsgrupper används för att utföra RBAC i Azure AD Graph API. Om du vill fastställa om en användare har åtkomst till en specifik resurs, programmet kan anropa exempelvis den [Kontrollera gruppmedlemskap (transitiva)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) åtgärd som returnerar true eller false.
* **Differentiella frågan**: differentiell frågan kan du spåra ändringar i en katalog mellan två tidsperioder utan att behöva göra ofta frågor i Azure AD Graph API. Den här typen av begäran returnerar bara de ändringar som gjorts mellan tidigare differentiell fråga och den aktuella begäranden. Mer information finns i [Azure AD Graph API differentiell frågan](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Katalogtillägg**: ou kan att lägga till anpassade egenskaper till katalogobjekt utan ett externt dataarkiv. Om ditt program kräver en Skype-ID-egenskap för varje användare, kan du registrera den nya egenskapen i katalogen och den blir tillgänglig för användning på alla användarobjekt. Mer information finns i [Azure AD Graph API Directory-schemautökningar](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Skyddas av behörighetsomfattningen**: Azure AD Graph API exponerar behörighetsomfattningen som ger säker åtkomst till Azure AD-data med hjälp av OAuth 2.0. Den stöder en mängd olika apptyper som klienten, inklusive:
  
  * användargränssnitt som ges delegerad åtkomst till data via tillstånd från (delegerad) inloggade användaren
  * Kontrollera tjänsten-daemon program att åtgärden i bakgrunden utan en inloggad användare som finns på och använda programdefinierade rollbaserad åtkomst
    
    Både delegerade och programbehörigheter representerar privilegium som exponeras av Azure AD Graph-API och kan begäras av klientprogram via tillämpningsfunktioner registrering behörigheter i den [Azure-portalen](https://portal.azure.com). [Azure AD Graph API Behörighetsomfattningen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) innehåller information om vad som är tillgängligt för användning av klientprogrammet.

## <a name="scenarios"></a>Scenarier
Azure AD Graph API kan många scenarier för programmet. Följande scenarier är de vanligaste:

* **Affärsprogram (enstaka klient)**: I det här scenariot fungerar en enterprise-utvecklare för en organisation som har en Office 365-prenumeration. Utvecklare skapar ett webbprogram som interagerar med Azure AD för att utföra uppgifter sådana tilldela en licens till en användare. Den här aktiviteten kräver åtkomst till Azure AD Graph API, så att utvecklaren registreras det enda klient-programmet i Azure AD och konfigurerar Läs- och skrivbehörighet för Azure AD Graph API. Sedan är programmet konfigurerat för att använda sina egna autentiseringsuppgifter eller den av den aktuella inloggningen för att hämta en token för att anropa Azure AD Graph API.
* **Programvara som en tjänst (flera innehavare)**: I det här scenariot en oberoende programvaruleverantör (ISV) utveckla värdbaserade flera innehavare webbprogram som ger användare hanteringsfunktioner för andra organisationer som använder Azure AD. Dessa funktioner kräver åtkomst till katalogobjekt och så programmet behöver anropa Azure AD Graph API. Utvecklaren registrerar programmet i Azure AD, konfigureras för kräver Läs- och skrivbehörighet för Azure AD Graph API och sedan aktiverar extern åtkomst så att andra organisationer kan samtycker till att använda programmet i sina kataloger. När en användare i en annan organisation autentiseras till programmet för första gången, visas en medgivande dialogruta med de behörigheter som programmet begär.  Ge ditt medgivande sedan ger programmet begärda de behörigheter till Azure AD Graph API i katalogen för den användaren. Läs mer på medgivande framework [översikt av Framework medgivande](active-directory-integrating-applications.md).

## <a name="see-also"></a>Se även
[Snabbstartsguide för Azure AD Graph API](active-directory-graph-api-quickstart.md)

[Azure AD Graph REST-dokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Utvecklarhandbok för Azure Active Directory](active-directory-developers-guide.md)

