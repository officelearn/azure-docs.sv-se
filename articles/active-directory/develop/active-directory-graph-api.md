---
title: Azure Active Directory Graph API | Microsoft Docs
description: En översikt och Snabbstart guide för Azure AD Graph-API som ger programmatisk åtkomst till Azure AD via REST API-slutpunkter.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev
ms.openlocfilehash: 2c95e19fe47e23cda6d14fc047fbe6c30100d78e
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56097772"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder [Microsoft Graph](https://developer.microsoft.com/graph/) i stället för Azure AD Graph API för att komma åt resurser i Azure Active Directory. Vårt utvecklingsarbete koncentreras nu till Microsoft Graph och inga fler förbättringar planeras för Azure AD Graph API. Det finns ett begränsat antal scenarier där Azure AD Graph API fortfarande kan vara lämpligt. Mer information finns i blogginlägget [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) (Microsoft Graph eller Azure AD Graph) i Office Dev Center.

Den här artikeln gäller för Azure AD Graph API. Liknande information relaterad till Microsoft Graph API finns i [använder Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api). 

Azure Active Directory Graph API ger Programmeringsåtkomst till Azure AD via REST API-slutpunkter. Program kan använda Azure AD Graph API för att utföra skapa, läsa, uppdatera och ta bort (CRUD)-åtgärder på katalogdata och objekt. Till exempel stöder Azure AD Graph API följande vanliga åtgärder för ett användarobjekt:

* Skapa en ny användare i en katalog
* Hämta en användares detaljerade egenskaper, till exempel deras grupper
* Uppdatera egenskaperna för en användare, till exempel sina platser och telefonnummer, eller ändra sina lösenord
* Kontrollera en användares gruppmedlemskap för rollbaserad åtkomst
* Inaktivera en användares konto eller ta bort helt

Du kan dessutom utföra liknande åtgärder på andra objekt, till exempel grupper och program. För att anropa Azure AD Graph API i en katalog, måste programmet registreras med Azure AD. Programmet måste också ha åtkomst till Azure AD Graph API. Den här åtkomsten uppnås normalt via något godkännandeflöde användare eller administratör.

Om du vill börja använda Azure Active Directory Graph API, se den [Snabbstartsguide för Azure AD Graph API](active-directory-graph-api-quickstart.md), eller visa den [interaktiva Azure AD Graph API-referensdokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funktioner

Azure AD Graph API: et tillhandahåller följande funktioner:

* **REST API-slutpunkter**: Azure AD Graph API är en RESTful-tjänst som består av slutpunkter som nås med hjälp av standard HTTP-begäranden. Azure AD Graph API har stöd för XML- eller Javascript Object Notation (JSON) innehållstyper för begäranden och svar. Mer information finns i [Azure AD Graph REST API-referens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Autentisering med Azure AD**: Varje begäran till Azure AD Graph API: et måste autentiseras genom att lägga till en JSON Web Token (JWT) i auktoriseringshuvudet för begäran. Denna token förvärvas genom gör en begäran till tokenslutpunkten för Azure AD och ange giltiga autentiseringsuppgifter. Du kan använda OAuth 2.0 flödet eller Auktoriseringskoden bevilja flödet för att hämta en token för att anropa Graph. Mer information [OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Rollbaserad auktorisering (RBAC)**: Säkerhetsgrupper används för att utföra RBAC i Azure AD Graph API. Exempel: Om du vill fastställa om en användare har åtkomst till en specifik resurs, programmet kan anropa den [kontrollera medlemskap (transitiva)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) åtgärd, som returnerar SANT eller FALSKT.
* **Differentiell fråga**: Differentiell fråga kan du spåra ändringar i en katalog mellan två tidsperioder utan att göra frekventa förfrågningar till Azure AD Graph API. Den här typen av begäran returnerar bara de ändringar som gjorts mellan tidigare differentiell fråga och den aktuella begäran. Mer information finns i [Azure AD Graph API differentiell fråga](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Katalogtillägg**: Du kan lägga till anpassade egenskaper katalogobjekt utan ett externa datalager. Om programmet kräver en Skype-ID-egenskap för varje användare kan du registrera den nya egenskapen i katalogen och den blir tillgänglig för användning på alla användarobjekt. Mer information finns i [Azure AD Graph API directory-schemautökningar](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Skyddas av behörighetsomfattningar**: Azure AD Graph API exponerar behörighetsomfattningar som ger säker åtkomst till Azure AD-data med hjälp av OAuth 2.0. Det stöder en mängd olika apptyper som klienten, bland annat:
  
  * användargränssnitt som ges delegerad åtkomst till data via auktorisering från (delegerad) inloggad användare
  * tjänsten/daemon-program som fungerar i bakgrunden utan att vara närvarande inloggade användaren och använda programdefinierade rollbaserad åtkomstkontroll
    
    Båda delegerade och programbehörigheter representerar en behörighet som exponeras av Azure AD Graph API och kan begäras av klientprogram via programfunktioner registrering behörigheter i den [Azure-portalen](https://portal.azure.com). [Behörighetsomfattning för Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) innehåller information om vad som är tillgängligt för användning av ditt klientprogram.

## <a name="scenarios"></a>Scenarier

Azure AD Graph API gör det möjligt för många scenarier för programmet. Följande scenarion är de vanligaste:

* **Affärsprogram (enskild klient)**: I det här scenariot fungerar företagsutvecklare för en organisation som har en Office 365-prenumeration. Utvecklaren är att skapa ett webbprogram som interagerar med Azure AD för att utföra uppgifter som att tilldela en licens till en användare. Den här uppgiften kräver åtkomst till Azure AD Graph API, så att utvecklare registreras enskild klient-programmet i Azure AD och konfigurerar Läs- och skrivbehörighet för Azure AD Graph API. Programmet konfigureras sedan för att använda sina egna autentiseringsuppgifter eller den av den för närvarande inloggning för att hämta en token för att anropa Azure AD Graph API.
* **Programvara som en tjänst (flera innehavare)**: I det här scenariot en oberoende programvaruleverantör (ISV) utvecklingen av ett program för värdbaserade webbprogram för flera innehavare som tillhandahåller funktioner för användarhantering för andra organisationer som använder Azure AD. De här funktionerna kräver åtkomst till katalogobjekt, så programmet måste anropa Azure AD Graph API. Utvecklaren registrerar programmet i Azure AD konfigureras för kräver Läs- och skrivbehörighet för Azure AD Graph API och aktiverar sedan extern åtkomst så att andra organisationer kan godkänna att använda programmet i sin katalog. När en användare i en annan organisation autentiseras till programmet för första gången, visas en dialogruta för medgivande med de behörigheter som programmet begär. Ge ditt medgivande sedan ger programmet begärda de behörigheter till Azure AD Graph API i användarens katalog. Mer information om ramverket för medgivande finns [översikt över ramverket för medgivande](consent-framework.md).

## <a name="next-steps"></a>Nästa steg

Om du vill börja använda Azure Active Directory Graph API, finns i följande avsnitt:

* [Snabbstartsguide för Azure AD Graph API](active-directory-graph-api-quickstart.md)
* [Dokumentation om Azure AD Graph REST](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
