---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 5ce0f18c1ec7a0fcb6465ab20e774976552687f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133649"
---
## <a name="set-up-your-web-server-or-project"></a>Konfigurera din webbserver eller projekt

> Om du vill ladda ned det här exempelprojektet i stället? Gör något av följande:
> 
> - Kör projektet med en lokal webbserver, till exempel Node.js, [hämta projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - (Valfritt) Kör projektet med IIS-server [ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Och sedan för att konfigurera kodexemplet innan du kan köra den, gå till den [konfigurationssteget](#register-your-application).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du vill köra den här självstudien, du behöver en lokal webbserver som [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core), eller IIS Express-integrering med [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Om du använder Node.js för att köra projektet installerar en integrerad utvecklingsmiljö (IDE), till exempel [Visual Studio Code](https://code.visualstudio.com/download), för att redigera projektfiler.

* Anvisningarna i den här guiden är baserat på Node.js och Visual Studio 2017, men du kan använda andra utvecklingsmiljö eller webbservern.

## <a name="create-your-project"></a>Skapa ditt projekt

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Alternativ 1: Node.js- eller andra webbservrar
> Kontrollera att du har installerat [Node.js](https://nodejs.org/en/download/), och gör sedan följande:
> - Skapa en mapp för att vara värd för programmet.
>
> ### <a name="option-2-visual-studio"></a>Alternativ 2: Visual Studio
> Om du använder Visual Studio och skapar ett nytt projekt, gör du följande:
> 1. Välj **Arkiv** > **Nytt** > **Projekt** i Visual Studio.
> 1. Under **Visual C#\Web** väljer du **ASP.NET-webbprogram (.NET Framework)** .
> 1. Ange ett namn för ditt program och välj sedan **OK**.
> 1. Under **nytt ASP.NET-webbprogram**väljer **tom**.

## <a name="create-the-spa-ui"></a>Skapa SPA-gränssnitt
1. Skapa en *index.html* -filen för din JavaScript SPA. Om du använder Visual Studio väljer du projektet (rotmappen för projektet), högerklicka och välj **Lägg till** > **nytt objekt** > **HTML-sida**, och ge filen namnet *index.html*.

1. I den *index.html* Lägg till följande kod:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Du kan ersätta versionen av MSAL.js i det här skriptet med den senaste utgivna versionen under [MSAL.js släpper](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
