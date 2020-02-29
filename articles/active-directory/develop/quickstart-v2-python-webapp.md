---
title: Lägg till inloggning med Microsoft till en python-webbapp för Microsoft Identity Platform | Azure
description: Lär dig hur du implementerar Microsoft-inloggning på en python-webbapp med OAuth2
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
ms.openlocfilehash: 5a2d6bfe2c58d382aea14b6ca8d4151acb9adfbf
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "78160874"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Snabb start: lägga till inloggning med Microsoft i en python-webbapp

I den här snabb starten får du lära dig hur du integrerar ett python-webbprogram med Microsoft Identity Platform. Din app kommer att logga in en användare, hämta en åtkomsttoken för att anropa Microsoft Graph-API: et och göra en begäran till Microsoft Graph API.

När du har slutfört guiden kommer ditt program att godkänna inloggningar med personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbets-eller skol konton från alla företag eller organisationer som använder Azure Active Directory. (Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.)


## <a name="prerequisites"></a>Förutsättningar

Om du vill köra det här exemplet behöver du:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) eller [python 3 +](https://www.python.org/downloads/release/python-364/)
- [Kolv](http://flask.pocoo.org/), [kolv](https://pythonhosted.org/Flask-Session/), [förfrågningar](https://requests.kennethreitz.org/en/master/)
- [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
>
> Det finns två alternativ för att starta ditt snabb starts program: Express (alternativ 1) och manuell (alternativ 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till [Azure Portal-Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Välj **ny registrering**.
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera din app
>
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>      - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `python-webapp`.
>      - Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
>      - Under avsnittet **omdirigerings-URI** väljer du **webb** plattformen i list rutan och anger värdet till `http://localhost:5000/getAToken`.
>      - Välj **Registrera**. På sidan **Översikt över** appar noterar du **programmets (klient) ID-** värde för senare användning.
> 1. På den vänstra menyn väljer du **certifikat & hemligheter** och klickar på **ny klient hemlighet** i avsnittet **klient hemligheter** :
>
>      - Ange en nyckel Beskrivning (av instansens program hemlighet).
>      - Välj en nyckel varaktighet på om **1 år**.
>      - När du klickar på **Lägg till**visas nyckelvärdet.
>      - Kopiera värdet för nyckeln. Du behöver den senare.
> 1. Välj avsnittet **API-behörigheter**
>
>      - Klicka på knappen **Lägg till en behörighet** och sedan
>      - Se till att fliken **Microsoft API: er** är vald
>      - I avsnittet *vanliga API: er för Microsoft* klickar du på **Microsoft Graph**
>      - I avsnittet **delegerade behörigheter** kontrollerar du att rätt behörigheter är markerade: **User. ReadBasic. all**. Använd Sök fältet om det behövs.
>      - Välj knappen **Lägg till behörigheter**
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
>
> För att kod exemplet för den här snabb starten ska fungera måste du:
>
> 1. Lägg till en svars-URL som `http://localhost:5000/getAToken`.
> 1. Skapa en klient hemlighet.
> 1. Lägg till Microsoft Graph API: s User. ReadBasic. all delegerad behörighet.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) Appen har konfigurerats med det här attributet

#### <a name="step-2-download-your-project"></a>Steg 2: Ladda ned ditt projekt
> [!div renderon="docs"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Hämta projektet och extrahera zip-filen till en lokal mapp närmare rotmappen – till exempel **C:\Azure-samples**
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet]()

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Steg 3: konfigurera programmet
> 
> 1. Extrahera zip-filen i en lokal mapp närmare rotkatalogen, till exempel **C:\Azure-Samples**
> 1. Om du använder en Integrated Development Environment öppnar du exemplet i din favorit IDE (valfritt).
> 1. Öppna filen **app_config. py** , som du hittar i rotmappen och Ersätt med följande kodfragment:
> 
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Där:
>
> - `Enter_the_Application_Id_here` – är program-Id för programmet som du har registrerat.
> - `Enter_the_Client_Secret_Here` – är den **klient hemlighet** som du skapade i **certifikat & hemligheter** för det program som du har registrerat.
> - `Enter_the_Tenant_Name_Here`-är **katalog-ID** -värdet för det program som du har registrerat.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Steg 3: kör kod exemplet

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Steg 4: kör kod exemplet

1. Du måste installera MSAL python Library, kolv Framework, kolv-sessioner för hantering av Server sidan och begär Anden med hjälp av pip på följande sätt:

    ```Shell
    pip install -r requirements.txt
    ```

2. Kör app.py från Shell eller kommando rad:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Det här snabbstartsprogrammet använder en klienthemlighet för att identifiera sig som en konfidentiell klient. Eftersom klienthemligheten läggs till som oformaterad text till dina projektfiler rekommenderar vi att du av säkerhetsskäl använder ett certifikat i stället för en klienthemlighet innan programmet används som produktionsprogram. Mer information om hur du använder ett certifikat finns i [följande instruktioner](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Hämtar MSAL
MSAL är det bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform.
Du kan lägga till MSAL python i ditt program med hjälp av pip.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>MSAL-initiering
Du kan lägga till referensen i MSAL python genom att lägga till följande kod överst i filen där du kommer att använda MSAL:

```Python
import msal
```

## <a name="next-steps"></a>Nästa steg

Läs mer om webbappar som loggar in användare och som anropar webb-API: er:

> [!div class="nextstepaction"]
> [Scenario: webbappar som loggar in användare](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
