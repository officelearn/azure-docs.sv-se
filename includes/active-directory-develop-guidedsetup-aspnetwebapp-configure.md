---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 15db2192703971a8056df34343c427db11c8411a
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988527"
---
## <a name="register-your-application"></a>Registrera ditt program

För att registrera ditt program och lägga till din registreringsinformation för programmet i din lösning, har du två alternativ:

### <a name="option-1-express-mode"></a>Alternativ 1: Express-läge

Du kan snabbt registrera ditt program genom att göra följande:

1. Registrera ditt program via den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2. Ange ett namn för ditt program och din e-post.
3. Kontrollera att alternativet för interaktiva installation är markerat.
4. Följ anvisningarna för att lägga till en omdirigerings-URL för ditt program.

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge

Du registrerar programmet och lägger till programregistreringsinformationen i din lösning genom att göra följande:

1. Gå till [Microsoft-portalen för programregistrering](https://apps.dev.microsoft.com/portal/register-app) för att registrera ett program.
2. Ange ett namn för ditt program och din e-post.
3. Kontrollera att alternativet för interaktiva installation är avmarkerat
4. Välj `Add Platform`, och välj sedan `Web`.
5. Gå tillbaka till Visual Studio och, i Solution Explorer, väljer du projektet och titta på fönstret Egenskaper (om du inte ser en egenskapsfönstret trycker på F4)
6. Ändra SSL aktiverat till `True`.
7. Högerklicka på projektet i Visual Studio och välj sedan **egenskaper**, och **Web** fliken. I den *servrar* avsnittet Ändra den *Url för Project* vara SSL-URL.
8. Kopiera URL: en för SSL och lägga till denna URL i listan över omdirigerings-URL: er i portalen för registrering lista över omdirigerings-URL: er:<br/><br/>![Projektegenskaper](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Lägg till följande i `web.config` finns i rotmappen i avsnittet `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Ersätt `ClientId` med program-ID som du just registrerade.
11. Ersätt `redirectUri` med SSL-URL: en för ditt projekt.
