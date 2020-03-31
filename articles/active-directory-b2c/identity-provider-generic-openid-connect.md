---
title: Konfigurera registrering och inloggning med OpenID Connect
titleSuffix: Azure AD B2C
description: Konfigurera registrering och inloggning med valfri OpenID Connect-identitetsprovider (IdP) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c72abf79f0a420309ebe229673be9439fd99b74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188264"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med OpenID Connect med Azure Active Directory B2C

[OpenID Connect](openid-connect.md) är ett autentiseringsprotokoll som bygger på OAuth 2.0 och som kan användas för säker användarloggning. De flesta identitetsleverantörer som använder det här protokollet stöds i Azure AD B2C. I den här artikeln beskrivs hur du kan lägga till anpassade OpenID Connect-identitetsleverantörer i dina användarflöden.

## <a name="add-the-identity-provider"></a>Lägg till identitetsprovidern

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att klicka på **katalog + prenumerationsfiltret** i den övre menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **Ny OpenID Connect-provider**.

## <a name="configure-the-identity-provider"></a>Konfigurera identitetsprovidern

Varje OpenID Connect-identitetsprovider beskriver ett metadatadokument som innehåller det mesta av den information som krävs för att utföra inloggning. Detta inkluderar information som webbadresser som ska användas och platsen för tjänstens offentliga signeringsnycklar. Metadatadokumentet OpenID Connect finns alltid vid en `.well-known\openid-configuration`slutpunkt som slutar i . För den OpenID Connect-identitetsprovider som du vill lägga till anger du dess metadata-URL.

## <a name="client-id-and-secret"></a>Klient-ID och hemlighet

För att tillåta användare att logga in kräver identitetsprovidern utvecklare att registrera ett program i sin tjänst. Det här programmet har ett ID som kallas **klient-ID** och en **klienthemlighet**. Kopiera dessa värden från identitetsprovidern och ange dem i motsvarande fält.

> [!NOTE]
> Klienthemligheten är valfri. Du måste dock ange en klienthemlighet om du vill använda [auktoriseringskodflödet](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), som använder hemligheten för att byta ut koden mot token.

## <a name="scope"></a>Omfång

Scope definierar den information och de behörigheter som du vill samla in från din anpassade identitetsleverantör. OpenID Connect-begäranden `openid` måste innehålla scopevärdet för att kunna ta emot ID-token från identitetsprovidern. Utan ID-token kan användare inte logga in på Azure AD B2C med hjälp av den anpassade identitetsprovidern. Andra scope kan läggas till avgränsade med blanksteg. Se den anpassade identitetsproviderns dokumentation för att se vilka andra scope som kan vara tillgängliga.

## <a name="response-type"></a>Typ av svar

Svarstypen beskriver vilken typ av information som skickas `authorization_endpoint` tillbaka i det första anropet till den anpassade identitetsprovidern. Följande svarstyper kan användas:

* `code`: Enligt [auktoriseringskodflödet](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)returneras en kod tillbaka till Azure AD B2C. Azure AD B2C fortsätter `token_endpoint` att anropa att byta koden för token.
* `id_token`: En ID-token returneras tillbaka till Azure AD B2C från den anpassade identitetsprovidern.

## <a name="response-mode"></a>Svarsläge

Svarsläget definierar den metod som ska användas för att skicka tillbaka data från den anpassade identitetsprovidern till Azure AD B2C. Följande svarslägen kan användas:

* `form_post`: Det här svarsläget rekommenderas för bästa säkerhet. Svaret överförs via HTTP-metoden, `POST` där koden eller token kodas `application/x-www-form-urlencoded` i brödtexten med formatet.
* `query`: Koden eller token returneras som en frågeparameter.

## <a name="domain-hint"></a>Tips för domän

Domäntipset kan användas för att hoppa direkt till inloggningssidan för den angivna identitetsleverantören, i stället för att låta användaren göra ett val bland listan över tillgängliga identitetsleverantörer. Om du vill tillåta den här typen av beteende anger du ett värde för domäntipset. Om du vill hoppa till den `domain_hint=<domain hint value>` anpassade identitetsprovidern lägger du till parametern i slutet av din begäran när du anropar Azure AD B2C för inloggning.

## <a name="claims-mapping"></a>Mappning av anspråk

När den anpassade identitetsprovidern skickar tillbaka en ID-token till Azure AD B2C måste Azure AD B2C kunna mappa anspråk från den mottagna token till de anspråk som Azure AD B2C känner igen och använder. För var och en av följande mappningar läser du dokumentationen av den anpassade identitetsprovidern för att förstå de anspråk som returneras tillbaka i identitetsproviderns token:

* **Användar-ID:** Ange anspråket som tillhandahåller den *unika identifieraren* för den inloggade användaren.
* **Visningsnamn**: Ange det anspråk som ger *användarens visningsnamn* eller *fullständiga namn.*
* **Förnamn**: Ange det anspråk som ger *användarens förnamn.*
* **Efternamn**: Ange det anspråk som ger användarens *efternamn.*
* **E-post**: Ange anspråket som ger *användarens e-postadress.*
