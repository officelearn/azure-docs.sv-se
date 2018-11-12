---
title: Förstå Azure Digital Twins API-autentisering | Microsoft Docs
description: 'Använd Azure Digital Twins att ansluta och autentisera till API: er'
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: f85ab05e785ea559962490b43e75b196d1602159
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016224"
---
# <a name="connect-and-authenticate-to-apis"></a>Ansluta och autentisera till API: er

Azure Digital Twins använder Azure Active Directory (Azure AD) för att autentisera användare och skydda program. Azure AD stöder autentisering för en mängd olika moderna arkitekturer. Alla baseras på branschstandardprotokollen OAuth 2.0 eller OpenID Connect. Utvecklare kan dessutom använda Azure AD för att skapa en enda klient och line-of-business (LOB) program. Utvecklare kan också använda Azure AD för att utveckla program med flera klienter.

En översikt över Azure AD, finns det [grunderna sidan](https://docs.microsoft.com/azure/active-directory/fundamentals/index) för stegvisa guider, begrepp och snabbstarter.

För att kunna integrera ett program eller en tjänst med Azure AD måste en utvecklare först registrera programmet med Azure AD. Detaljerade anvisningar och skärmdumpar i [snabbstarten](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

[Fem primära Programscenarier](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) stöds av Azure AD:

* Ensidesapplikation (SPA): en användare måste logga in på ett enkelsidigt program som skyddas av Azure AD.
* Webbläsarens webbprogram: en användare måste logga in till ett webbprogram som skyddas av Azure AD.
* Internt program till webb-API: ett internt program som körs på en telefon, surfplatta eller dator måste autentisera en användare för att hämta resurser från ett webb-API som skyddas av Azure AD.
* Webbprogram till webb-API: ett program behöver få resurser från ett webb-API som skyddas av Azure AD.
* Daemon eller ett serverprogram till webb-API: en daemon-program eller ett serverprogram med inga webbgränssnitt måste hämta resurser från ett webb-API som skyddas av Azure AD.

Autentiseringsbibliotek för Windows Azure erbjuder många sätt att hämta token för Active Directory. Mer information om de bibliotek och exempel på kod finns [i den här artikeln](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Anropa digitala Twins från ett mellannivå webb-API

När utvecklare skapar lösningar för Digital Twins, skapas vanligtvis en mellannivå program eller API: et. Appen eller API sedan anropar API: et för Digital Twins nedströms. Användare först autentisera sig till programmet medelnivån och sedan en på-token flöde används för att anropa nedströms. Anvisningar om hur du samordnar on-behalf-of-flöde finns i [den här sidan](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Du kan också visa kodexempel på [den här sidan](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Testa med Postman-klienten

För att komma igång med API: er för Digital Twins, kan du använda en klient, till exempel Postman som en API-miljö. Postman kan du skapa komplexa HTTP-begäranden snabbt. Följande steg visar hur du hämtar en Azure AD-token som behövs för att anropa digitala Twins i Postman-Användargränssnittet.


1. Gå till https://www.getpostman.com/ att ladda ned appen.
1. Följ stegen i [snabbstarten](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) att skapa ett Azure AD-program. Eller du kan återanvända en befintlig registrering. 
1. Under **nödvändiga behörigheter**, ange ”Azure Digital Twins” och välj **delegerade behörigheter**. Välj sedan **bevilja behörigheter**.
1. Öppna applikationsmanifestet och ange **oauth2AllowImplicitFlow** till true.
1. Konfigurera en svars-URL till [ https://www.getpostman.com/oauth2/callback ](https://www.getpostman.com/oauth2/callback).
1. Välj den **auktorisering** fliken **OAuth 2.0**, och välj sedan **hämta ny åtkomsttoken**.

    |**Fält**  |**Värde** |
    |---------|---------|
    | Beviljandetyp | Implicit |
    | Motringnings-URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | Auth URL | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/Authorize?Resource=0b07f429-9F4B-4714-9392-cc5e8e80c8b0 |
    | Klientorganisations-ID | Använda program-ID för Azure AD-app som har skapats eller ändrat syfte från steg 2. |
    | Omfång | Lämna tomt. |
    | Status | Lämna tomt. |
    | Klientautentisering | Skicka som en rubrik för grundläggande autentisering. |

1. Välj **begära Token**.

    >[!NOTE]
    >Om du får felmeddelandet ”Det gick inte att slutföra OAuth 2” kan du prova följande:
    > * Stäng Postman och öppna den igen och försök igen.
   
1. Bläddra nedåt och välj **Använd Token**.

## <a name="next-steps"></a>Nästa steg

Läs om Azure Digital Twins säkerhet [skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md).
