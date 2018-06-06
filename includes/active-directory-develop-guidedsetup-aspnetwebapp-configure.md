---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7c052f30e564700b0a99b76342dc6c34ec5983b7
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "33901286"
---
## <a name="register-your-application"></a>Registrera ditt program

För att registrera ditt program och lägga till registreringsinformationen program i lösningen, har du två alternativ:

### <a name="option-1-express-mode"></a>Alternativ 1: Express-läge

Du kan snabbt registrera ditt program genom att göra följande:

1. Registrera ditt program via den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Ange ett namn för ditt program och din e-post
3.  Kontrollera att alternativet för interaktiv installation är markerat
4.  Följ instruktionerna för att lägga till en omdirigerings-URL för ditt program

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge

För att registrera ditt program och lägga till registreringsinformationen program i lösningen måste du göra följande:

1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app) registrera ett program
2. Ange ett namn för ditt program och din e-post 
3. Kontrollera att alternativet för interaktiv installation är markerat
4. Klicka på `Add Platform`och välj `Web`
5. Gå tillbaka till Visual Studio och i Solution Explorer, välj projektet och titta på fönstret Egenskaper (om du inte ser en egenskapsfönstret trycker du på F4)
6. Ändra SSL aktiverat för `True`
7. Högerklicka på projektet i Visual Studio och välj sedan **egenskaper**, och **Web** fliken. I den *servrar* avsnittet Ändra den *Url för* att SSL-URL
8. Kopiera den URL som SSL och lägga till denna URL i listan över omdirigerings-URL: er i portalen för registrering omdirigerings-URL-listan:<br/><br/>![Egenskaper för](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Lägg till följande i `web.config` finns i rotmappen under avsnittet `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Ersätt `ClientId` med program-ID som du precis har registrerats
11. Ersätt `redirectUri` med SSL-URL för ditt projekt

