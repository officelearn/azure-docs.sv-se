---
title: Förstå Azure Digital Twins API-autentisering | Microsoft Docs
description: 'Använd Azure Digital Twins att ansluta och autentisera till API: er'
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: 4ea4479d77e06940bed50859341952ffbcbbda46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533838"
---
# <a name="connect-and-authenticate-to-apis"></a>Ansluta och autentisera till API: er

Azure Digital Twins använder Azure Active Directory (Azure AD) för att autentisera användare och skydda program. Azure AD stöder autentisering för en mängd olika moderna arkitekturer. Alla baseras på branschstandardprotokollen OAuth 2.0 eller OpenID Connect. Utvecklare kan dessutom använda Azure AD för att skapa en enda klient och line-of-business (LOB) program. Utvecklare kan också använda Azure AD för att utveckla program med flera klienter.

En översikt över Azure AD, finns det [grunderna sidan](https://docs.microsoft.com/azure/active-directory/fundamentals/index) för stegvisa guider, begrepp och snabbstarter.

För att kunna integrera ett program eller en tjänst med Azure AD måste en utvecklare först registrera programmet med Azure AD. Detaljerade anvisningar och skärmdumpar i [snabbstarten](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

[Fem primära Programscenarier](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) stöds av Azure AD:

* Ensidesapplikation (SPA): En användare måste logga in på ett enkelsidigt program som skyddas av Azure AD.
* Webbläsare till webbprogram: En användare måste logga in till ett webbprogram som skyddas av Azure AD.
* Internt program till webb-API: Ett internt program som körs på en telefon, surfplatta eller dator måste autentisera en användare för att hämta resurser från ett webb-API som skyddas av Azure AD.
* Webbprogram till webb-API: Ett webbprogram måste hämta resurser från ett webb-API som skyddas av Azure AD.
* Daemon eller server-program till webb-API: En daemon-program eller ett serverprogram med ingen webbplats måste Användargränssnittet att hämta resurser från ett webb-API som skyddas av Azure AD.

Autentiseringsbibliotek för Windows Azure erbjuder många sätt att hämta token för Active Directory. Mer information om de bibliotek och exempel på kod finns [i den här artikeln](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Anropa digitala Twins från ett mellannivå webb-API

När utvecklare skapar lösningar för Digital Twins, skapas vanligtvis en mellannivå program eller API: et. Appen eller API sedan anropar API: et för Digital Twins nedströms. För att stödja den här standarden web-lösningsarkitekturen, se till att användare första:

1. Autentisera med mellannivå-program

1. Det krävs en OAuth 2.0-Behalf-token under autentiseringen

1. Förvärvade token används sedan för att autentisera med eller anropa API: er som ytterligare nedströms använder On-Behalf-Of-flöde

Anvisningar om hur du samordnar on-behalf-of-flöde finns i [flödet för OAuth 2.0 Behalf](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Du kan också visa kodexempel i [anropa ett webb-API som är underordnade](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera och testa Azure Digital Twins med hjälp av OAuth 2.0 implicit beviljande av flow, läsa [konfigurera Postman](./how-to-configure-postman.md).

Läs om Azure Digital Twins säkerhet [skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md).