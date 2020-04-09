---
title: Lägga till inloggning med Microsoft i en Microsoft identity platform Python-webbapp | Azure
description: Lär dig hur du implementerar Microsoft-inloggning i en Python Web App med OAuth2
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 0affae56ef6998efe4bb370287ff3688f83f3878
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873964"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Snabbstart: Lägga till inloggning med Microsoft i en Python-webbapp

I den här snabbstarten får du lära dig hur du integrerar ett Python-webbprogram med Microsofts identitetsplattform. Din app loggar in en användare, får en åtkomsttoken för att anropa Microsoft Graph API och göra en begäran till Microsoft Graph API.

När du har slutfört guiden accepterar programmet inloggningar av personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbets- eller skolkonton från alla företag eller organisationer som använder Azure Active Directory. (Se [Hur exemplet fungerar](#how-the-sample-works) för en illustration.)


## <a name="prerequisites"></a>Krav

För att kunna köra det här exemplet behöver du:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) eller [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Kolv,](http://flask.pocoo.org/) [kolv-session,](https://pythonhosted.org/Flask-Session/) [förfrågningar](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
>
> Du har två alternativ för att starta snabbstartsprogrammet: express (alternativ 1) och manuell (alternativ 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till [Azure-portalen - Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs).
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ instruktionerna för att hämta och konfigurera ditt nya program automatiskt.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. Navigera till sidan Microsoft identity platform för utvecklare [Appregistreringar.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Välj **Ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>      - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `python-webapp`.
>      - Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
>      - Välj **Registrera**.
>      - På sidan Översikt **över** appen noterar du **värdet program (klient)** för senare användning.
> 1. Välj **Autentisering** på menyn och lägg sedan till följande information:
>    - Lägg **Web** till webbplattformskonfigurationen. Lägg `http://localhost:5000/getAToken` till som **omdirigerings-URI:er**.
>    - Välj **Spara**.
> 1. På menyn till vänster väljer du **Certifikat & hemligheter** och klickar på Ny **klienthemlighet** i avsnittet **Klienthemligheter:**
>
>      - Skriv en nyckelbeskrivning (av instansapphemlighet).
>      - Välj en nyckellängd **på 1 år**.
>      - När du klickar på **Lägg till**visas nyckelvärdet.
>      - Kopiera nyckelns värde. Du behöver den senare.
> 1. Välj avsnittet **API-behörigheter**
>
>      - Klicka på knappen **Lägg till en behörighet** och sedan
>      - Kontrollera att fliken **Microsoft API:er** är markerad
>      - I avsnittet *Vanliga Microsoft API:er* klickar du på **Microsoft Graph**
>      - Kontrollera att rätt **behörigheter kontrolleras:** **User.ReadBasic.All**. Använd sökrutan om det behövs.
>      - Välj knappen **Lägg till behörigheter**
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
>
> För att kodexemplet för snabbstart ska fungera måste du:
>
> 1. Lägg till en `http://localhost:5000/getAToken`svars-URL som .
> 1. Skapa en klienthemlighet.
> 1. Lägg till Microsoft Graph API:s User.ReadBasic.All delegerad behörighet.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) appen konfigureras med det här attributet

#### <a name="step-2-download-your-project"></a>Steg 2: Ladda ned ditt projekt
> [!div renderon="docs"]
> [Ladda ner kodexemplet](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Hämta projektet och extrahera zip-filen till en lokal mapp närmare rotmappen , till exempel **C:\Azure-Samples**
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ner kodexemplet](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Steg 3: Konfigurera programmet
>
> 1. Extrahera zip-filen i en lokal mapp närmare rotkatalogen, till exempel **C:\Azure-Samples**
> 1. Om du använder en integrerad utvecklingsmiljö öppnar du exemplet i din favorit-IDE (valfritt).
> 1. Öppna filen **app_config.py,** som finns i rotmappen och ersätt med följande kodavsnitt:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Där:
>
> - `Enter_the_Application_Id_here` – är program-Id för programmet som du har registrerat.
> - `Enter_the_Client_Secret_Here`- är **client secret** du skapade i certifikat & **hemligheter** för det program du registrerade.
> - `Enter_the_Tenant_Name_Here`- är **värdet för katalog -ID för** det program du registrerade.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Steg 3: Kör kodexemplet

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Steg 4: Kör kodexemplet

1. Du måste installera MSAL Python-bibliotek, Flask-ramverk, Flask-Sessions för sessionshantering på serversidan och begäranden med pip enligt följande:

    ```Shell
    pip install -r requirements.txt
    ```

2. Kör app.py från skal eller kommandorad:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Det här snabbstartsprogrammet använder en klienthemlighet för att identifiera sig som en konfidentiell klient. Eftersom klienthemligheten läggs till som oformaterad text till dina projektfiler rekommenderar vi att du av säkerhetsskäl använder ett certifikat i stället för en klienthemlighet innan programmet används som produktionsprogram. Mer information om hur du använder ett certifikat finns i [de här anvisningarna](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Visar hur exempelappen som genereras av den här snabbstarten fungerar](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Få MSAL
MSAL är det bibliotek som används för att logga in användare och begära token som används för att komma åt ett API som skyddas av Microsoft identity Platform.
Du kan lägga till MSAL Python i ditt program med Pip.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>MSAL-initiering
Du kan lägga till referensen till MSAL Python genom att lägga till följande kod högst upp i filen där du ska använda MSAL:

```Python
import msal
```

## <a name="next-steps"></a>Nästa steg

Läs mer om webbappar som loggar in användare och sedan anropar webb-API:er:

> [!div class="nextstepaction"]
> [Scenario: Webbappar som loggar in användare](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
