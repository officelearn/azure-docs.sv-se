---
title: 'Ange omdirigera URL: er till b2clogin.com för Azure Active Directory B2C | Microsoft Docs'
description: 'Lär dig mer om med hjälp av b2clogin.com i din omdirigerings-URL: er för Azure Active Directory B2C.'
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8e06cf1a443d4fd158e29ef4b53206a83800dfe9
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803060"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Ange omdirigera URL: er till b2clogin.com för Azure Active Directory B2C

När du har konfigurerat en identitetsprovider för registrering och inloggning i ditt Azure Active Directory (Azure AD) B2C-program måste du ange en omdirigerings-URL. Tidigare användes login.microsoftonline.com, nu bör du använda b2clogin.com.

Med hjälp av b2clogin.com ger dig ytterligare fördelar, till exempel:

- Cookies som inte längre delas med andra Microsoft-tjänster.
- Din URL: er inkluderar inte längre en referens till Microsoft. Till exempel `https://your-tenant-name.b2clogin.com/tfp/your-tenant-ID/policyname/v2.0/.well-known/openid-configuration`.

Ange om du vill använda b2clogin.com omdirigeringen URL: er i din identitet provider-programmen kan använda b2clogin.com. Du kan också ange att Azure AD B2C-programmet ska använda b2clogin.com för princip-referenser och token-slutpunkter. Om du använder MSAL, måste du ange den **ValidateAuthority** egenskap `false`.

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
- [Anpassad OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Uppdatera ditt program

Azure AD B2C-program förmodligen avser `login.microsoftonline.com` på flera platser, t.ex din princip-referenser och token-slutpunkter.  Se till att din auktoriseringsslutpunkt och tokenslutpunkten utfärdare har uppdaterats för att använda `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Ange egenskapen ValidateAuthority

Om du använder MSAL, ange den **ValidateAuthority** till `false`. I följande exempel visas hur du kan ange egenskapen:

```
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

 Mer information finns i [ClientApplicationBase klass ](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase?view=azure-dotnet).