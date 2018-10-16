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
ms.openlocfilehash: 1d5b1869428cec6bf80b8518485f685e38ad5997
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324313"
---
# <a name="connect-and-authenticate-to-apis"></a>Ansluta och autentisera till API: er

Azure Digital Twins använder Azure Active Directory (Azure AD) för att autentisera användare och skydda program.

Om du inte är bekant med Azure AD, mer information finns i den [utvecklarguide](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-developers-guide). Autentiseringsbibliotek för Windows Azure erbjuder många sätt att hämta token för Active Directory. För en djupdykning i biblioteket, ta en titt [här](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

Den här artikeln ger en översikt över två scenarier: ett produktionsscenario med mellannivå-API och autentisering i klientprogrammet Postman för snabb start och testning.

## <a name="authentication-in-production"></a>Autentisering i produktion

Lösningar för utvecklare har två sätt att ansluta till Digital Twins.  Lösningar för utvecklare kan ansluta till Azure Digital Twins på följande sätt:

* De kan skapa ett klientprogram eller en mellannivå-API. Klientappar kräver att användarna autentisera och sedan använda den [OAuth 2.0 Behalf](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) security flödet att anropa en underordnad API.
* Skapa eller använda ett befintligt Azure AD-program. Visa dokumentationen [här](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad).
    1. Ange den **inloggnings- och omdirigerings-URI: er** (vid behov).
    1. I programmet manifest set `oauth2AllowImplicitFlow` till true.
    1. I **nödvändiga behörigheter**, lägga till Digital Twins genom att söka ”Azure Digital Twins”. Välj **delegerade behörigheter läs-/ skrivåtkomst** och klicka på den **bevilja behörigheter** knappen.

Detaljerade anvisningar om hur du samordnar on-behalf-of-flöde finns [den här sidan](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Du kan också visa kodexempel [här](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="test-with-the-postman-client"></a>Testa med Postman-klienten

1. Följ de första stegen ovan för att skapa (eller ändra) ett Azure Active Directory-program. Slutligen anger `oauth2AllowImplicitFlow` till true i appmanifestet och ge behörigheter till ”Azure Digital Twins”.
1. Ange en svars-url [ https://www.getpostman.com/oauth2/callback ](https://www.getpostman.com/oauth2/callback).
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

    >[!TIP]
    >Om du får felmeddelandet ”Det gick inte att slutföra OAuth 2”, prova något av följande:
    > * Stäng Postman och öppna den igen och försök igen.
    > * Ta bort den hemliga nyckeln i din app, skapa en ny och hyrestagare värdet i formuläret ovan.

1. Rulla nedåt och klicka på **Använd Token**.

## <a name="next-steps"></a>Nästa steg

Läs om Azure Digital Twins säkerhet [skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md).
