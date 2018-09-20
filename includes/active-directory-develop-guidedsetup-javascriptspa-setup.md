---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: cc5272ed133f4fa5c99eb3b3a397ac2c53b93b7c
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46473879"
---
## <a name="setting-up-your-web-server-or-project"></a>Hur du konfigurerar din webbserver eller projekt

> Om du vill ladda ned det här exempelprojektet i stället?
> - [Ladda ned Visual Studio-projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> eller
> - [Hämta projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) för en lokal webbserver, till exempel nod
>
> Och sedan gå vidare till den [konfigurationssteget](#register-your-application) konfigurera kodexemplet innan det körs.

## <a name="prerequisites"></a>Förutsättningar
En lokal webbserver som [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core), eller IIS Express-integrering med [Visual Studio 2017](https://www.visualstudio.com/downloads/) krävs för att köra den här självstudien.

Anvisningarna i den här guiden är baserat på Node.js och Visual Studio 2017, men passa på att använda något annat utvecklingsmiljö eller webbservern.

## <a name="create-your-project"></a>Skapa ditt projekt

> ### <a name="option-1-visual-studio"></a>Alternativ 1: Visual Studio
> Om du använder Visual Studio och skapar ett nytt projekt, följer du stegen nedan för att skapa en ny Visual Studio-lösning:
> 1.    I Visual Studio:  `File` > `New` > `Project`
> 2.    Under `Visual C#\Web`väljer `ASP.NET Web Application (.NET Framework)`
> 3.    Namnge ditt program och klicka på *OK*
> 4.    Under `New ASP.NET Web Application`väljer `Empty`

<p/><!-- -->

> ### <a name="option-2-node-other-web-servers"></a>Alternativ 2: Noden / övrigt webbservrar
> Kontrollera att du har installerat [Node.js](https://nodejs.org/en/download/), följ instruktionerna nedan:
> - Skapa en mapp för att vara värd för programmet.


## <a name="create-your-single-page-applications-ui"></a>Skapa en sida programmets användargränssnitt
1.  Skapa en *index.html* -filen för din JavaScript SPA. Om du använder Visual Studio väljer du projektet (rotmappen för projektet), högerklicka och välj: `Add`  >  `New Item`  >  `HTML page` och ge den namnet index.html
2.  Lägg till följande kod till din sida:
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
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
