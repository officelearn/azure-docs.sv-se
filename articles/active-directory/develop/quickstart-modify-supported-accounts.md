---
title: Ändra konton som stöds av en app registrerad på Microsoft Identity Platform | Azure
description: Konfigurera en app registrerad på Microsoft Identity Platform för att ändra vilka, eller vilka konton, som har åtkomst till appen.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a2c68d607e7afc2e3eac675511734c8d054c427
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299540"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application-preview"></a>Snabbstart: Ändra konton som stöds av ett program (förhandsversion)

När du registrerar en app på Microsoft Identity Platform vill du kanske att appen endast kan kommas åt av användare i din organisation. Du kan också vilja att appen är åtkomlig för användare i externa organisationer eller av användare i externa i organisationer samt användare som inte nödvändigtvis är en del av en organisation (personliga konton).

I den här snabbstarten lär du hur du ändrar appens konfiguration för att ändra vilka, eller vilka konton, som har åtkomst till appen.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar kontrollerar du att följande krav är uppfyllda:

* Lär dig mer om [behörigheter och medgivande](v2-permissions-and-consent.md) som stöds, vilket är viktigt att förstå när du skapar appar som måste användas av andra användare eller appar.
* Ha en klientorganisation som har appar som är registrerade till den.
  * Om du inte har några registrerade appar kan du [få information om hur du registrerar appar på Microsoft Identity Platform](quickstart-register-app.md).
* Registrera dig för förhandsversionsfunktionerna appregistreringar i Azure-portalen. Stegen i den här snabbstarten motsvarar det nya användargränssnittet och fungerar bara om du använder förhandsversionsfunktionerna.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logga in på Azure-portalen och välj appen

Innan du kan konfigurera appen gör du följande:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller en personligt Microsoft-konto.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten och sedan **Appregistreringar (förhandsversion)**.
1. Leta reda på och välj den app du vill konfigurera. När du har valt appen ser du dess **översikt** eller huvudregistreringssida.
1. Följ stegen för att [ändra appregistreringen för att stödja olika konton](#change-the-application-registration-to-support-different-accounts).
1. Om du har en ensidesapplikation [aktiverar du implicit OAuth 2.0-beviljande](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>Ändra appregistreringen för att stödja olika konton

Om du skriver ett program som du vill göra tillgängliga för dina kunder eller partner utanför din organisation behöver du uppdatera programdefinitionen i Azure-portalen.

> [!IMPORTANT]
> Azure AD kräver att app-ID-URI för program för flera klientorganisationer är globalt unik. App-ID-URI är en av de sätt som ett program identifieras i protokollmeddelanden. För ett program för en enskild klientorganisation räcker det att app-ID-URI är unikt i den klientorganisationen. För ett program för flera klientorganisationer måste den vara globalt unikt så att Azure AD kan hitta programmet bland alla klientorganisationer. Global unikhet framtvingas genom att det krävs att app-ID-URI har ett värdnamn som matchar en verifierad domän i Azure AD-klientorganisationen. Om namnet på din klientorganisation till exempel är contoso.onmicrosoft.com blir en giltig app-ID-URI https://contoso.onmicrosoft.com/myapp. Om din klientorganisation har en verifierad domän som är contoso.com blir en giltig app-ID-URI även https://contoso.com/myapp. Om App-ID-URI inte följer detta mönster misslyckas konfigurationen av ett program som ett program för flera klientorganisationer.

### <a name="to-change-who-can-access-your-application"></a>Ändra vilka som har åtkomst till appen

1. På appens **översiktssida** väljer du avsnittet **Autentisering** och ändrar det valda värdet under **Kontotyper som stöds**.
    * Välj **Accounts in this directory only** (Endast konton i den här katalogen) om du skapar en verksamhetsspecifik app. Det här alternativet är inte tillgängligt om appen inte har registrerats i katalogen.
    * Välj **Accounts in any organizational directory** (Konton i alla organisationskataloger) om du vill rikta dig till mot alla företags- och utbildningskunder.
    * Välj **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton) för att rikta dig till den största uppsättningen kunder.
1. Välj **Spara**.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>Aktivera implicit OAuth 2.0-beviljande för ensidesprogram

Ensidesprogram (SPA, Single-page applications) är vanligtvis strukturerade med en klientdel som bygger starkt på JavaScript, körs i webbläsaren och anropar programmets webb-API-serverdel för att utföra sin affärslogik. För SPA:er som hanteras i Azure AD använder du implicit OAuth 2.0-beviljande för att autentisera användaren med Azure AD och hämta en token som du kan använda för att skydda anrop från programmets JavaScript-klient till dess serverdels-webb-API.

När användaren har gett sitt medgivande kan det här autentiseringsprotokollet användas till att hämta token för att skydda anrop mellan klienten och andra webb-API-resurser som har konfigurerats för programmet. Om du vill lära dig mer om implicit auktoriseringsbeviljande och få hjälp med att avgöra om det är rätt val för ditt programscenario kan du läsa om flödet för implicit OAuth 2.0-beviljande i Azure AD [v1.0](v1-oauth2-implicit-grant-flow.md) och [v2.0](v2-oauth2-implicit-grant-flow.md).

Som standard är implicit OAuth 2.0-beviljande inaktiverat för program. Du kan aktivera implicit OAuth 2.0-beviljande för appen genom att följa stegen som beskrivs nedan.

### <a name="to-enable-oauth-20-implicit-grant"></a>Så aktiverar du implicit OAuth 2.0-beviljande

1. På appens **översiktssida** väljer du avsnittet **Autentisering**.
1. Under **Avancerade inställningar** letar du redan på avsnittet **Implicit beviljande**.
1. Välj **ID-token**, **Åtkomsttoken** eller båda.
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

Läs mer om dessa andra relaterade snabbstarter för apphantering:

* [Registrera en app på Microsoft Identity Platform](quickstart-register-app.md)
* [Konfigurera ett klientprogram för åtkomst till webb-API:er](quickstart-configure-app-access-web-apis.md)
* [Konfigurera en app att exponera webb-API:er](quickstart-configure-app-expose-web-apis.md)
* [Ta bort en app registrerad på Microsoft Identity Platform](quickstart-remove-app.md)

Mer information om de två Azure AD-objekt som representerar ett registrerat program och relationen mellan dem finns i [Programobjekt och tjänsthuvudnamnsobjekt](app-objects-and-service-principals.md).

Mer information om de varumärkesriktlinjer som du bör använda när du utvecklar program med Azure Active Directory finns i [Varumärkesriktlinjer för program](howto-add-branding-in-azure-ad-apps.md).
