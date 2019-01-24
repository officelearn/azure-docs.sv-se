---
title: 'Ange omdirigera URL: er till b2clogin.com för Azure Active Directory B2C | Microsoft Docs'
description: 'Lär dig mer om med hjälp av b2clogin.com i din omdirigerings-URL: er för Azure Active Directory B2C.'
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 147d8e80c66bd47ed56400deca024532fc7e39c7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844934"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Ange omdirigera URL: er till b2clogin.com för Azure Active Directory B2C

När du har konfigurerat en identitetsprovider för registrering och inloggning i ditt Azure Active Directory (Azure AD) B2C-program måste du ange en omdirigerings-URL. Tidigare användes login.microsoftonline.com, nu bör du använda b2clogin.com.

Med hjälp av b2clogin.com ger dig ytterligare fördelar, till exempel:

- Cookies som inte längre delas med andra Microsoft-tjänster.
- Din URL: er inkluderar inte längre en referens till Microsoft. Till exempel `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`.

Överväg att dessa inställningar som kan behöva ändra när med hjälp av b2clogin.com:

- Ange omdirigerings URL: er i din identitet provider-programmen kan använda b2clogin.com. 
- Ange din Azure AD B2C-program att använda b2clogin.com för användarreferenser för flödet och token-slutpunkter. 
- Om du använder MSAL, måste du ange den **ValidateAuthority** egenskap `false`.
- Se till att du ändrar någon **tillåtna ursprung** som du har definierat i CORS-inställningarna för [anpassning av användargränssnittet](active-directory-b2c-ui-customization-custom-dynamic.md).  

## <a name="change-redirect-urls"></a>Ändra omdirigera URL: er

Om du vill använda b2clogin.com, i inställningarna för ditt program för providern av identitet, leta efter och ändra listan över URL: erna för betrodda att omdirigera tillbaka till Azure AD B2C.  För närvarande har du förmodligen att det ställts in för att omdirigera till vissa login.microsoftonline.com-webbplatsen. 

Du måste ändra omdirigerings-URL så att `your-tenant-name.b2clogin.com` har behörighet. Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klientorganisationen och ta bort `/te` om den finns i URL: en. Det finns mindre variationer till denna URL för varje identitetsprovider så kontrollera motsvarande sidan för att få en exakt Webbadress.

Du hittar information om konfiguration för Identitetsproviders i följande artiklar:

- [Microsoft-konto](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [Custom OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Uppdatera ditt program

Azure AD B2C-program förmodligen avser `login.microsoftonline.com` på flera platser, till exempel användarreferenser för flödet och token-slutpunkter.  Se till att din auktoriseringsslutpunkt och tokenslutpunkten utfärdare har uppdaterats för att använda `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Ange egenskapen ValidateAuthority

Om du använder MSAL, ange den **ValidateAuthority** till `false`. I följande exempel visas hur du kan ange egenskapen:

I [MSAL för .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet):

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

Och i [MSAL för Javascript](https://github.com/AzureAD/microsoft-authentication-library-for-js):

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
