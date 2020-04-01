---
title: Microsoft identitetsplattform Python demon | Azure
description: Lär dig hur en Python-process kan få en åtkomsttoken och anropa ett API som skyddas av Microsoft identity platform slutpunkt, med hjälp av appens egen identitet
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Python
ms.openlocfilehash: 3306fe6265d02fda48e3a75540be42e46e8feb89
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473546"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Snabbstart: Skaffa en token och anropa Microsoft Graph API från en Python-konsolapp med appens identitet

I den här snabbstarten skriver du ett Python-program som hämtar en åtkomsttoken med appens identitet och anropar sedan Microsoft Graph API för att visa en [lista över användare](https://docs.microsoft.com/graph/api/user-list) i katalogen. Det här scenariot är användbart för situationer där ett fjärradministrerat, obevakat jobb eller en Windows-tjänst måste köras med en programidentitet, istället för en användares identitet.

> [!div renderon="docs"]
> ![Visar hur exempelappen som genereras av den här snabbstarten fungerar](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Krav

För att kunna köra det här exemplet behöver du:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) eller [Python 3+](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen

> [!div renderon="docs" class="sxs-lookup"]
>
> Du har två alternativ för att starta din snabbstart ansökan: Express (Alternativ 1 nedan), och Manuell (Alternativ 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till den nya [Azure-portalen - fönstret Appregistreringar.](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs)
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. Navigera till sidan Microsoft identity platform för utvecklare [Appregistreringar.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Välj **Ny registrering**.
> 1. När sidan **Registrera en ansökan** visas anger du registreringsinformationen för din ansökan. 
> 1. I avsnittet **Namn** anger du ett beskrivande namn som visas för användare av appen, till exempel `Daemon-console`, och välj sedan **Registrera** för att skapa appen.
> 1. När den har registrerats väljer du menyn **Certifikat och hemligheter**.
> 1. Under **Klienthemligheter** väljer du **+ Ny klienthemlighet**. Ge den ett namn och välj **Lägg till**. Kopiera hemligheten på en säker plats. Du behöver den för att använda i din kod.
> 1. Välj nu menyn **API Behörigheter**, välj **+ Lägg till en behörighet** och välj **Microsoft Graph**.
> 1. Välj **Programbehörigheter**.
> 1. Under noden **Användare** väljer du **User.Read.All** och väljer sedan **Lägg till behörigheter**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Hämta och konfigurera din app för Snabbstart
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> För att kodexemplet för den här snabbstarten ska fungera måste du skapa en klienthemlighet och lägga till Graph-API:ts programbehörighet **User.Read.All**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-netcore-daemon/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-your-python-project"></a>Steg 2: Ladda ned ditt Python-projekt

> [!div renderon="docs"]
> [Ladda ner Python-demonprojektet](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ner kodexemplet](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-python-project"></a>Steg 3: Konfigurera python-projektet
> 
> 1. Extrahera zip-filen i en lokal mapp nära diskens rot, till exempel **C:\Azure-Samples**.
> 1. Navigera till undermappen **1-Call-MsGraph-WithSecret"**.
> 1. Redigera **parameters.json** och ersätt värdena i fälten `authority`och `client_id` `secret` med följande utdrag:
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    Där:
>    - `Enter_the_Application_Id_Here` – är **program-ID (klient)** för programmet som du har registrerat.
>    - `Enter_the_Tenant_Id_Here` – ersätt det här värdet med **klient-ID** eller **klientnamn** (t.ex. contoso.microsoft.com)
>    - `Enter_the_Client_Secret_Here` – ersätt det här värdet med klienthemligheten som skapades i steg 1.
>
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och går du till appens **översiktssida** i Azure-portalen. Generera en ny nyckel genom att gå till sidan **Certifikat och hemligheter**.
    
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Steg 3: Administratörssamtycke

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Steg 4: Administratörsmedgivande

Om du försöker köra programmet på denna punkt, får du *HTTP 403 - Förbjudet* fel: `Insufficient privileges to complete the operation`. Det här felet beror på att alla *behörigheter endast för appar* kräver administratörsgodkännande: en global administratör för din katalog måste ge ditt samtycke till ditt program. Välj ett av alternativen nedan beroende på din roll:

##### <a name="global-tenant-administrator"></a>Global innehavaradministratör

> [!div renderon="docs"]
> Om du är global klientadministratör går du till sidan **API-behörigheter** i Programregistrering (förhandsversion) i Azure-portalen och väljer **Bevilja administratörsmedgivande för {klientnamn}** (där {klientnamn} är namnet på din katalog).

> [!div renderon="portal" class="sxs-lookup"]
> Om du är global administratör går du till sidan **API-behörigheter** och väljer **Bevilja administratörsmedgivande för Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Gå till sidan API-behörigheter]()

##### <a name="standard-user"></a>Standardanvändare

Om du är en standardanvändare av din klientorganisation måste du be en global administratör att ge administratörens medgivande för ditt program. Gör detta genom att ge följande URL till administratören:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Där:
>> * `Enter_the_Tenant_Id_Here` – ersätt det här värdet med **klient-ID** eller **klientnamn** (t.ex. contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` – är **program-ID (klient)** för programmet som du har registrerat.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Steg 4: Kör programmet

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Steg 5: Köra appen

Du måste installera beroenden för det här exemplet en gång

```console
pip install -r requirements.txt
```

Kör sedan programmet via kommandotolken eller konsolen:

```console
python confidential_client_secret_sample.py parameters.json
```

Du bör se på konsolen utdata några Json fragment som representerar en lista över användare i din Azure AD katalog.

> [!IMPORTANT]
> Det här snabbstartsprogrammet använder en klienthemlighet för att identifiera sig som en konfidentiell klient. Eftersom klienthemligheten läggs till som oformaterad text till dina projektfiler rekommenderar vi att du av säkerhetsskäl använder ett certifikat i stället för en klienthemlighet innan programmet används som produktionsprogram. Mer information om hur du använder ett certifikat finns i [de här anvisningarna](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) i samma GitHub-databas för det här exemplet, men i den andra mappen **2-Call-MsGraph-WithCertificate**

## <a name="more-information"></a>Mer information

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) är det bibliotek som används för att logga in användare och begära token som används för att komma åt ett API som skyddas av Microsofts identitetsplattform. Som beskrivits begär den här snabbstarten token med hjälp av programmets egen identitet i stället för delegerade behörigheter. Autentiseringsflödet som används i det här fallet kallas för *[oauth-flöde för klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md)*. Mer information om hur du använder MSAL Python med daemonappar finns i [den här artikeln](scenario-daemon-overview.md).

 Du kan installera MSAL Python genom att köra följande pip-kommando.

```powershell
pip install msal
```

### <a name="msal-initialization"></a>MSAL-initiering

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```Python
import msal
```

Initiera sedan MSAL med hjälp av följande kod:

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | Där: ||
> |---------|---------|
> | `config["secret"]` | Är klienthemligheten som skapats för appen i Azure-portalen. |
> | `config["client_id"]` | Är **Program-ID (klient)** för det program som registrerats på Azure-portalen. Du hittar det här värdet på appens **översiktssida** på Azure-portalen. |
> | `config["authority"]`    | STS-slutpunkten för autentisering av användaren. Vanligtvis <https://login.microsoftonline.com/{tenant}> för offentligt moln, där {klient} är namnet på klientorganisationen eller klient-ID:t.|

Mer information finns i [referensdokumentationen för `ConfidentialClientApplication`](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)

### <a name="requesting-tokens"></a>Begära token

Om du vill begära en token med appens identitet använder du metoden `AcquireTokenForClient`:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Där:| |
> |---------|---------|
> | `config["scope"]` | Innehåller omfattningarna som begärdes. För konfidentiella klienter bör ett format som liknar `{Application ID URI}/.default` användas för att ange att omfattningarna som begärs är dem som statiskt definieras i appobjektet som anges i Azure-portalen (för Microsoft Graph, `{Application ID URI}` pekar på `https://graph.microsoft.com`). För anpassade webb-API:er definieras `{Application ID URI}` under avsnittet **Exponera ett API** i Programregistrering (förhandsversion) i Azure-portalen. |

Mer information finns i [referensdokumentationen för `AcquireTokenForClient`](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om daemon-program finns på scenariomålsidan

> [!div class="nextstepaction"]
> [Daemon program som anropar webb-API: er](scenario-daemon-overview.md)

För handledning av demonprogrammet finns i:

> [!div class="nextstepaction"]
> [Daemon Python konsol handledning](https://github.com/Azure-Samples/ms-identity-python-daemon)

Lär dig mer om behörigheter och medgivande:

> [!div class="nextstepaction"]
> [Behörigheter och samtycke](v2-permissions-and-consent.md)

Mer information om autentiseringsflödet för det här scenariot finns i Oauth 2.0-flödet för klientautentiseringsuppgifter:

> [!div class="nextstepaction"]
> [Oauth-flöde för klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md)

Hjälp oss att förbättra Microsofts identitetsplattform. Berätta vad du tycker genom att fylla i en kort två-fråga undersökning.

> [!div class="nextstepaction"]
> [Undersökning av Microsofts identitetsplattform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
