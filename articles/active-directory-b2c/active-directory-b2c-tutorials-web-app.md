---
title: "Använda Azure Active Directory B2C för användarautentisering i en ASP.NET webbapp – självstudier"
description: "Självstudie som lär dig använda Azure Active Directory B2C för att tillhandahålla en användarinloggning till en ASP.NET-webbapp."
services: active-directory-b2c
author: PatAltimore
ms.author: patricka
ms.reviewer: saraford
ms.date: 1/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: 1d1e0ce51d86ebcdbf0a2a423ff64b3814413d86
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-authenticate-users-with-azure-active-directory-b2c-in-an-aspnet-web-app"></a>Självstudier: Autentisera användare med Azure Active Directory B2C i en ASP.NET webbapp

Den här självstudien lär dig använda Azure Active Directory (Azure AD) B2C för att logga in och registrera användare i en ASP.NET webbapp. Med Azure AD B2C kan appar autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med öppna protokoll.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Registrera en ASP.NET exempelwebbapp i en Azure AD B2C-klientorganisation.
> * Skapa principer för registrering av användare, inloggning, redigering av profil och återställning av lösenord.
> * Konfigurera exempelwebbappen så att den använder din Azure AD B2C-klientorganisation. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Skapa en egen [Azure AD B2C-klientorganisation](active-directory-b2c-get-started.md)
* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med arbetsbelastningen **ASP.NET och webbutveckling**.

## <a name="register-web-app"></a>Registrera webbappen

