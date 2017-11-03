---
title: "Azure AD v2 JS SPA interaktiv installation - inställningar | Microsoft Docs"
description: "Hur JavaScript SPA program anropar en API som kräver åtkomst-token i Azure Active Directory v2 slutpunkten"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: fc9f88cc8d23abcfa8ea30e346192732b422ffa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
## <a name="setting-up-your-web-server-or-project"></a>Konfigurera webbservern eller projekt

> Om du vill hämta den här exempelprojektet i stället? 
> - [Hämta Visual Studio-projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> eller
> - [Hämta projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) för en lokal webbserver, till exempel Python
>
> Och gå sedan till den [konfigurationssteget](#create-an-application-express) konfigurera kodexemplet innan den körs.

## <a name="prerequisites"></a>Krav
En lokal webbserver som [Python http.server](https://www.python.org/downloads/), [http-server](https://www.npmjs.com/package/http-server/), [.NET Core](https://www.microsoft.com/net/core), eller IIS Express integrering med [Visual Studio 2017](https://www.visualstudio.com/downloads/) krävs för att köra interaktiva installationen. 

Anvisningarna i guiden är baserade på både Python och Visual Studio 2017 men passa på att använda något annat utvecklingsmiljö eller webbserver.

## <a name="create-your-project"></a>Skapa projektet 

> ### <a name="option-1-visual-studio"></a>Alternativ 1: Visual Studio 
> Om du använder Visual Studio och skapar ett nytt projekt, Följ stegen nedan för att skapa en ny Visual Studio-lösning:
> 1.    I Visual Studio:`File` > `New` > `Project`
> 2.    Under `Visual C#\Web`väljer`ASP.NET Web Application (.NET Framework)`
> 3.    Namnge ditt program och klicka på *OK*
> 4.    Under `New ASP.NET Web Application`väljer`Empty`

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>Alternativ 2: Python / andra webbservrar
> Kontrollera att du har installerat [Python](https://www.python.org/downloads/), följ instruktionerna nedan:
> - Skapa en mapp som värd för ditt program.


## <a name="create-your-single-page-applications-ui"></a>Skapa sida programmets användargränssnitt
1.  Skapa en *index.html* -filen för JavaScript-SPA. Om du använder Visual Studio, Välj projekt (rotmapp projekt), högerklicka och välj: `Add`  >  `New Item`  >  `HTML page` och ge den namnet index.html
2.  Lägg till följande kod på sidan:
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.1/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
