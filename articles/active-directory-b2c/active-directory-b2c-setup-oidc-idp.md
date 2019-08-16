---
title: Konfigurera registrering och inloggning med OpenID Connect-Azure Active Directory B2C
description: Konfigurera registrering och inloggning med OpenID Connect med Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 720deb28ce124af23035337ac88cfb1d37fc7c53
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509690"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med OpenID Connect med Azure Active Directory B2C

[OpenID Connect](active-directory-b2c-reference-oidc.md) är ett autentiseringsprotokoll som byggts ovanpå OAuth 2,0 och som kan användas för säker inloggning av användare. De flesta identitets leverantörer som använder det här protokollet stöds i Azure AD B2C. Den här artikeln förklarar hur du kan lägga till anpassade OpenID ansluta identitets leverantörer till dina användar flöden.

## <a name="add-the-identity-provider"></a>Lägg till identitets leverantören

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitets leverantörer**och klicka sedan på **Lägg till**.
5. För **typ av identitetsprovider**väljer du **OpenID Connect**.

## <a name="configure-the-identity-provider"></a>Konfigurera identitets leverantören

Varje OpenID ansluter identitets leverantörer beskriver ett dokumentmetadata som innehåller merparten av den information som krävs för att utföra inloggning. Detta omfattar information som webb adresserna som ska användas och platsen för tjänstens offentliga signerings nycklar. OpenID Connect-Metadatadokumentet finns alltid i en slut punkt som slutar med `.well-known\openid-configuration`. För den OpenID Connect Identity-Provider som du vill lägga till anger du dess URL för metadata.

För att användarna ska kunna logga in kräver identitets leverantören utvecklare att registrera ett program i sin tjänst. Det här programmet har ett ID som kallas **klient-ID** och en **klient hemlighet**. Kopiera de här värdena från identitets leverantören och ange dem i motsvarande fält.

> [!NOTE]
> Klient hemligheten är valfri. Du måste dock ange en klient hemlighet om du vill använda ett [auktoriseringskod](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)som använder hemligheten för att byta kod för token.

Omfattning definierar den information och de behörigheter som du vill samla in från den anpassade identitets leverantören. OpenID Connect-begäranden måste innehålla `openid` omfattnings värde för att ta emot ID-token från identitets leverantören. Utan ID-token kan användarna inte logga in på Azure AD B2C med hjälp av den anpassade identitets leverantören. Andra omfattningar kan läggas till åtskilda med blank steg. Se den anpassade identitets leverantörens dokumentation för att se vilka andra omfattningar som kan vara tillgängliga.

Svars typen beskriver vilken typ av information som skickas tillbaka i det första anropet till `authorization_endpoint` den anpassade identitets leverantören. Följande svars typer kan användas:

- `code`: Som enligt [koden för auktoriseringskod](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)returneras en kod tillbaka till Azure AD B2C. Azure AD B2C fortsätter att anropa `token_endpoint` för att skicka koden för token.
- `token`: En åtkomsttoken returneras tillbaka till Azure AD B2C från den anpassade identitets leverantören.
- `id_token`: En ID-token returneras tillbaka till Azure AD B2C från den anpassade identitets leverantören.

Svars läget definierar metoden som ska användas för att skicka tillbaka data från den anpassade identitets leverantören till Azure AD B2C. Följande svars lägen kan användas:

- `form_post`: Det här svars läget rekommenderas för bästa säkerhet. Svaret överförs via http- `POST` metoden, med koden eller token som kodas i texten `application/x-www-form-urlencoded` med formatet.
- `query`: Koden eller token returneras som en frågeparameter.

Du kan använda domän tipset för att hoppa direkt till inloggnings sidan för den angivna identitets leverantören, i stället för att låta användaren göra ett val bland listan över tillgängliga identitets leverantörer. Ange ett värde för domän tipset för att tillåta den här typen av beteende. Om du vill gå till den anpassade identitets leverantören lägger du `domain_hint=<domain hint value>` till parametern i slutet av din begäran när du anropar Azure AD B2C för inloggning.

När den anpassade identitets leverantören skickar tillbaka en ID-token till Azure AD B2C måste Azure AD B2C kunna mappa anspråk från den mottagna token till anspråk som Azure AD B2C känner igen och använder. För var och en av följande mappningar läser du dokumentationen för den anpassade identitets leverantören för att förstå de anspråk som returneras tillbaka i identitets leverantörens token:

- `User ID`: Ange anspråket som tillhandahåller den unika identifieraren för den inloggade användaren.
- `Display Name`: Ange det anspråk som visar visnings namnet eller det fullständiga namnet för användaren.
- `Given Name`: Ange det anspråk som innehåller användarens förnamn.
- `Surname`: Ange det anspråk som innehåller användarens efter namn.
- `Email`: Ange det anspråk som tillhandahåller användarens e-postadress.

