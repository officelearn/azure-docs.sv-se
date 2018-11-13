---
title: Självstudiekurs – Aktivera autentisering av skrivbordsapp med konton med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Självstudiekurs som lär dig använda Azure Active Directory B2C för att tillhandahålla användarinloggning till en .NET-webbapp.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 2/28/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: a7a861ccff168655d866d8c9205160bface79c9e
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913418"
---
# <a name="tutorial-enable-desktop-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Självstudiekurs: Aktivera autentisering av skrivbordsapp med konton med hjälp av Azure Active Directory B2C

I den här självstudien lär du dig använda Azure Active Directory (Azure AD) B2C för att logga in och registrera användare i en WFP-skrivbordsapp (Windows Presentation Foundation). Med Azure AD B2C kan appar autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med öppna protokoll.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Registrera exempelskrivbordsappen i din Azure AD B2C-klientorganisation.
> * Skapa principer för registrering av användare, inloggning, redigering av profil och återställning av lösenord.
> * Konfigurera exempelprogrammet så att det använder din Azure AD B2C-klientorganisation.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Skapa en egen [Azure AD B2C-klientorganisation](active-directory-b2c-get-started.md)
* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med **.NET-skrivbordsutveckling** och arbetsbelastningarna **ASP.NET och webbutveckling**.

## <a name="register-desktop-app"></a>Registrera skrivbordsapp

Program måste [registreras](../active-directory/develop/developer-glossary.md#application-registration) i klientorganisationen innan de kan ta emot [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token) från Azure Active Directory. Programregistrering skapar ett [program-ID](../active-directory/develop/developer-glossary.md#application-id-client-id) för appen i din klientorganisation. 

Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Välj **Azure AD B2C** i listan över tjänster i Azure Portal. 

2. I B2C-inställningarna klickar du på **Program** och sedan på **Lägg till**. 

    Registrera exempelwebbappen i klientorganisationen med följande inställningar:
    
    ![Lägg till en ny app](media/active-directory-b2c-tutorials-desktop-app/desktop-app-registration.png)
    
    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | Min WPF-exempelapp | Ange ett **Namn** som beskriver appen för konsumenterna. | 
    | **Ta med webbapp/webb-API** | Nej | Välj **Nej** för en skrivbordsapp. |
    | **Inkludera intern klient** | Ja | Eftersom detta är en skrivbordsapp och betraktas som en intern klient. |
    | **Omdirigerings-URI** | Standardvärden | Unik identifierare till vilken Azure AD B2C dirigerar om användaragenten i ett OAuth 2.0-svar. |
    | **Anpassad omdirigerings-URI** | `com.onmicrosoft.contoso.appname://redirect/path` | Ange sändningstoken för `com.onmicrosoft.<your tenant name>.<any app name>://redirect/path`-principer till denna URI. |
    
3. Klicka på **Skapa** för att registrera din app.

Registrerade appar visas i programlistan för Azure AD B2C-klientorganisationen. Välj din skrivbordsapp i listan. Den registrerade skrivbordsappens egenskapsfönster visas.

![Egenskaper för skrivbordsapp](./media/active-directory-b2c-tutorials-desktop-app/b2c-desktop-app-properties.png)

Anteckna det **Programklients-id** som visas. Detta ID identifierar appen och behövs när appen konfigureras senare under självstudierna.

## <a name="create-policies"></a>Skapa principer

En Azure AD B2C-princip definierar arbetsflöden för användare. Vanliga arbetsflöden är att logga in, registrera sig, byta lösenord och redigera profiler.

### <a name="create-a-sign-up-or-sign-in-policy"></a>Skapa en registrerings- eller inloggningsprincip

Skapa en **registrerings- eller inloggningsprincip** om du vill registrera användare och sedan låta dem logga in i skrivbordsappen.

1. I Azure AD B2C-portalsidan väljer du **registrerings- eller inloggningsprinciper** och klickar på **Lägg till**.

    Konfigurerar principen med följande inställningar:

    ![Lägg till en registrerings- eller inloggningsprincip](media/active-directory-b2c-tutorials-desktop-app/add-susi-policy.png)

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

## <a name="update-desktop-app-code"></a>Uppdatera skrivbordsappens kod

Nu när du har registrerat en skrivbordsapp och skapat principer måste du konfigurera appen så att den använder din Azure AD B2C-klientorganisation. I den här självstudien konfigurerar du en exempelskrivbordsapp. 

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [bläddra på lagringsplatsen](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Exemplet på WPF-skrivbordsapp visar hur en skrivbordsapp kan använda Azure AD B2C för användarens registrering och inloggning, och anropa ett skyddat webb-API.

Appen måste ändras så den använder appregistreringen i din klientorganisation. 

Så här ändrar du appinställningarna:

1. Öppna därefter `active-directory-b2c-wpf`-lösningen i Visual Studio.

2. I projektet `active-directory-b2c-wpf` öppnar du filen **App.xaml.cs** filen och gör följande uppdateringar:

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "The Application ID for your desktop app registered in your tenant";
    ```

3. Uppdatera variabeln **PolicySignUpSignIn** med namnet på den *princip för registrering eller inloggning* som du skapade i föregående steg. Kom ihåg att inkludera prefixet *B2C_1_*.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_SiUpIn";
    ```

## <a name="run-the-sample-desktop-application"></a>Köra exempelskrivbordsappen

Tryck på **F5** för att skapa och köra skrivbordsappen. 

Exempelappen har stöd för registrering av användare, inloggning, redigering av profil och återställning av lösenord. Den här självstudien visar hur en användare registrerar sig för att använda programmet med en e-postadress. Du kan själv prova andra scenarion.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Klicka på knappen **Logga in** om du vill logga in som användare av skrivbordsappen. Då används principen **B2C_1_SiUpIn** som du definierade i ett tidigare steg.

2. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du inte har något konto klickar du på länken **Registrera dig**. 

3. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som begärs i principen.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen. 

    ![Arbetsflöde för registrering](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Klicka på **Skapa** och skapa ett lokalt konto i Azure AD B2C-klientorganisationen.

Användaren kan nu använda e-postadressen för att logga in och använda skrivbordsappen.

> [!NOTE]
> Om du klickar på knappen **Anropa API** får du felmeddelandet ”Obehörig”. Du får felmeddelandet eftersom du försöker komma åt en resurs från demoklientorganisationen. Eftersom ditt åtkomst-token endast är giltigt för din Azure AD-klientorganisation är API-anropet obehörigt. Fortsätt med nästa självstudiekurs och skapa ett skyddat webb-API för din klientorganisation. 

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-klientorganisationen om du vill prova andra självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-klientorganisationen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa en Azure AD B2C-klientorganisation, skapa principer och uppdatera exempelskrivbordsappen så att den använder din Azure AD B2C-klientorganisation. Fortsätt till nästa självstudie och lär dig registrera, konfigurera och anropa ett skyddat webb-API från en skrivbordsapp.

> [!div class="nextstepaction"]
> 
