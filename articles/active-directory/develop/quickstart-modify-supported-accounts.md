---
title: 'Snabb start: ändra Microsoft identifiera plattforms program konton | Azure'
description: I den här snabb starten konfigurerar du ett program som är registrerat med Microsoft Identity Platform för att ändra vem eller vilka konton som har åtkomst till programmet.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: d143bde9c22bc726f00b5c209d1b7fbc131905b0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258021"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Snabb start: ändra de konton som stöds av ett program

När du registrerar en app på Microsoft Identity Platform vill du kanske att appen endast kan kommas åt av användare i din organisation. Du kan också vilja att appen är åtkomlig för användare i externa organisationer eller av användare i externa i organisationer samt användare som inte nödvändigtvis är en del av en organisation (personliga konton).

I den här snabbstarten lär du hur du ändrar appens konfiguration för att ändra vilka, eller vilka konton, som har åtkomst till appen.

## <a name="prerequisites"></a>Förutsättningar

* Slut för ande av [snabb start: registrera ett program med Microsoft Identity Platform](quickstart-register-app.md)

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logga in på Azure-portalen och välj appen

Innan du kan konfigurera appen gör du följande:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** och väljer sedan **Appregistreringar**.
1. Leta reda på och välj den app du vill konfigurera. När du har valt appen ser du dess **översikt** eller huvudregistreringssida.
1. Följ stegen för att [ändra appregistreringen för att stödja olika konton](#change-the-application-registration-to-support-different-accounts).
1. Om du har en ensidesapplikation [aktiverar du implicit OAuth 2.0-beviljande](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>Ändra appregistreringen för att stödja olika konton

Om du skriver ett program som du vill göra tillgängliga för dina kunder eller partner utanför din organisation behöver du uppdatera programdefinitionen i Azure-portalen.

> [!IMPORTANT]
> Azure AD kräver att app-ID-URI för program för flera klientorganisationer är globalt unik. App-ID-URI är en av de sätt som ett program identifieras i protokollmeddelanden. För ett program för en enskild klientorganisation räcker det att app-ID-URI är unikt i den klientorganisationen. För ett program för flera klientorganisationer måste den vara globalt unikt så att Azure AD kan hitta programmet bland alla klientorganisationer. Global unikhet framtvingas genom att det krävs att app-ID-URI har ett värdnamn som matchar en verifierad domän i Azure AD-klientorganisationen. Om namnet på din klientorganisation till exempel är contoso.onmicrosoft.com blir en giltig app-ID-URI `https://contoso.onmicrosoft.com/myapp`. Om din klientorganisation har en verifierad domän som är contoso.com blir en giltig app-ID-URI även `https://contoso.com/myapp`. Om App-ID-URI inte följer detta mönster misslyckas konfigurationen av ett program som ett program för flera klientorganisationer.

### <a name="to-change-who-can-access-your-application"></a>Ändra vilka som har åtkomst till appen

1. På appens **översiktssida** väljer du avsnittet **Autentisering** och ändrar det valda värdet under **Kontotyper som stöds**.
    * Välj **Accounts in this directory only** (Endast konton i den här katalogen) om du skapar en verksamhetsspecifik app. Det här alternativet är inte tillgängligt om appen inte har registrerats i katalogen.
    * Välj **Accounts in any organizational directory** (Konton i alla organisationskataloger) om du vill rikta dig till mot alla företags- och utbildningskunder.
    * Välj **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton) för att rikta dig till den största uppsättningen kunder.
1. Välj **Spara**.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>Aktivera implicit OAuth 2.0-beviljande för ensidesprogram

Ensidesprogram (SPA, Single-page applications) är vanligtvis strukturerade med en klientdel som bygger starkt på JavaScript, körs i webbläsaren och anropar programmets webb-API-serverdel för att utföra sin affärslogik. För SPA:er som hanteras i Azure AD använder du implicit OAuth 2.0-beviljande för att autentisera användaren med Azure AD och hämta en token som du kan använda för att skydda anrop från programmets JavaScript-klient till dess serverdels-webb-API.

När användaren har gett sitt medgivande kan det här autentiseringsprotokollet användas till att hämta token för att skydda anrop mellan klienten och andra webb-API-resurser som har konfigurerats för programmet. Om du vill lära dig mer om implicit auktoriseringsbeviljande och få hjälp med att avgöra om det är rätt val för ditt programscenario kan du läsa om flödet för implicit OAuth 2.0-beviljande i Azure AD [v1.0](../azuread-dev/v1-oauth2-implicit-grant-flow.md) och [v2.0](v2-oauth2-implicit-grant-flow.md).

Som standard är implicit OAuth 2.0-beviljande inaktiverat för program. Du kan aktivera implicit OAuth 2.0-beviljande för appen genom att följa stegen som beskrivs nedan.

### <a name="to-enable-oauth-20-implicit-grant"></a>Så aktiverar du implicit OAuth 2.0-beviljande

1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** och väljer sedan **Appregistreringar**.
1. Leta reda på och välj den app du vill konfigurera. När du har valt appen ser du dess **översikt** eller huvudregistreringssida.
1. På appens **översiktssida** väljer du avsnittet **Autentisering**.
1. Under **Avancerade inställningar** letar du redan på avsnittet **Implicit beviljande**.
1. Välj **ID-token**, **Åtkomsttoken** eller båda.
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Varumärkesriktlinjer för program](howto-add-branding-in-azure-ad-apps.md)
