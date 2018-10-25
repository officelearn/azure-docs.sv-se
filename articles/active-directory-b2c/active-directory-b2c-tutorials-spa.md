---
title: Självstudiekurs – Aktivera autentisering av app med en enda sida med konton med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Självstudie som lär dig använda Azure Active Directory B2C för att tillhandahålla en användarinloggning till en ensidesapplikation (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 0f2fa2bb8e20ce4cc187fe6f061d2d8c251c4673
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945220"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Självstudiekurs: Aktivera autentisering av app med en enda sida med konton med hjälp av Azure Active Directory B2C

Den här självstudien lär dig använda Azure Active Directory (Azure AD) B2C för att logga in och registrera användare i en ensidesapp (SPA). Med Azure AD B2C kan appar autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med öppna protokoll.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Registrera ett exempel på en ensidesapp i Azure AD B2C-katalogen.
> * Skapa principer för registrering av användare, inloggning, redigering av profil och återställning av lösenord.
> * Konfigurera exempelprogrammet så att det använder din Azure AD B2C-katalog.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Skapa en egen [Azure AD B2C-katalog](active-directory-b2c-get-started.md)
* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med arbetsbelastningen **ASP.NET och webbutveckling**.
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) eller senare
* Installera [Node.js](https://nodejs.org/en/download/)

## <a name="register-single-page-app"></a>Registrera ensidesapp

Program måste [registreras](../active-directory/develop/developer-glossary.md#application-registration) i katalogen innan de kan ta emot [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token) från Azure Active Directory. Programregistrering skapar ett [program-ID](../active-directory/develop/developer-glossary.md#application-id-client-id) för appen i din katalog. 

Logga in på [Azure-portalen](https://portal.azure.com/) som global administratör för Azure AD B2C-katalogen.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Välj **Azure AD B2C** i listan över tjänster i Azure Portal. 

2. I B2C-inställningarna klickar du på **Program** och sedan på **Lägg till**. 

    Registrera exempelwebbappen i katalogen med följande inställningar:
    
    ![Lägg till en ny app](media/active-directory-b2c-tutorials-spa/spa-registration.png)
    
    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | Min exempel-ensidesapp | Ange ett **Namn** som beskriver appen för konsumenterna. | 
    | **Ta med webbapp/webb-API** | Ja | Välj **Ja** för en ensidesapp. |
    | **Tillåt implicit flöde** | Ja | Välj **Ja** eftersom appen använder [OpenID Connect-inloggning](active-directory-b2c-reference-oidc.md). |
    | **Svarswebbadress** | `http://localhost:6420` | Svarswebbadresser är slutpunkter där Azure AD B2C returnerar de token som appen begär. I den här självstudien körs exemplet lokalt (lokal värd) och lyssnar på port 6420. |
    | **Inkludera intern klient** | Nej | Eftersom det här är en ensidesapp och inte en intern klient väljer du Nej. |
    
3. Klicka på **Skapa** för att registrera din app.

Registrerade appar visas i programlistan för Azure AD B2C-katalogen. Välj din ensidesapp i listan. Den registrerade ensidesappens egenskapsfönster visas.

![Egenskaper för ensidesapp](./media/active-directory-b2c-tutorials-spa/b2c-spa-properties.png)

Anteckna det **Programklients-id** som visas. Detta ID identifierar appen och behövs när appen konfigureras senare under självstudierna.

## <a name="create-policies"></a>Skapa principer

En Azure AD B2C-princip definierar arbetsflöden för användare. Vanliga arbetsflöden är att logga in, registrera sig, byta lösenord och redigera profiler.

### <a name="create-a-sign-up-or-sign-in-policy"></a>Skapa en registrerings- eller inloggningsprincip

Skapa en **registrerings- eller inloggningsprincip** om du vill registrera användare och sedan låta dem logga in på webbappen.

1. I Azure AD B2C-portalsidan väljer du **registrerings- eller inloggningsprinciper** och klickar på **Lägg till**.

    Konfigurerar principen med följande inställningar:

    ![Lägg till en registrerings- eller inloggningsprincip](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | SiUpIn | Ange ett **Namn** för principen. Principens namn har prefixet **B2C_1_**. Använd det fullständiga principnamnet **B2C_1_SiUpIn** i exempelkoden. | 
    | **Identitetsprovider** | E-postregistrering | Den identitetsprovider som används för att unikt identifiera användaren. |
    | **Registreringsattribut** | Visningsnamn och Postnummer | Välj vilka attribut som samlas in från användaren under registreringen. |
    | **Programanspråk** | Visningsnamn, postnummer, användare är ny, användarens objekt-ID | Välj [anspråk](../active-directory/develop/developer-glossary.md#claim) som ska tas med i [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token). |

2. Klicka på **Skapa** för att skapa principen. 

### <a name="create-a-profile-editing-policy"></a>Skapa en profilredigeringsprincip

Om du vill att användarna själva ska kunna återställa informationen i sin användarprofil skapar du en **profilredigeringsprincip**.

1. I Azure AD B2C-portalsidan väljer du **profilredigeringsprinciper** och klickar på **Lägg till**.

    Konfigurerar principen med följande inställningar:

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | SiPe | Ange ett **Namn** för principen. Principens namn har prefixet **B2C_1_**. Använd det fullständiga principnamnet **B2C_1_SiPe** i exempelkoden. | 
    | **Identitetsprovider** | Inloggning på lokalt konto | Den identitetsprovider som används för att unikt identifiera användaren. |
    | **Profilattribut** | Visningsnamn och Postnummer | Välj de attribut en användare kan ändra under profilredigering. |
    | **Programanspråk** | Visningsnamn, postnummer, användarens objekt-ID | Välj de [anspråk](../active-directory/develop/developer-glossary.md#claim) du vill ta med i [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token) efter en lyckad profilredigering. |

2. Klicka på **Skapa** för att skapa principen. 

### <a name="create-a-password-reset-policy"></a>Skapa en princip för återställning av lösenord

Om du vill kunna aktivera lösenordsåterställning i programmet måste du skapa en **princip för lösenordsåterställning**. Principen beskriver hur lösenordsåterställningen går till för konsumenterna och innehållet i de token som programmet tar emot när återställningen genomförts.

1. I Azure AD B2C-portalsidan väljer du **Princip för lösenordsåterställning** och klickar på **Lägg till**.

    Konfigurera principen med följande inställningar.

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | SSPR | Ange ett **Namn** för principen. Principens namn har prefixet **B2C_1_**. Använd det fullständiga principnamnet **B2C_1_SSPR** i exempelkoden. | 
    | **Identitetsprovider** | Återställ lösenord med e-postadress | Den identitetsprovider som används för att unikt identifiera användaren. |
    | **Programanspråk** | Användarens objekt-ID | Välj de [anspråk](../active-directory/develop/developer-glossary.md#claim) du vill ta med i [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token) efter en lyckad lösenordsåterställning. |

2. Klicka på **Skapa** för att skapa principen. 

## <a name="update-single-page-app-code"></a>Uppdatera kod för ensidesapp

När du nu registrerat en app och skapat principer måste du konfigurera appen så att den använder din Azure AD B2C-katalog. I den här självstudien får konfigurera en SPA JavaScript-app som du kan ladda ned från GitHub. 

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) eller klona exempelwebbappen från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
Exempelappen visar hur en ensidesapp kan använda Azure AD B2C för användarens registrering och inloggning, och anropa ett skyddat webb-API. Appen måste ändras så den använder appregistreringen i din katalog. 

Så här ändrar du appinställningarna:

1. Öppna filen `index.html` i exemplet för ensidesappen i Node.js.
2. Konfigurera exemplet med registreringsinformation för Azure AD B2C-katalogen. Ändra följande rader med kod (se till att ersätta värdena med namnen på dina kataloger och API:er):

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://fabrikamb2c.b2clogin.com/tfp/fabrikamb2c.onmicrosoft.com/B2C_1_<Sign-up or sign-in policy name>",
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://fabrikamb2chello.azurewebsites.net/hello',
    };
    ```

    Principnamnet som används i den här självstudien är **B2C_1_SiUpIn**. Om du använder ett annat principnamn använder du principnamnet i värdet `authority`.

## <a name="run-the-sample"></a>Kör exemplet

1. Starta en kommandotolk för Node.js.
2. Gå till den katalog som innehåller Node.js-exemplet. Till exempel `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Kör följande kommandon:
    ```
    npm install && npm update
    node server.js
    ```

    Konsolfönstret visar portnumret där appen finns.
    
    ```
    Listening on port 6420...
    ```

4. Använd en webbläsare för att gå till adressen `http://localhost:6420` för att visa appen.

Exempelappen har stöd för registrering av användare, inloggning, redigering av profil och återställning av lösenord. Den här självstudien visar hur en användare registrerar sig för att använda programmet med en e-postadress. Du kan själv prova andra scenarion.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Klicka på **Logga in** för att logga in som användare av SPA-appen. Då används principen **B2C_1_SiUpIn** som du definierade i ett tidigare steg.

2. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du inte har något konto klickar du på länken **Registrera dig**. 

3. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som begärs i principen.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen. 

    ![Arbetsflöde för registrering](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Klicka på **Skapa** och skapa ett lokalt konto i Azure AD B2C-katalogen.

Användaren kan nu använda e-postadressen och logga in och använda SPA-appen.

> [!NOTE]
> Efter inloggningen visar appen ett fel som handlar om ”otillräckliga behörigheter”. Du får felmeddelandet eftersom du försöker komma åt en resurs från demokatalogen. Eftersom ditt åtkomst-token endast är giltigt för din Azure AD-katalog är API-anropet obehörigt. Fortsätt med nästa självstudiekurs och skapa ett skyddat webb-API för din katalog. 

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-katalogen om du vill prova andra självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-katalogen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa en Azure AD B2C-katalog, att skapa principer och uppdatera exempelensidesappen så den använder din Azure AD B2C-katalogen. Fortsätt till nästa självstudie och lär dig registrera, konfigurera och anropa ett skyddat webb-API från en skrivbordsapp.

> [!div class="nextstepaction"]
> [Kodexempel för Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
