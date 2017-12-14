---
title: "Testkör ett program för Azure AD B2C-sida | Microsoft Docs"
description: "Testkör logga in, registrera, Redigera profil och återställa lösenord för användaren resor använder en testmiljö Azure AD B2C"
services: active-directory-b2c
documentationcenter: 
author: saraford
manager: mtillman
editor: PatAltimore
ms.assetid: 5a8a46af-28bb-4b70-a7f0-01a5240d0255
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: ba8ee4657309ab2a541f4c7b3fd4879542eee63c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="test-drive-a-single-page-application-configured-with-azure-ad-b2c"></a>Testkör en enda sida program som konfigurerats med Azure AD B2C

## <a name="about-this-sample"></a>Om det här exemplet

Azure Active Directory B2C ger Identitetshantering i molnet för att hålla ditt program, företag och kunder som skyddas.  Denna Snabbstart använder ett exempelprogram sida för att demonstrera:

* Med hjälp av den **registrera dig eller logga In** princip för att skapa eller logga in med ett sociala identitetsleverantör eller ett lokalt konto med en e-postadress. 
* **Anropar en API** att hämta ditt namn från en Azure AD B2C skyddad resurs.

## <a name="prerequisites"></a>Krav

* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
    - **ASP.NET och webbutveckling**

* Installera [Node.js](https://nodejs.org/en/download/)

* Ett sociala konto från Facebook, Google, Microsoft eller Twitter. Om du inte har en sociala konto, krävs en giltig e-postadress.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Hämta exemplet

[Hämta eller klona exempelprogrammet](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) från GitHub.

## <a name="run-the-sample-application"></a>Kör exempelprogrammet

Att köra det här exemplet från Kommandotolken Node.js: 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

Konsolfönstret visar portnumret för webbprogram som körs på datorn.

```
Listening on port 6420...
```

Öppna `http://localhost:6420` i en webbläsare för att få åtkomst till webbprogrammet.


![Exempelapp i webbläsare](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>Skapa ett konto

Klicka på den **inloggning** för att starta Azure AD B2C **registrera dig eller logga In** arbetsflöde. När du skapar ett konto kan använda du ett befintligt sociala identitet provider-konto eller ett e-postkonto.

### <a name="sign-up-using-a-social-identity-provider"></a>Logga med ett sociala identitetsleverantören.

Klicka på knappen för den identitetsleverantör som du vill använda för att registrera dig med sociala identitetsleverantör. Om du föredrar att använda en e-postadress, hoppa till den [logga med en e-postadress](#sign-up-using-an-email-address) avsnitt.

![Logga In eller registrera dig](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

Du måste autentisera med sociala kontot autentiseringsuppgifter och den programmet behörighet att läsa information från sociala kontot (inloggning). Programmet kan hämta profilinformation från sociala kontot, till exempel ditt namn och ort genom att bevilja åtkomst. 

![Autentisera och auktorisera med sociala konto](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

Ditt nya kontoinformation finns i förväg med information från ditt sociala konto. 

![Nya kontouppgifterna registreringen profil](media/active-directory-b2c-quickstarts-spa/new-account-sign-up-profile-details-spa.png)

Uppdatera fält visningsnamn, befattning och ort och klicka på **Fortsätt**.  De värden du anger används för din Azure AD B2C användarprofil.

Du har skapat ett nytt Azure AD B2C-användarkonto som använder en identitetsleverantör. 

Nästa steg: [anropa en resurs](#call-a-resource) avsnitt.

### <a name="sign-up-using-an-email-address"></a>Logga med en e-postadress

Om du väljer att inte använda ett konto för sociala för autentisering kan du skapa ett Azure AD B2C-användarkonto med en giltig e-postadress. Ett lokalt användarkonto i Azure AD B2C använder Azure Active Directory som identitetsleverantören. Om du vill använda din e-postadress, klickar du på den **har du inget konto? Logga nu** länk.

![Logga In eller registrera dig via e-post](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-email-spa.png)

Ange en giltig e-postadress och klicka på **skicka verifieringskoden**. En giltig e-postadress krävs för att få verifieringskoden från Azure AD B2C. 

Ange den Verifieringskod du via e-post och klickar på **Kontrollera koden**.

Lägg till din profilinformation och på **skapa**.

![Logga med ett nytt konto med e-post](media/active-directory-b2c-quickstarts-spa/sign-up-new-account-profile-email-web.png)

Du har skapat en ny Azure AD B2C lokalt användarkonto.

## <a name="call-a-resource"></a>Anropa en resurs

När du är inloggad kan du klicka på den **anropa webb-API** så ditt visningsnamn som returnerades från Web API-anrop som ett JSON-objekt. 

![Webb-API-svar](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

## <a name="next-steps"></a>Nästa steg

Nästa steg är att skapa din egen Azure AD B2C-klient och konfigurera samplet som ska köras med din klient. 

> [!div class="nextstepaction"]
> [Skapa en Azure Active Directory B2C-klient i Azure-portalen](active-directory-b2c-get-started.md)