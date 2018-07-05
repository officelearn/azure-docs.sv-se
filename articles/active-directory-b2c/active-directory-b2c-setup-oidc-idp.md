---
title: Att lägga till OpenID Connect Identitetsproviders i inbyggda principer i Azure Active Directory B2C | Microsoft Docs
description: Översikt över guide om hur du lägger till OpenID Connect-providern i inbyggda principer i Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e5baf95cd2426c9753620cba7c5a67b4c1672788
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444233"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Azure Active Directory B2C: Lägga till en anpassad OpenID Connect identitetsprovider i inbyggda principer

>[!NOTE]
> Den här funktionen är i offentlig förhandsversion. Använd inte funktionen i produktionsmiljöer.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) är ett autentiseringsprotokoll som bygger på OAuth 2.0, som kan användas för säker inloggning av användare. De flesta identitetsleverantörer som använder detta protokoll, exempelvis [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), stöds i Azure AD B2C. Den här artikeln förklarar hur du kan lägga till anpassad OpenID Connect Identitetsproviders i din inbyggda principer.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>Konfigurera en anpassad OpenID Connect identitetsprovider

Lägga till en anpassad OpenID Connect identitetsprovider:

1. Följ dessa steg för att [navigera till inställningarna för Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) i Azure-portalen.
1. Klicka på **Identitetsprovidrar**.
1. Klicka på **Lägg till**.
1. För den **identifiera providertyp**väljer **OpenID Connect**.

### <a name="setting-up-the-openid-connect-identity-provider"></a>Konfigurera identitetsleverantören OpenID Connect

#### <a name="metadata-url"></a>Metadata-URL

Enligt specifikationen beskriver varje OpenID Connect identitetsleverantörer ett metadatadokument som innehåller de flesta av informationen som krävs för att utföra logga in. Detta omfattar information, till exempel URL: er att använda och platsen för tjänstens offentliga Signeringsnycklar. Metadatadokument OpenID Connect finns alltid på en slutpunkt som slutar med `.well-known\openid-configuration`.

Identitetsprovider OpenID Connect tittar du ange dess metadata-URL.

#### <a name="client-id-and-secret"></a>Klient-ID och hemlighet

Om du vill tillåta användare att logga in, kräver identitetsprovidern att utvecklare kan registrera ett program i sin tjänst. Det här programmet har ett ID (kallas den **klient-ID**) och en **klienthemlighet**. Kopiera värdena från identitetsprovidern och ange dem i motsvarande fält.

> [!NOTE]
> Klienthemlighet är valfritt. Men du måste ange en klienthemlighet om du vill använda den [auktoriseringskodflödet](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), som använder hemligheten för att utbyta koden för token.

#### <a name="scope"></a>Omfång

Omfång definierar information och behörigheterna som du vill samla in från din anpassade identitetsprovider. OpenID Connect-förfrågningar måste innehålla den `openid` omfång värdet för att få ID-token från identitetsprovidern. Utan ID-token användare kommer inte att kunna logga in på Azure AD B2C med hjälp av den anpassade identitetsprovidern.

Andra scope kan läggas (avgränsade med blanksteg). I den anpassade identitetsprovidern-dokumentationen för att se vad andra scope kan vara tillgängliga.

#### <a name="response-type"></a>Svarstyp

Svarstypen beskriver vilken typ av information ska skickas tillbaka i första anropet till den `authorization_endpoint` för den anpassade identitetsprovidern. 

* `code`: Enligt den [auktoriseringskodflödet](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), returneras en kod till Azure AD B2C. Azure AD B2C fortsätter sedan med att anropa den `token_endpoint` att byta ut koden för token.
* `token`: En åtkomst-token returneras till Azure AD B2C från den anpassade identitetsprovidern.
* `id_token`: Ett ID-token returneras till Azure AD B2C från den anpassade identitetsprovidern.


#### <a name="response-mode"></a>Svarsläge

Svarsläget definierar den metod som ska användas för att skicka data från den anpassade identitetsprovidern till Azure AD B2C.

* `form_post`: Det här Svarsläge rekommenderas för bästa säkerhet. Svaret skickas via HTTP `POST` metod med kod eller token som kodas i brödtexten med den `application/x-www-form-urlencoded` format.
* `query`: Den kod eller token returneras som en frågeparameter.


#### <a name="domain-hint"></a>Tips för domänen

Tips för domänen kan användas för att gå direkt till inloggningssidan för den angivna identitetsprovidern, i stället för att användaren gör ett val i listan över tillgängliga identitetsprovidrar. Om du vill tillåta den här typen av beteende, ange ett värde för tips för domänen.

Om du vill gå direkt till den anpassade identitetsprovidern, lägger du till parametern `domain_hint=<domain hint value>` i slutet av din begäran när du anropar Azure AD B2C för inloggning.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>Mappning av anspråk från identitetsprovider OpenID Connect

Efter den anpassade identiteten skickar providern en ID-token tillbaka till Azure AD B2C, Azure AD B2C behov för att kunna mappa anspråk från den mottagna token till anspråk som Azure AD B2C känner igen och använder. 

Var och en av mappningarna nedan finns i dokumentationen för den anpassade identitetsprovidern att förstå de anspråk som returneras i identitetsleverantörens token.

* `User ID`: Ange det anspråk som innehåller den unika identifieraren för den inloggade användaren.
* `Display Name`: Ange anspråk som innehåller visningsnamn eller efternamn för användaren.
* `Given Name`: Ange anspråk som innehåller det första namnet för användaren.
* `Surname`: Ange anspråk som innehåller efternamnet på användaren.
* `Email`: Ange det anspråk som innehåller e-postadressen för användaren.

## <a name="next-steps"></a>Nästa steg

Lägg till anpassad OpenID Connect identitetsprovider om att din [inbyggd princip](active-directory-b2c-reference-policies.md).
