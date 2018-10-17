---
title: Förstå Azure Digital Twins API-autentisering | Microsoft Docs
description: 'Med hjälp av Azure Digital Twins ansluta och autentisera till API: er'
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: ef7838c41bb479da273123c2eb3def8e12802390
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351310"
---
# <a name="connect-and-authenticate-to-apis"></a>Ansluta och autentisera till API: er

Azure Digital Twins använder Azure Active Directory (Azure AD) för att autentisera användare och skydda program. Azure AD stöder autentisering för en mängd olika moderna arkitekturer, alla utifrån branschstandardprotokoll OAuth 2.0 eller OpenID Connect. Dessutom kan kan Azure AD utvecklare som skapar såväl enda klient, line-of-business (LOB) program, som utvecklare som vill utveckla program för flera innehavare.

En översikt över Azure AD finns i [grunderna sidan](https://docs.microsoft.com/azure/active-directory/fundamentals/index) för stegvisa guider, begrepp och snabbstarter.

För att kunna integrera ett program eller en tjänst med Azure AD måste en utvecklare först registrera programmet med Azure AD. För detaljerade anvisningar och skärmdumpar visa anvisningarna [här](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

Det här är den [fem primära Programscenarier](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) stöds av Azure AD:

* Ensidesapplikation (SPA): en användare måste logga in på ett enkelsidigt program som skyddas av Azure AD.
* Webbläsarens webbprogram: en användare måste logga in till ett webbprogram som skyddas av Azure AD.
* Internt program till webb-API: ett internt program som körs på en telefon, surfplatta eller dator måste autentisera en användare för att hämta resurser från ett webb-API som skyddas av Azure AD.
* Webbprogram till webb-API: ett program behöver få resurser från ett webb-API som skyddas av Azure AD.
* Daemon eller ett serverprogram till webb-API: en daemon-program eller ett serverprogram utan användargränssnitt för web måste hämta resurser från ett webb-API som skyddas av Azure AD.

Autentiseringsbibliotek för Windows Azure erbjuder många sätt att hämta token för Active Directory. För en djupdykning i biblioteket samt kod exempel ta en titt [här](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="calling-digital-twins-from-a-middle-tier-web-api"></a>Anropa digitala Twins från ett mellannivå webb-API

När utforma digitala Twins lösningar utvecklare ofta kan du välja att skapa en mellannivå program eller en API som anropar API: et för Digital Twins nedströms. Användare skulle först autentisera sig till programmet medelnivån och sedan en på-token flöde ska användas när du anropar nedströms. Detaljerade anvisningar om hur du samordnar on-behalf-of-flöde finns [den här sidan](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Du kan också visa kodexempel [här](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Testa med Postman-klienten

Du kan använda en klient, till exempel Postman som en API-miljö för att komma igång med API: er för Digital Twins. Postman kan du skapa komplexa HTTP-begäranden snabbt. Anvisningarna nedan fokuserar på hur du skaffar en Azure AD-token som krävs för att anropa digitala Twins i Postman-Användargränssnittet.


1. Gå till https://www.getpostman.com/ hämta appen
1. Följ stegen [här](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) att skapa ett Azure Active Directory-program (eller du kan välja att återanvända en befintlig registrering). 
1. Lägg till ”Azure Digital Twins” under behörigheter som krävs och välj delegerade behörigheter. Glöm inte att klicka på bevilja behörigheter för att slutföra.
1. Konfigurera en svars-url till [ https://www.getpostman.com/oauth2/callback ](https://www.getpostman.com/oauth2/callback).
1. Välj den **auktorisering fliken**, klicka på **OAuth 2.0**, och välj **hämta ny åtkomsttoken**.

    |**Fält**  |**Värde** |
    |---------|---------|
    | Beviljandetyp | Implicit |
    | Motringnings-URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | Auth URL | [https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0](https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0)
    | Klient-ID | Använda program-Id för Azure AD-app som har skapats eller ändrat syfte från steg 1 |
    | Omfång | Lämna tomt |
    | Status | Lämna tomt |
    | Klientautentisering | Skicka som grundläggande autentisering rubrik |

1. Klicka på **begära Token**.

    >[!NOTE]
    >Om du får felmeddelandet ”Det gick inte att slutföra OAuth 2”, kan du prova följande:
    > * Stäng Postman och öppna den igen och försök igen.
   
1. Rulla nedåt och klicka på **Använd Token**.

## <a name="next-steps"></a>Nästa steg

Läs om Azure Digital Twins säkerhet [skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md).
