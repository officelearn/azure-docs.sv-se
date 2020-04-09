---
title: Microsoft Graph-API
description: Microsoft Graph API är ett RESTful-webb-API som gör att du kan komma åt Microsoft Cloud-tjänstresurser.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 67dbf696903e7a930d75762deb00ad58ed1a4f69
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886474"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph-API

Microsoft Graph API är ett RESTful-webb-API som gör att du kan komma åt Microsoft Cloud-tjänstresurser. När du har registrerat appen och hämtat autentiseringstoken för en användare eller tjänst kan du göra förfrågningar till Microsoft Graph API. Mer information finns i [Översikt över Microsoft Graph](https://docs.microsoft.com/graph/overview).

I Microsoft Graph visas REST-API:er och klientbibliotek för åtkomst till data på följande Microsoft 365-tjänster:
- Office 365-tjänster: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner och SharePoint
- Enterprise Mobility and Security services: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager och Intune
- Windows 10-tjänster: aktiviteter, enheter, meddelanden
- Dynamics 365 Business Central

## <a name="versions"></a>Versioner

Microsoft Graph stöder för närvarande två versioner: v1.0 och beta. V1.0-versionen innehåller allmänt tillgängliga API:er. Använd v1.0-versionen för alla produktionsappar. Betaversionen innehåller API:er som för närvarande förhandsgranskas. Eftersom vi kan införa bryta ändringar i våra beta-API: er, rekommenderar vi att du använder betaversionen endast för att testa appar som är under utveckling; använd inte beta-API:er i dina produktionsappar. Mer information finns i [Version, support och brytning av ändringsprinciper för Microsoft Graph](https://docs.microsoft.com/graph/versioning-and-support).

Om du vill börja använda beta-API:erna läser du [Microsoft Graph betaslutpunktsreferens](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta)

Om du vill börja använda v1.0-API:erna läser du [Microsoft Graph REST API v1.0-referens](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>Kom igång

Om du vill läsa från eller skriva till en resurs, till exempel en användare eller ett e-postmeddelande, skapar du en begäran som ser ut så här:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Mer information om elementen i den konstruerade begäran finns i [Använda Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api)

Snabbstartsexempel finns tillgängliga för att visa hur du kommer åt kraften i Microsoft Graph API. Exemplen som är tillgängliga får två tjänster med en autentisering: Microsoft-konto och Outlook. Varje snabbstart får åtkomst till information från Microsoft-kontoanvändares profiler och visar händelser från sin kalender.
Snabbstarterna innebär fyra steg:
- Välj din plattform
- Hämta ditt app-ID (klient-ID)
- Skapa exemplet
- Logga in och visa händelser i kalendern

När du är klar med snabbstarten har du en app som är redo att köras. Mer information finns i [vanliga frågor och svar om snabbstart](https://docs.microsoft.com/graph/quick-start-faq)i Microsoft Graph . Mer om du vill komma igång med exemplen finns i [Microsoft Graph QuickStart](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Verktyg

Microsoft Graph Explorer är ett webbaserat verktyg som du kan använda för att skapa och testa begäranden med hjälp av Microsoft Graph API:er. Du kan komma åt `https://developer.microsoft.com/graph/graph-explorer`Microsoft Graph Explorer på: .

Postman är ett verktyg som du också kan använda för att skapa och testa begäranden med hjälp av Microsoft Graph API:er. Du kan ladda ner `https://www.getpostman.com/`Postman på: . Om du vill interagera med Microsoft Graph i Postman använder du Microsoft Graph-samlingen i Postman. Mer information finns i [Använda Postman med Microsoft Graph API](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta).
