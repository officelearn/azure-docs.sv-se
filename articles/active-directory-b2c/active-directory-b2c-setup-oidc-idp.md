---
title: Att lägga till OpenID Connect identitetsleverantörer i principer för inbyggda - Azure AD B2C | Microsoft Docs
description: Översikt över vägledning om hur du lägger till OpenID Connect providers i principer som är inbyggda i Azure AD B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: parakhj
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/23/2018
ms.author: davidmu
ms.openlocfilehash: 52cefd4fad96e94dd2cf0871fac12a9a4d74b96f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Azure Active Directory B2C: Lägga till en anpassad provider för OpenID Connect identitet i inbyggda principer

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) är ett autentiseringsprotokoll som bygger på OAuth 2.0 som kan användas på ett säkert sätt loggar användarna in. De flesta leverantörer som använder det här protokollet, t.ex Azure AD stöds i Azure AD B2C. Den här artikeln förklarar hur du lägger till anpassade OpenID Connect identitetsleverantörer i din inbyggda principer.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>Konfigurera en anpassad OpenID Connect identitetsleverantören.

Lägg till en anpassad provider för OpenID Connect identitet:

1. Följ dessa steg för att [navigera till Azure AD B2C-inställningar](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) i Azure-portalen.
1. Klicka på **identitetsleverantörer**.
1. Klicka på **Lägg till**.
1. För den **identitet providertyp**väljer **OpenID Connect**.

### <a name="setting-up-the-openid-connect-identity-provider"></a>Konfigurera identitetsleverantören OpenID Connect.

#### <a name="metadata-url"></a>Metadata-URL

Enligt specifikationen beskriver varje OpenID Connect identitetsleverantörer ett metadata-dokument som innehåller de flesta av information som krävs för att utföra inloggning. Detta omfattar information som de URL: er för att använda och platsen för tjänstens offentliga Signeringsnycklar. Metadatadokumentet OpenID Connect finns alltid på en slutpunkt som slutar med `.well-known\openid-configuration`.

Identitetsprovider OpenID Connect tittar du ange dess metadata-URL.

#### <a name="client-id-and-secret"></a>Klient-ID och Hemlig

Om du vill tillåta användare att logga in kräver identitetsleverantören utvecklare att registrera ett program i den här tjänsten. Det här programmet har ett ID (kallas den **klient-ID**) och en **klienthemlighet**. Kopiera värdena från identitetsleverantören och ange dem i motsvarande fält.

> [!NOTE]
> Klienthemligheten är valfritt. Men du måste ange en klienthemlighet om du vill använda den [auktoriseringskodflödet](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), som använder hemligheten för att utbyta koden för token.

#### <a name="scope"></a>Omfång

Scope definiera den information och de behörigheter som du vill samla in från din anpassade identitetsleverantören. OpenID Connect förfrågningar måste innehålla den `openid` omfång värdet för att kunna ta emot ID-token från identitetsleverantören. Utan ID-token, användare kommer inte att kunna logga in på Azure AD B2C med hjälp av den anpassa identitetsleverantören.

Andra scope läggs (avgränsade med blanksteg). Den anpassade identitetsleverantör dokumentationen att se vad andra scope som är tillgängliga.

#### <a name="response-type"></a>Svarstyp

Svarstypen beskriver vilken typ av information skickas tillbaka i det första anropet till den `authorization_endpoint` av den anpassa identitetsleverantören. 

* `code`: Som per den [auktoriseringskodflödet](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), returneras en kod till Azure AD B2C. Azure AD B2C fortsätter sedan att anropa den `token_endpoint` till exchange-koden för token.
* `token`: En åtkomst-token returneras tillbaka till Azure AD B2C från anpassade identitetsleverantören.
* `id_token`: Ett ID-token returneras tillbaka till Azure AD B2C från anpassade identitetsleverantören.


#### <a name="response-mode"></a>Svaret läge

Svaret läge definierar den metod som ska användas för att skicka informationen tillbaka från anpassade identitetsleverantören till Azure AD B2C.

* `form_post`: Det här svaret läge rekommenderas för bästa säkerhet. Svaret överförs via HTTP `POST` metod med kod eller token som kodats i en brödtext med hjälp av den `application/x-www-form-urlencoded` format.
* `query`: Kod eller token returneras som en frågeparameter.


#### <a name="domain-hint"></a>Domän-tipset

Domän-tipset kan användas för att hoppa över direkt till inloggningssidan för den angivna identitetsleverantören, i stället för att användaren gör ett val i listan över tillgängliga identitetsleverantörer. Ange ett värde för domän-tipset så att den här typen av problem.

Lägg till parametern för att hoppa till den anpassa identitetsleverantören `domain_hint=<domain hint value>` till slutet av din begäran vid anrop av Azure AD B2C för inloggning.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>Mappning av anspråk från identitetsleverantören OpenID Connect.

När den anpassade identiteten skickar providern en ID-token tillbaka till Azure AD B2C, Azure AD B2C måste kunna mappa anspråk från mottaget token till anspråk som Azure AD B2C känner igen och använder. 

Var och en av mappningarna nedan finns i dokumentationen för den anpassa identitetsleverantören att förstå de anspråk som returneras i identitetsleverantörens token.

* `User ID`: Ange anspråk som innehåller den unika identifieraren för den inloggade användaren.
* `Display Name`: Ange anspråk som innehåller namn eller efternamn för användaren.
* `Given Name`: Ange anspråk som innehåller förnamnet för användaren.
* `Surname`: Ange anspråk som innehåller efternamn för användaren.
* `Email`: Ange anspråk som innehåller e-postadressen för användaren.

## <a name="next-steps"></a>Nästa steg

Lägg till den anpassade OpenID Connect identitetsprovider till din [inbyggd princip](active-directory-b2c-reference-policies.md).
