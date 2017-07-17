---
title: 'Azure Active Directory B2C: Programregistrering | Microsoft Docs'
description: Registrera ditt program med Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 3499ff57e650c70679dfa018eec5dbe1a6173a33
ms.contentlocale: sv-se
ms.lasthandoff: 07/04/2017



---
# Azure Active Directory B2C: Registrera ditt program
<a id="azure-active-directory-b2c-register-your-application" class="xliff"></a>

> [!IMPORTANT]
> Program som har skapats från Azure AD B2C-bladet i Azure Portal måste hanteras från samma plats. Om du redigerar B2C-program med hjälp av PowerShell eller en annan portal stöds de inte och kommer troligen inte att fungera med Azure AD B2C. Läs mer [nedan](#faulted-apps).
>

## Krav
<a id="prerequisite" class="xliff"></a>

Om du vill skapa ett program som accepterar registrering och inloggning av konsumenter måste du först registrera programmet med en Azure Active Directory B2C-klient. Skaffa en egen klient genom att följa stegen i [Skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md). När du har följt alla steg i artikeln är B2C-funktionsbladet fäst på startsidan.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Gå till B2C-funktionsbladet
<a id="navigate-to-the-b2c-features-blade" class="xliff"></a>

Om B2C-funktionsbladet är fäst på startsidan visas bladet så fort du loggar in på [Azure-portalen](https://portal.azure.com/) som global administratör för B2C-klienten.

Du kan också öppna bladet genom att klicka på **Fler tjänster** och sedan söka på **Azure AD B2C** i det vänstra navigeringsfönstret på [Azure Portal](https://portal.azure.com/).

> [!IMPORTANT]
> Du måste vara global administratör för B2C-klienten för att kunna komma åt B2C-funktionsbladet. En global administratör från en annan klient eller en användare från en klient kan inte komma åt det.  Du kan växla till B2C-klienten med klientväxlaren i det övre högra hörnet i Azure Portal.
>
>

## Registrera ett webbprogram
<a id="register-a-web-application" class="xliff"></a>

1. Klicka på **Program** på B2C-funktionsbladet på Azure-portalen.
1. Klicka på **+Lägg till** överst på bladet.
1. Ange ett **namn** för programmet som beskriver det för konsumenterna. Du kan till exempel skriva ”Contoso B2C-app”.
1. Ändra **Include web app/web API** (Ta med webbapp/webb-API) till **Ja**.
1. Ange ett [giltigt värde](#limitations) för **Svars-URL:erna**, som är slutpunkter där Azure AD B2C returnerar de tokens som ditt program begär. Ange till exempel `https://localhost:44316/`.
1. Klicka på **Skapa** för att registrera ditt program.
1. Klicka på det program som du just har skapat och kopiera det globalt unika **klient-ID:t** som du ska använda senare i koden.
1. Om ditt webbprogram också ska anropa ett webb-API som skyddas av Azure AD B2C måste du:
    1. Skapa en **Programhemlighet** genom att gå till bladet **Nycklar** och klicka på knappen **Generera nyckel**.
    1. Klicka på **API-åtkomst**, klicka på **Lägg till** och välj din webb-API och scope (behörigheter).

> [!NOTE]
> En **programhemlighet** är en viktig autentiseringsuppgift och bör skyddas på lämpligt sätt.
>

## Registrera en webb-API
<a id="register-a-web-api" class="xliff"></a>

1. Klicka på **Program** på B2C-funktionsbladet på Azure-portalen.
1. Klicka på **+Lägg till** överst på bladet.
1. Ange ett **namn** för programmet som beskriver det för konsumenterna. Du kan till exempel skriva ”Contoso B2C-api”.
1. Ändra **Include web app/web API** (Ta med webbapp/webb-API) till **Ja**.
1. Ange ett [giltigt värde](#choosing-a-web-app/api-reply-url) för **Svars-URL:erna**, som är slutpunkter där Azure AD B2C returnerar de tokens som ditt program begär. Ange till exempel `https://localhost:44316/`.
1. Ange en **App-ID-URI**. Det här är identifieraren som används för ditt webb-API. Ange till exempel ”information”. Den genererar den fullständiga identifierar-URI:n nedanför.
1. Klicka på **Skapa** för att registrera ditt program.
1. Klicka på det program som du just har skapat och kopiera det globalt unika **klient-ID:t** som du ska använda senare i koden.
1. Klicka på alternativet för **publicerade omfång**. Här definierar du behörigheterna (omfång) som kan beviljas till andra program.
1. Lägg till fler omfång efter behov. Som standard definieras omfånget ”user_impersonation”. Detta ger andra program möjlighet att komma åt det här API:et för den inloggade användarens räkning. Du kan ta bort det om du vill.
1. Klicka på **Spara**.

## Registrera ett mobilt/internt program
<a id="register-a-mobilenative-application" class="xliff"></a>

1. Klicka på **Program** på B2C-funktionsbladet på Azure-portalen.
1. Klicka på **+Lägg till** överst på bladet.
1. Ange ett **namn** för programmet som beskriver det för konsumenterna. Du kan till exempel skriva ”Contoso B2C-app”.
1. Ändra **Include native client** (Ta med intern klient) till **Ja**.
1. Ange en **Omdirigerings-URI** med ett eget schema. Till exempel com.onmicrosoft.contoso.appname://redirect/path. Se till att välja en [bra omdirigerings-URI](#choosing-a-native-application-redirect-uri) som inte innehåller specialtecken, till exempel understreck.
1. Klicka på **Spara** för att registrera programmet.
1. Klicka på det program som du just har skapat och kopiera det globalt unika **klient-ID:t** som du ska använda senare i koden.
1. Om ditt egna program också ska anropa ett webb-API som skyddas av Azure AD B2C måste du:
    1. Skapa en **Programhemlighet** genom att gå till bladet **Nycklar** och klicka på knappen **Generera nyckel**.
    1. Klicka på **API-åtkomst**, klicka på **Lägg till** och välj din webb-API och scope (behörigheter).

> [!NOTE]
> En **programhemlighet** är en viktig autentiseringsuppgift och bör skyddas på lämpligt sätt.
>

## Begränsningar
<a id="limitations" class="xliff"></a>

### Om du väljer en webbapp/api svars-URL
<a id="choosing-a-web-appapi-reply-url" class="xliff"></a>

Appar som har registrerats med Azure AD B2C är för närvarande begränsade till en begränsad uppsättning svars-URL-värden. Svars-URL för webbprogram och tjänster måste börja med schemat `https` och alla svars-URL-värden måste dela en enda DNS-domän. Exempelvis kan du registrera ett webbprogram som har en av dessa svars-URL: er:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Registreringssystemet jämför hela DNS-namnet på den befintliga svars-URL:en med DNS-namnet på den svars-URL som du lägger till. Begäran om att lägga till DNS-namnet kommer att misslyckas om något av följande villkor föreligger:

* Hela DNS-namnet på den nya svars-URL:en motsvarar inte DNS-namnet på den befintliga svars-URL:en.
* Hela DNS-namnet på den nya svars-URL:en är inte en underdomän till den befintliga svars-URL:en.

Till exempel om appen har svars-URL:

`https://login.contoso.com`

Du kan lägga till data så här:

`https://login.contoso.com/new`

I det här fallet matchar DNS-namnet exakt. Du kan också göra detta:

`https://new.login.contoso.com`

I så fall måste du referera till DNS-underdomänen login.contoso.com. Om du vill ha en app som har login-east.contoso.com och login-west.contoso.com som svars-URL: er måste du lägga till dessa svars-URL: er i den här ordningen:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Du kan lägga till två senare eftersom de är underdomäner i den första reply-URL:en, contoso.com.

### Välja en programspecifik omdirigerings-URI
<a id="choosing-a-native-application-redirect-uri" class="xliff"></a>

Det finns två viktiga överväganden när du väljer en omdirigerings-URI för mobila/interna program:

* **Unik**: Schemat för omdirigerings-URI måste vara unikt för varje program. I vårt exempel (com.onmicrosoft.contoso.appname://redirect/path) använder vi com.onmicrosoft.contoso.appname som schema. Vi rekommenderar att detta mönster följs. Om två program delar samma schema visas en dialogruta för att "välja app". Inloggningen misslyckas om användaren gör ett felaktigt val.
* **Fullständig**: Omdirigerings-URI måste ha ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen (till exempel fungerar //contoso/ medan //contoso misslyckas).

Se till att det inte finns några specialtecken som understreck i omdirigerings-uri.

### Felaktig appar
<a id="faulted-apps" class="xliff"></a>

B2C program bör INTE redigeras:

* På andra programhanteringsportaler, som den [klassiska Azure-portalen](https://manage.windowsazure.com/) & den [Programregistreringsportalen](https://apps.dev.microsoft.com/).
* Med Graph API eller PowerShell

Om du redigerar B2C-programmet enligt ovan och försöker att redigera den i Azure AD B2C-funktionsbladet på Azure-portalen kommer den att bli en felaktig app och programmet kommer inte längre att kunna användas med Azure AD B2C. Du måste ta bort appen och skapa det igen.

Ta bort appen genom att gå till den [Appregistreringsportalen](https://apps.dev.microsoft.com/) och ta bort det appen. Du måste vara ägare till appen (och inte bara en administratör för klienten) för att appen ska vara synlig.

## Nästa steg
<a id="next-steps" class="xliff"></a>

Nu när du har registrerat ett program med Azure AD B2C kan du gå [en av våra snabbstartsguider](active-directory-b2c-overview.md#get-started) för att komma igång.

