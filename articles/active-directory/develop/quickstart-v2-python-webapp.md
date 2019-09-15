---
title: Snabb start för Microsoft Identity Platform python Web App | Azure
description: Lär dig hur du implementerar Microsoft-inloggning på en python-webbapp med OAuth2
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 682582c8c695550f7dfdfcc079e1d0bf04828180
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997378"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Snabbstart: Lägg till inloggning med Microsoft i en python-webbapp

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

I den här snabb starten får du lära dig hur du integrerar ett python-webbprogram med Microsoft Identity Platform. Din app kommer att logga in en användare, hämta en åtkomsttoken för att anropa Microsoft Graph-API: et och göra en begäran till Microsoft Graph API.

När du har slutfört guiden kommer ditt program att godkänna inloggningar med personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbets-eller skol konton från alla företag eller organisationer som använder Azure Active Directory.

![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Förutsättningar

Om du vill köra det här exemplet behöver du:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) eller [python 3 +](https://www.python.org/downloads/release/python-364/)
- [Kolv](http://flask.pocoo.org/), [kolv](https:/pythonhosted.org/Flask-Session/), [förfrågningar](https://2.python-requests.org/en/master/)
- [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python) 
- En Azure Active Directory-klient (Azure AD). Mer information om hur du skaffar en Azure AD-klient finns i [så här skaffar du en Azure AD-klient.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

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
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
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
>      - Under avsnittet omdirigerings- **URI** väljer du **webb** plattformen i list rutan och anger värdet till `http://localhost:5000/getAToken`.
>      - Välj **Registrera**. På sidan **Översikt över** appar noterar du **programmets (klient) ID-** värde för senare användning.
> 1. På den vänstra menyn väljer du **certifikat & hemligheter** och klickar på **ny klient hemlighet** i avsnittet **klient hemligheter** :
>
>      - Ange en nyckel Beskrivning (av instansens program hemlighet).
>      - Välj en nyckel varaktighet på om **1 år**.
>      - När du klickar på **Lägg till**visas nyckelvärdet.
>      - Kopiera värdet för nyckeln. Du behöver den senare.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Steg 1: Konfigurera din app på Azure Portal
>
> För att kod exemplet för den här snabb starten ska fungera måste du:
>
> 1. Lägg till en svars `http://localhost:5000/getAToken`-URL som.
> 1. Skapa en klient hemlighet.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-aspnet-webapp/green-check.png) appen konfigureras med det här attributet

#### <a name="step-2-download-your-project"></a>Steg 2: Ladda ned ditt projekt

[Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>Steg 3: Konfigurera programmet

1. Extrahera zip-filen i en lokal mapp närmare rotkatalogen, till exempel **C:\Azure-Samples**
1. Om du använder en Integrated Development Environment öppnar du exemplet i din favorit IDE (valfritt).
1. Öppna filen **app_config. py** , som du hittar i rotmappen och Ersätt med följande kodfragment:

```python
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
SCOPE = ["https://graph.microsoft.com/User.Read"]
REDIRECT_URI = "http://localhost:5000/getAToken"
```

> [!div renderon="docs"]
> Där:
>
> - `Enter_the_Application_Id_here` – är program-Id för programmet som du har registrerat.
> - `Enter_the_Tenant_Info_Here` – är ett av alternativen nedan:
>   - Om ditt program **endast stöder min organisation**ersätter du värdet med **klient-ID** eller **klient namn** (till exempel contoso.onmicrosoft.com)
>   - Om ditt program stöder **Konton i valfri organisationskatalog** ersätter du värdet med `organizations`
>   - Om ditt program stöder **Alla Microsoft-kontoanvändare** ersätter du värdet med `common`
> - `Enter_the_Client_Secret_Here`– är den **klient hemlighet** som du skapade i **certifikat & hemligheter** för det program som du har registrerat.

#### <a name="step-4-run-the-code-sample"></a>Steg 4: Kör kod exemplet

- Du måste installera MSAL python Library, kolv Framework, kolv-sessioner för hantering av Server sidan och begär Anden med hjälp av pip på följande sätt:

```Shell
pip install msal
pip install flask
pip install Flask-Session
pip install requests
```

- Om miljövariabeln för kolv redan har angetts: Kör app.py från Shell eller kommando rad:

```Shell
python app.py
```

- Om miljövariabeln för flaska inte har angetts:

    1. Skriv följande kommandon på Shell eller kommando rad genom att gå till projekt katalogen:

```Shell
export FLASK_APP=app.py
export FLASK_DEBUG=1
flask run
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
