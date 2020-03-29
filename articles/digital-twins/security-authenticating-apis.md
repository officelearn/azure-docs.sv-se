---
title: Förstå API-autentisering - Azure Digital Twins | Microsoft-dokument
description: Lär dig hur du ansluter till och autentiserar med API:er med Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: d950d41186d578702343645875dd7c565002d5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513015"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Ansluta till och autentisera med API:er

Azure Digital Twins använder Azure Active Directory (Azure AD) för att autentisera användare och skydda program. Azure AD stöder autentisering för en mängd moderna arkitekturer. Alla är baserade på branschstandardprotokollen OAuth 2.0 eller OpenID Connect. Dessutom kan utvecklare använda Azure AD för att skapa LOB-program (Single-Tenant and line-of-business). Utvecklare kan också använda Azure AD för att utveckla [program med flera trogna .](how-to-multitenant-applications.md)

En översikt över Azure AD finns på [grunderna för](https://docs.microsoft.com/azure/active-directory/fundamentals/) steg-för-steg-guider, koncept och snabbstarter.

> [!TIP]
> Följ [självstudien för](tutorial-facilities-setup.md) att konfigurera och köra en exempelapp för Azure Digital Twins.

För att kunna integrera ett program eller en tjänst med Azure AD måste en utvecklare först registrera programmet med Azure AD. För detaljerade instruktioner och skärmdumpar, läs [denna snabbstart](../active-directory/develop/quickstart-register-app.md).

[Fem primära programscenarier](../active-directory/develop/v2-app-types.md) stöds av Azure AD:

* Spa (Single page Application): En användare måste logga in på ett ensidigt program som skyddas av Azure AD.
* Webbläsare till webbprogram: En användare måste logga in på ett webbprogram som skyddas av Azure AD.
* Native-program till webb-API: Ett inbyggt program som körs på en telefon, surfplatta eller dator måste autentisera en användare för att hämta resurser från ett webb-API som skyddas av Azure AD.
* Webbprogram till webb-API: Ett webbprogram måste hämta resurser från ett webb-API som skyddas av Azure AD.
* Daemon eller serverprogram till webb-API: Ett daemonprogram eller ett serverprogram utan webbgränssnitt behöver hämta resurser från ett webb-API som skyddas av Azure AD.

> [!IMPORTANT]
> Azure Digital Twins stöder båda följande autentiseringsbibliotek:
> * Det nyare [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Anropa Digitala tvillingar från ett webb-API på mellannivå

När utvecklare arkitekt Digital Twins lösningar, skapar de vanligtvis en mellannivå program eller API. Appen eller API:et anropar sedan Digital Twins API nedströms. Om du vill stödja den här standardarkitekturen för webblösningar kontrollerar du att användarna först:

1. Autentisera med programmet på mellannivå

1. En OAuth 2.0 On-Behalf-Of-token förvärvas under autentisering

1. Den förvärvade token används sedan för att autentisera med eller anropa API:er som är längre nedströms med flödet On-Behalf-Of

Instruktioner om hur du orkestrerar flödet för den skull finns i [OAuth 2.0 on-Behalf-Of-flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Du kan också visa kodexempel i [Anropa ett nedströms webb-API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera och testa Azure Digital Twins med oauth 2.0 implicita bidragsflödet läser [du Konfigurera Postman](./how-to-configure-postman.md).

Om du vill veta mer om Azure Digital Twins säkerhet läser du [Skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md).
