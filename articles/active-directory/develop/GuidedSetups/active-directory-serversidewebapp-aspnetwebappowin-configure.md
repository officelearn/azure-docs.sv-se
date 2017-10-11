---
title: "Azure AD v2 ASP.NET Web Server komma igång - Config | Microsoft Docs"
description: "Implementera Microsoft logga In på en ASP.NET-lösning med ett traditionellt webbläsarbaserade program med OpenID Connect standard"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 0c627802ccfba230dcde2dafffee26cb1c895791
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
## <a name="create-an-application-express"></a>Skapa ett program (snabb)
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:
1. Registrera ditt program via den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Ange ett namn för ditt program och din e-post
3.  Kontrollera att alternativet för interaktiv installation är markerat
4.  Följ instruktionerna för att lägga till en omdirigerings-URL för ditt program

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Lägga till registreringsinformationen program i lösningen (Avancerat)
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:
1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app) registrera ett program
2. Ange ett namn för ditt program och din e-post 
3.  Kontrollera att alternativet för interaktiv installation är markerat
4.  Klicka på `Add Platform`och välj`Web`
5.  Gå tillbaka till Visual Studio och i Solution Explorer, välj projektet och titta på fönstret Egenskaper (om du inte ser en egenskapsfönstret trycker du på F4)
6.  Ändra SSL aktiverat för`True`
7.  Kopiera den URL som SSL och lägga till denna URL i listan över omdirigerings-URL: er i portalen för registrering omdirigerings-URL-listan:<br/><br/>![Egenskaper för](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
8.  Lägg till följande i `web.config` finns i rotmappen under avsnittet `configuration\appSettings`:

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
Ersätt `ClientId` med program-Id som du precis har registrerats
</li>
<li>
Ersätt `redirectUri` med SSL-URL för ditt projekt
</li>
</ol>
<!-- End Docs -->