Program måste [registreras](../active-directory/develop/active-directory-dev-glossary.md#application-registration) i klientorganisationen innan de kan ta emot [åtkomsttoken](../active-directory/develop/active-directory-dev-glossary.md#access-token) från Azure Active Directory. Programregistrering skapar ett [program-ID](../active-directory/develop/active-directory-dev-glossary.md#application-id-client-id) för appen i din klientorganisation. 

Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Välj **Azure AD B2C** i listan över tjänster i Azure Portal.

2. I B2C-inställningarna klickar du på **Program** och sedan på **Lägg till**.

    Registrera exempelwebbappen i klientorganisationen med följande inställningar:

    ![Lägg till en ny app](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | Min exempelwebbapp | Ange ett **Namn** som beskriver appen för konsumenterna. | 
    | **Ta med webbapp/webb-API** | Ja | Välj **Ja** om det är en webbapp. |
    | **Tillåt implicit flöde** | Ja | Välj **Ja** eftersom appen använder [OpenID Connect-inloggning](active-directory-b2c-reference-oidc.md). |
    | **Svarswebbadress** | `https://localhost:44316` | Svarswebbadresser är slutpunkter där Azure AD B2C returnerar de token som appen begär. I den här självstudien körs exemplet lokalt (lokal värd) och lyssnar på port 44316. |
    | **Inbyggd klient** | Nej | Eftersom det här är en webbapp och inte en intern klient väljer du Nej. |

3. Klicka på **Skapa** för att registrera din app.

Registrerade appar visas i programlistan för Azure AD B2C-klientorganisationen. Välj webbapp i listan. Webbappens egenskapsruta visas.

![Webbappegenskaper](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

Anteckna det **Programklients-id** som visas. Detta ID identifierar appen och behövs när appen konfigureras senare under självstudierna.

### <a name="create-a-client-password"></a>Skapa ett klientlösenord

Azure AD B2C använder OAuth2-auktorisering för [klientprogram](../active-directory/develop/active-directory-dev-glossary.md#client-application). Webbappar är [konfidentiella klienter](../active-directory/develop/active-directory-dev-glossary.md#web-client) och kräver en klienthemlighet (lösenord). Programklients-id och klienthemlighet används när webbappen autentiserar med Azure Active Directory. 

1. Välj sidan Nycklar för den registrerade webbappen och klicka på **Generera nyckel**.

2. Klicka på **Spara** och visa nyckeln.

    ![sidan nycklar för allmänna appar](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

Nyckeln visas en gång i portalen. Det är viktigt att nyckelvärdet kopieras och sparas. Värdet behövs när appen konfigureras. Förvara nyckeln säkert. Dela inte nyckeln offentligt.

## <a name="create-policies"></a>Skapa principer

En Azure AD B2C-princip definierar arbetsflöden för användare. Vanliga arbetsflöden är att logga in, registrera sig, byta lösenord och redigera profiler.

### <a name="create-a-sign-up-or-sign-in-policy"></a>Skapa en registrerings- eller inloggningsprincip

Skapa en **registrerings- eller inloggningsprincip** om du vill registrera användare och sedan låta dem logga in på webbappen.

1. I Azure AD B2C-portalsidan väljer du **registrerings- eller inloggningsprinciper** och klickar på **Lägg till**.

    Konfigurerar principen med följande inställningar:

    ![Lägg till en registrerings- eller inloggningsprincip](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | SiUpIn | Ange ett **Namn** för principen. Principens namn har prefixet **b2c_1_**. Använd det fullständiga principnamnet **b2c_1_SiUpIn** i exempelkoden. | 
    | **Identitetsprovider** | E-postregistrering | Den identitetsprovider som används för att unikt identifiera användaren. |
    | **Registreringsattribut** | Visningsnamn och Postnummer | Välj vilka attribut som samlas in från användaren under registreringen. |
    | **Programanspråk** | Visningsnamn, postnummer, användare är ny, användarens objekt-ID | Välj [anspråk](../active-directory/develop/active-directory-dev-glossary.md#claim) som ska tas med i [åtkomsttoken](../active-directory/develop/active-directory-dev-glossary.md#access-token). |

2. Klicka på **Skapa** för att skapa principen. 

### <a name="create-a-profile-editing-policy"></a>Skapa en profilredigeringsprincip

Om du vill att användarna själva ska kunna återställa informationen i sin användarprofil skapar du en **profilredigeringsprincip**.

1. I Azure AD B2C-portalsidan väljer du **profilredigeringsprinciper** och klickar på **Lägg till**.

    Konfigurerar principen med följande inställningar:

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | SiPe | Ange ett **Namn** för principen. Principens namn har prefixet **b2c_1_**. Använd det fullständiga principnamnet **b2c_1_SiPe** i exempelkoden. | 
    | **Identitetsprovider** | Inloggning på lokalt konto | Den identitetsprovider som används för att unikt identifiera användaren. |
    | **Profilattribut** | Visningsnamn och Postnummer | Välj de attribut en användare kan ändra under profilredigering. |
    | **Programanspråk** | Visningsnamn, postnummer, användare är ny, användarens objekt-ID | Välj de [anspråk](../active-directory/develop/active-directory-dev-glossary.md#claim) du vill ta med i [åtkomsttoken](../active-directory/develop/active-directory-dev-glossary.md#access-token) efter en lyckad profilredigering. |

2. Klicka på **Skapa** för att skapa principen. 

### <a name="create-a-password-reset-policy"></a>Skapa en princip för återställning av lösenord

Om du vill kunna aktivera lösenordsåterställning i programmet måste du skapa en **princip för lösenordsåterställning**. Principen beskriver hur lösenordsåterställningen går till för konsumenterna och innehållet i de token som programmet tar emot när återställningen genomförts.

1. I Azure AD B2C-portalsidan väljer du **Princip för lösenordsåterställning** och klickar på **Lägg till**.

    Konfigurera principen med följande inställningar.

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | SSPR | Ange ett **Namn** för principen. Principens namn har prefixet **b2c_1_**. Använd det fullständiga principnamnet **b2c_1_SSPR** i exempelkoden. | 
    | **Identitetsprovider** | Återställ lösenord med e-postadress | Den identitetsprovider som används för att unikt identifiera användaren. |
    | **Programanspråk** | Användarens objekt-ID | Välj de [anspråk](../active-directory/develop/active-directory-dev-glossary.md#claim) du vill ta med i [åtkomsttoken](../active-directory/develop/active-directory-dev-glossary.md#access-token) efter en lyckad lösenordsåterställning. |

2. Klicka på **Skapa** för att skapa principen. 

## <a name="update-web-app-code"></a>Uppdatera webbappens kod

När du nu registrerat en webbapp och skapat principer måste du konfigurera appen så att den använder din Azure AD B2C-klientorganisation. I den här självstudien konfigurerar du en exempelwebbapp. 

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) eller klona exempelwebbappen från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

ASP.NET exempelwebbappen är en enkel app som kan skapa och uppdatera en uppgiftslista. Appen använder [Microsoft OWIN mellanprogramkomponenter](https://docs.microsoft.com/en-us/aspnet/aspnet/overview/owin-and-katana/) och låter användare registrera sig för att använda appen i din Azure AD B2C-klientorganisation. Genom att skapa en Azure AD B2C-princip kan användare använda ett konto för sociala media eller skapa ett konto för att kunna använda appen. 

Exempellösningen innehåller två projekt:

**Exempelwebbapp (TaskWebApp):** webbapp som skapar och redigerar en uppgiftslista. Webbappen använder **registrerings- eller inloggningsprincipen** för att registrera eller logga in användare.

**Webb-API-exempelapp (TaskService):** Webb-API med stöd för att skapa, läsa, uppdatera och ta bort en uppgiftslista. Webb-API:n skyddas av Azure AD B2C och anropas av webbappen.

Appen måste ändras så den använder appregistreringen i din klientorganisation. Du måste även konfigurera de principer du ställde in. Exempelwebbappen definierar konfigurationsvärdena som appinställningar i Web.config-filen. Så här ändrar du appinställningarna:

1. Öppna **B2C-WebAPI-DotNet**-lösningen i Visual Studio.

2. I webbapprojektet **TaskWebApp** öppnar du **Web.config**-filen och gör följande uppdateringar:

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    
    <add key="ida:ClientId" value="The Application ID for your web app registered in your tenant" />
    
    <add key="ida:ClientSecret" value="Client password (client secret)" />
    ```
3. Uppdatera principinställningarna med det namn som skapades när du skapade principerna.

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="b2c_1_SiUpIn" />
    <add key="ida:EditProfilePolicyId" value="b2c_1_SiPe" />
    <add key="ida:ResetPasswordPolicyId" value="b2c_1_SSPR" />
    ```

## <a name="run-the-sample-web-app"></a>Kör exempelwebbappen

Högerklicka på **TaskWebApp**-projektet i Solution Explorer och klicka på **Ställ in som startprojekt**

Starta webbappen genom att trycka på **F5**. Standardwebbläsaren öppnas med den lokala webbplatsadressen `https://localhost:44316/`. 

Exempelappen har stöd för registrering av användare, inloggning, redigering av profil och återställning av lösenord. Den här självstudien visar hur en användare registrerar sig för att använda programmet med en e-postadress. Du kan själv prova andra scenarion.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Klicka på länken **Registrera dig eller Logga in** längst upp och registrera dig som användare. Principen **b2c_1_SiUpIn** som du definierade i ett tidigare steg används.

2. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du inte har något konto klickar du på länken **Registrera dig**. 

3. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som begärs i principen.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen. 

    ![Arbetsflöde för registrering](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Klicka på **Skapa** och skapa ett lokalt konto i Azure AD B2C-klientorganisationen.

Användaren kan nu använda e-postadressen och logga in och använda webbappen.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-klientorganisationen om du vill prova andra självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-klientorganisationen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa en Azure AD B2C-klientorganisation, att skapa principer och uppdatera exempelwebbappen så den använder din Azure AD B2C-klientorganisation. Fortsätt till nästa självstudie och lär dig registrera, konfigurera och anropa ett ASP.NET webb-API som skyddas av din Azure AD B2C-klientorganisation.

> [!div class="nextstepaction"]
> [Självstudie: Använda Azure Active Directory B2C för att skydda ett ASP.NET webb-API](active-directory-b2c-tutorials-web-api.md)
