---
title: Konfigurera registrering och inloggning med OpenID Connect - Azure Active Directory B2C | Microsoft Docs
description: Konfigurera registrering och inloggning med OpenID Connect med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: de0c866889f15f096bea26915f58cd4e268dfed3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428763"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med OpenID Connect med hjälp av Azure Active Directory B2C

>[!NOTE]
> Den här funktionen är en allmänt tillgänglig förhandsversion. Använd inte funktionen i produktionsmiljöer.


[OpenID Connect](active-directory-b2c-reference-oidc.md) är ett autentiseringsprotokoll som bygger på OAuth 2.0, som kan användas för säker inloggning av användare. De flesta identitetsleverantörer som använder detta protokoll stöds i Azure AD B2C. Den här artikeln förklarar hur du kan lägga till anpassad OpenID Connect Identitetsproviders i dina användarflöden.

## <a name="add-the-identity-provider"></a>Lägg till identitetsleverantören.

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **Identitetsprovidrar**, och klicka sedan på **Lägg till**.
5. För den **identifiera providertyp**väljer **OpenID Connect (förhandsversion)**.

## <a name="configure-the-identity-provider"></a>Konfigurera identitetsleverantören.

Varje OpenID Connect identitetsleverantörer beskriver ett metadatadokument som innehåller de flesta av informationen som krävs för att utföra logga in. Detta omfattar information, till exempel URL: er att använda och platsen för tjänstens offentliga Signeringsnycklar. Metadatadokument OpenID Connect finns alltid på en slutpunkt som slutar med `.well-known\openid-configuration`. Identitetsprovider OpenID Connect tittar du ange dess metadata-URL.

Om du vill tillåta användare att logga in, kräver identitetsprovidern utvecklare att registrera ett program i sin tjänst. Det här programmet har ett ID som kallas den **klient-ID** och en **klienthemlighet**. Kopiera värdena från identitetsprovidern och ange dem i motsvarande fält.

> [!NOTE]
> Klienthemlighet är valfritt. Men du måste ange en klienthemlighet om du vill använda den [auktoriseringskodflödet](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), som använder hemligheten för att utbyta koden för token.

Omfång definierar den information och behörigheter som du vill samla in från din anpassade identitetsprovider. OpenID Connect-förfrågningar måste innehålla den `openid` omfång värdet för att få ID-token från identitetsprovidern. Utan ID-token användare kan inte logga in på Azure AD B2C med hjälp av den anpassade identitetsprovidern. Andra scope kan läggas avgränsade med blanksteg. I den anpassade identitetsprovidern-dokumentationen för att se vad andra scope kan vara tillgängliga.

Svarstypen beskriver vilken typ av information skickas tillbaka i det första anropet till den `authorization_endpoint` för den anpassade identitetsprovidern. Du kan använda följande svarstyper av:

- `code`: Enligt den [auktoriseringskodflödet](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), returneras en kod till Azure AD B2C. Azure AD B2C fortsätter att anropa den `token_endpoint` att byta ut koden för token.
- `token`: Tillbaka till Azure AD B2C returneras en åtkomsttoken från den anpassade identitetsprovidern.
- `id_token`: Ett ID-token som returneras från den anpassade identitetsprovidern tillbaka till Azure AD B2C.

Svarsläget definierar den metod som ska användas för att skicka data från den anpassade identitetsprovidern till Azure AD B2C. Du kan använda följande svar lägen:

- `form_post`: Den här Svarsläge rekommenderas för bästa säkerhet. Svaret skickas via HTTP `POST` metod med kod eller token som kodas i brödtexten med den `application/x-www-form-urlencoded` format.
- `query`: Kod eller token returneras som en frågeparameter.

Tips för domänen kan användas för att gå direkt till inloggningssidan för den angivna identitetsprovidern, i stället för att användaren gör ett val i listan över tillgängliga identitetsprovidrar. Om du vill tillåta den här typen av beteende, ange ett värde för tips för domänen. Om du vill gå direkt till den anpassade identitetsprovidern, lägger du till parametern `domain_hint=<domain hint value>` i slutet av din begäran när du anropar Azure AD B2C för inloggning.

Efter den anpassade identiteten skickar providern en ID-token tillbaka till Azure AD B2C, Azure AD B2C behov för att kunna mappa anspråk från den mottagna token till anspråk som Azure AD B2C känner igen och använder. För var och en av följande mappningar finns i dokumentationen för den anpassade identitetsprovidern att förstå de anspråk som returneras i identitetsleverantörens token:

- `User ID`: Ange det anspråk som innehåller den unika identifieraren för den inloggade användaren.
- `Display Name`: Ange anspråk som innehåller visningsnamn eller efternamn för användaren.
- `Given Name`: Ange anspråk som innehåller det första namnet för användaren.
- `Surname`: Ange anspråk som innehåller efternamnet på användaren.
- `Email`: Ange det anspråk som innehåller e-postadressen för användaren.

