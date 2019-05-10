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
ms.openlocfilehash: a00bc7a05af9e329494a11f9bee444827cbebf38
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199508"
---
## <a name="setting-up-your-web-server-or-project"></a>Hur du konfigurerar din webbserver eller projekt

> Om du vill ladda ned det här exempelprojektet i stället?
> - [Hämta projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) ska köras med en lokal webbserver, till exempel nod
>
> eller
> - (Valfritt) [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip) att köra med IIS-server
>
> Och sedan gå vidare till den [konfigurationssteget](#register-your-application) konfigurera kodexemplet innan det körs.

## <a name="prerequisites"></a>Nödvändiga komponenter
En lokal webbserver som [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core), eller IIS Express-integrering med [Visual Studio 2017](https://www.visualstudio.com/downloads/) krävs för att köra den här självstudien.

Om du använder Node.js för att köra projektet, installera en IDE som [Visual Studio Code](https://code.visualstudio.com/download) redigera projektfiler.

Anvisningarna i den här guiden är baserat på Node.js och Visual Studio 2017, men passa på att använda något annat utvecklingsmiljö eller webbservern.

## <a name="create-your-project"></a>Skapa ditt projekt

> ### <a name="option-1-node-other-web-servers"></a>Alternativ 1: Noden / övrigt webbservrar
> Kontrollera att du har installerat [Node.js](https://nodejs.org/en/download/), följ instruktionerna nedan:
> - Skapa en mapp för att vara värd för programmet.

<p><!-- -->

> ### <a name="option-2-visual-studio"></a>Alternativ 2: Visual Studio
> Om du använder Visual Studio och skapar ett nytt projekt, följer du stegen nedan för att skapa en ny Visual Studio-lösning:
> 1.    I Visual Studio:  **Arkiv > Nytt > projekt**
> 2.    Under **Visual C# \Web**väljer **ASP.NET-Webbtillämpningsprogram (.NET Framework)**
> 3.    Ange ett namn för ditt program och välj **OK**
> 4.    Under **nytt ASP.NET-webbprogram**väljer **tom**

## <a name="create-your-single-page-applications-ui"></a>Skapa en sida programmets användargränssnitt
1. Skapa en `index.html` -filen för din JavaScript SPA. Om du använder Visual Studio väljer du projektet (rotmappen för projektet), högerklicka och välj: **Lägg till > Nytt objekt > HTML-sida** och ge den namnet index.html.

2. Lägg till följande kod till din sida:
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
   > Du kan ersätta versionen av MSAL.js i ovan skript med den senaste utgivna versionen under [MSAL.js släpper](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
