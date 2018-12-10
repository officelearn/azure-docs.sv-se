---
title: Självstudiekurs – Aktivera autentisering av app med en enda sida med konton med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Självstudie som lär dig använda Azure Active Directory B2C för att tillhandahålla en användarinloggning till en ensidesapplikation (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: cce76a0e97e039ec6e6c3a976d1fc7caca7fde73
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834442"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Självstudiekurs: Aktivera autentisering av app med en enda sida med konton med hjälp av Azure Active Directory B2C

Den här självstudien lär dig använda Azure Active Directory (Azure AD) B2C för att logga in och registrera användare i en ensidesapp (SPA). Med Azure AD B2C kan appar autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med öppna protokoll.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Registrera ett exempel på en ensidesapp i Azure AD B2C-katalogen.
> * Skapa användarflöden för registrering av användare, inloggning, redigering av profil och återställning av lösenord.
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

## <a name="create-user-flows"></a>Skapa användarflöden

Azure AD B2C-användarflöden definierar användargränssnittet för en identitetsuppgift. Till exempel är vanliga användarflöden att logga in, registrera sig, byta lösenord och redigera profiler.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Skapa ett användarflöde för registrering eller inloggning

Skapa en **användarflöde för registrering eller inloggning** om du vill registrera användare och sedan låta dem logga in på webbappen.

1. På Azure AD B2C-portalsidan väljer du **Användarflöden** och klickar på **Nytt användarflöde**.
2. På fliken **Rekommenderat** klickar du på **Sign up and sign in** (Registrera och logga in).

    Konfigurera användarflödet med följande inställningar:

    ![Lägga till ett användarflöde för registrering eller inloggning](media/active-directory-b2c-tutorials-spa/add-susi-user-flow.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | SiUpIn | Ange ett **Namn** för användarflödet. Användarflödets namn har prefixet **B2C_1_**. Använd det fullständiga användarflödesnamnet **B2C_1_SiUpIn** i exempelkoden. | 
    | **Identitetsprovidrar** | E-postregistrering | Den identitetsprovider som används för att unikt identifiera användaren. |

3. Under **Användarattribut och anspråk** klickar du på **Visa mer** och väljer följande inställningar:

    ![Lägga till ett användarflöde för registrering eller inloggning](media/active-directory-b2c-tutorials-spa/add-attributes-and-claims.png)

    | Kolumn      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Samla in attribut** | Visningsnamn och Postnummer | Välj vilka attribut som samlas in från användaren under registreringen. |
    | **Returanspråk** | Visningsnamn, postnummer, användare är ny, användarens objekt-ID | Välj [anspråk](../active-directory/develop/developer-glossary.md#claim) som ska tas med i [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token). |

4. Klicka på **OK**.
5. Klicka på **Skapa** för att skapa användarflödet. 

### <a name="create-a-profile-editing-user-flow"></a>Skapa ett användarflöde för profilredigering

Om du vill att användarna själva ska kunna återställa informationen i sin användarprofil skapar du ett **användarflöde för profilredigering**.

1. På Azure AD B2C-portalsidan väljer du **Användarflöden** och klickar på **Nytt användarflöde**.
2. På fliken **Rekommenderat** klickar du på **Profilredigering**.

    Konfigurera användarflödet med följande inställningar:

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | SiPe | Ange ett **Namn** för användarflödet. Användarflödets namn har prefixet **B2C_1_**. Använd det fullständiga användarflödesnamnet **B2C_1_SiPe** i exempelkoden. | 
    | **Identitetsprovidrar** | Inloggning på lokalt konto | Den identitetsprovider som används för att unikt identifiera användaren. |

3.  Under **Användarattribut** klickar du på **Visa mer** och väljer följande inställningar:

    | Kolumn      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Samla in attribut** | Visningsnamn och Postnummer | Välj de attribut en användare kan ändra under profilredigering. |
    | **Returanspråk** | Visningsnamn, postnummer, användarens objekt-ID | Välj de [anspråk](../active-directory/develop/developer-glossary.md#claim) du vill ta med i [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token) efter en lyckad profilredigering. |

4. Klicka på **OK**.
5. Klicka på **Skapa** för att skapa användarflödet. 

### <a name="create-a-password-reset-user-flow"></a>Skapa ett användarflöde för återställning av lösenord

Om du vill kunna aktivera lösenordsåterställning i programmet behöver du skapa ett **användarflöde för lösenordsåterställning**. Det här användarflödet beskriver hur lösenordsåterställningen går till för konsumenterna och innehållet i de token som programmet tar emot när återställningen genomförts.

1. På Azure AD B2C-portalsidan väljer du **Användarflöden** och klickar på **Nytt användarflöde**.
2. På fliken **Rekommenderat** klickar du på **Återställning av lösenord**.

    Konfigurera användarflödet med följande inställningar.

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | SSPR | Ange ett **Namn** för användarflödet. Användarflödets namn har prefixet **B2C_1_**. Använd det fullständiga användarflödesnamnet **B2C_1_SSPR** i exempelkoden. | 
    | **Identitetsprovidrar** | Återställ lösenord med e-postadress | Den identitetsprovider som används för att unikt identifiera användaren. |

3. Under **Programanspråk** klickar du på **Visa mer** och väljer följande inställning:

    | Kolumn      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Returanspråk** | Användarens objekt-ID | Välj de [anspråk](../active-directory/develop/developer-glossary.md#claim) du vill ta med i [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token) efter en lyckad lösenordsåterställning. |

4. Klicka på **OK**.
5. Klicka på **Skapa** för att skapa användarflödet. 

## <a name="update-single-page-app-code"></a>Uppdatera kod för ensidesapp

Nu när du har registrerat en app och skapat användarflöden behöver du konfigurera appen så att den använder din Azure AD B2C-katalog. I den här självstudien får konfigurera en SPA JavaScript-app som du kan ladda ned från GitHub. 

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) eller klona exempelwebbappen från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
Exempelappen visar hur en ensidesapp kan använda Azure AD B2C för användarens registrering och inloggning, och anropa ett skyddat webb-API. Du behöver ändra appen så att den använder appregistreringen i din katalog och konfigurera de användarflöden som du skapade. 

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

    Det namn på användarflöde som används i den här självstudien är **B2C_1_SiUpIn**. Om du använder ett annat namn på användarflöde använder du namnet på användarflöde i värdet `authority`.

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

1. Klicka på **Logga in** för att logga in som användare av SPA-appen. Då används användarflödet **B2C_1_SiUpIn**, som du definierade i ett tidigare steg.

2. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du inte har något konto klickar du på länken **Registrera dig**. 

3. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen. 

    ![Arbetsflöde för registrering](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Klicka på **Skapa** och skapa ett lokalt konto i Azure AD B2C-katalogen.

Användaren kan nu använda e-postadressen och logga in och använda SPA-appen.

> [!NOTE]
> Efter inloggningen visar appen ett fel som handlar om ”otillräckliga behörigheter”. Du får felmeddelandet eftersom du försöker komma åt en resurs från demokatalogen. Eftersom ditt åtkomst-token endast är giltigt för din Azure AD-katalog är API-anropet obehörigt. Fortsätt med nästa självstudiekurs och skapa ett skyddat webb-API för din katalog. 

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-katalogen om du vill prova andra självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-katalogen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa en Azure AD B2C-katalog, skapa användarflöden och uppdatera exempelensidesappen så den använder din Azure AD B2C-katalog. Fortsätt till nästa självstudie och lär dig registrera, konfigurera och anropa ett skyddat webb-API från en skrivbordsapp.

> [!div class="nextstepaction"]
> [Kodexempel för Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
