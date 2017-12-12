---
title: "Testkör en webbapp i Azure AD B2C aktiverat | Microsoft Docs"
description: "Testkör logga in, registrera, Redigera profil och återställa lösenord för användaren resor använder en testmiljö Azure AD B2C"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: mtillman
editor: PatAltimore
ms.assetid: 2ffb780d-2c51-4c2e-b8d6-39c40a81a77e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: patricka
ms.openlocfilehash: bc56da695145f396a2899fb9dc7add3af9a549e8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="test-drive-an-azure-ad-b2c-enabled-web-app"></a>Testkör en Azure AD B2C-aktiverade webbapp

Azure Active Directory B2C ger Identitetshantering i molnet för att hålla ditt program, företag och kunder som skyddas. Denna Snabbstart använder en exempelapp uppgiften listan för att demonstrera:

> [!div class="checklist"]
> * Logga in med en anpassad inloggningssida.
> * Logga in med ett sociala identitetsleverantör.
> * Skapa och hantera din Azure AD B2C-konto och användarens profil.
> * Anropa ett webb-API som skyddas av Azure AD B2C.

## <a name="prerequisites"></a>Krav

* [Visual Studio-2017](https://www.visualstudio.com/downloads/) med den **ASP.NET och web development** arbetsbelastning. 
* Ett sociala konto från Facebook, Google, Microsoft eller Twitter.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Hämta exemplet

[Hämta eller klona exempelprogrammet](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) från GitHub.

## <a name="run-the-app-in-visual-studio"></a>Kör appen i Visual Studio

Öppna i projektmappen exempel programmet den `B2C-WebAPI-DotNet.sln` lösningen i Visual Studio. 

Lösningen är ett uppgiften listan exempelprogram som består av två projekt:

* **TaskWebApp** – en ASP.NET MVC-webbprogram där en användare kan hantera sina arbetsuppgifter i listan.  
* **TaskService** – ett ASP.NET Web API-serverdel som hanterar operations utförs på en användares att göra listobjekt. Webbprogrammet anropar den här webb-API och visar resultatet.

För den här snabbstarten måste du köra både det `TaskWebApp` och `TaskService` projekt på samma gång. 

1. I Visual Studio-menyn, Välj **projekt > Ange Startprojekt...** . 
2. Välj **flera Startprojekt** knappen.
3. Ändra den **åtgärd** för båda projekten till **starta**. Klicka på **OK**.

![Ange startsida i Visual Studio](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

Välj **Felsök > Starta felsökning** att skapa och köra båda programmen. Varje program öppnas på sin egen webbläsarflik i:

`https://localhost:44316/`-Den här sidan är ASP.NET-webbprogram. Du kan interagera direkt med det här programmet i Snabbstart.
`https://localhost:44332/`-Den här sidan är webb-API som anropas av ASP.NET-webbprogram.

## <a name="create-an-account"></a>Skapa ett konto

Klicka på den **registrering / logga in** länken i ASP.NET-webbprogram för att starta den **registrera dig eller logga In** arbetsflöde. När du skapar ett konto kan använda du ett befintligt sociala identitet provider-konto eller ett e-postkonto. Använd sociala identitet provider-konto från Facebook, Google, Microsoft eller Twitter för denna Snabbstart.

![Exempelwebbapp för ASP.NET](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>Logga med ett sociala identitetsleverantören.

Klicka på knappen för den identitetsleverantör som du vill använda för att registrera dig med sociala identitetsleverantör. 

![Logga In eller registrera dig](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

Du måste autentisera med sociala kontot autentiseringsuppgifter och den programmet behörighet att läsa information från sociala kontot (inloggning). Programmet kan hämta profilinformation från sociala kontot, till exempel ditt namn och ort genom att bevilja åtkomst. 

Slut processen inloggning identitetsprovider. Till exempel om du väljer Twitter, ange dina autentiseringsuppgifter för Twitter och på **logga in**.

![Autentisera och auktorisera med sociala konto](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

Din nya Azure AD B2C profil kontouppgifterna finns i förväg med information från ditt sociala konto.

Uppdatera fält visningsnamn, befattning och ort och klicka på **Fortsätt**.  De värden du anger används för din Azure AD B2C användarprofil.

![Nya kontouppgifterna registreringen profil](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

Du har:

> [!div class="checklist"]
> * Autentiseras med en identitetsleverantör.
> * Skapa ett användarkonto i Azure AD B2C. 

## <a name="edit-your-profile"></a>Redigera din profil

Azure Active Directory B2C innehåller funktioner för att tillåta användare att uppdatera deras profiler. Klicka på ditt profilnamn i menyraden web application och välj **Redigera profil** så här redigerar du den profil som du har skapat.

![Redigera profil](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

Ändra din **visningsnamn** och **Stad**.  Klicka på **Fortsätt** att uppdatera din profil.

![Uppdatera profilen](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

Lägg märke till ditt namn i den övre högra delen av sidan visar uppdaterade namnet. 

## <a name="access-a-secured-web-api-resource"></a>Åtkomst till en skyddad web API-resurs

Klicka på **uppgiftslista** att ange och ändra din att göra listobjekt. ASP.NET-webbprogram innehåller en åtkomst-token i begäran till web API resurs begärande behörighet att utföra åtgärder på användarens lista arbetsuppgifter. 

Ange text i den **nytt objekt** textruta. Klicka på **Lägg till** skyddas webb-API som lägger till ett listobjekt för att göra att anropa Azure AD B2C.

![Lägga till ett listobjekt för att göra](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

Du har har använt ditt användarkonto i Azure AD B2C kan ringa en auktoriserad en Azure AD B2C skyddade webb-API.

## <a name="next-steps"></a>Nästa steg

Exempel som används i den här snabbstarten kan användas för andra Azure AD B2C-scenarier inklusive:

* Skapa ett nytt lokalt konto med en e-postadress.
* Återställa lösenordet lokalt konto.

Om du är redo att detaljerad information om hur du skapar en egen Azure AD B2C-klient och konfigurera samplet som ska köras med en egen klient kan du prova följande kursen.

> [!div class="nextstepaction"]
> [Skapa en ASP.NET-webbapp med Azure Active Directory B2C profil för registrering, inloggning, redigera och återställning av lösenord](active-directory-b2c-devquickstarts-web-dotnet-susi.md)