---
title: Med hjälp av b2clogin.com | Microsoft Docs
description: Läs mer om med hjälp av b2clogin.com i stället för login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c24582fce44006d9a3972d73078aa8cb0d212c11
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47053852"
---
# <a name="using-b2clogincom"></a>Med hjälp av B2Clogin.com

Framöver kommer vi användarutbildning alla kunder att använda `<YourDirectoryName>.b2clogin.com` och versionen kommer vi att avveckla `login.microsoftonline.com`. B2Clogin.com ger dig ytterligare förmåner som:
* Du kan inte längre dela samma cookie med andra Microsoft-tjänster.
* Du kan ta bort alla referenser till Microsoft i din URL (du kan ersätta `<YourDirectoryName>.onmicrosoft.com` med din katalog-ID). Till exempel: `https://<YourDirectoryName>.b2clogin.com/tfp/<YourDirectoryID>/<policyname>/v2.0/.well-known/openid-configuration`.

Här är vad du behöver göra för att flytta över till b2clogin.com

* Ändra omdirigerings-URI: er för dina sociala provider-appar
* Redigera programmet för användning av B2Clogin.com i stället för `login.microsoftonline.com` för princip-referenser och token-slutpunkter.
* Om du använder MSAL kan du behöva ange `ValidateAuthority=false`.  

##<a name="redirect-uris-for-social-identity-providers"></a>Omdirigerings-URI: er för sociala identitetsleverantörer

Om du har socialt konto identitetsleverantörer som angetts i din katalog kommer du behöva göra ändringar i sina program.  Det finns en parameter för programmet för med varje social provider som innehåller en lista över URL: erna för betrodda att omdirigera tillbaka till Azure AD B2C.  För närvarande kan du antagligen har den konfigurerat för att omdirigera till några `login.microsoftonline.com` plats, måste du ändra den här URL: en så att `YourDirectoryName.b2clogin.com` blir en auktoriserad omdirigerings-URI.  Se till att ta bort den `/te` samt.  Det finns mindre variationer till denna URL för varje identitetsprovider så kontrollera motsvarande sidan för att få en exakt Webbadress.  

| Identitetsprovider |
|-------------------|
|[Microsoft-konto](active-directory-b2c-setup-msa-app.md)|
|[Facebook](active-directory-b2c-setup-fb-app.md)|
|[Google](active-directory-b2c-setup-goog-app.md)|
|[Amazon](active-directory-b2c-setup-amzn-app.md)|
|[LinkedIn](active-directory-b2c-setup-li-app.md)|
|[Twitter](active-directory-b2c-setup-twitter-app.md)|
|[GitHub](active-directory-b2c-setup-github-app.md)|
|[Weibo](active-directory-b2c-setup-weibo-app.md)|
|[QQ](active-directory-b2c-setup-qq-app.md)|
|[WeChat](active-directory-b2c-setup-wechat-app.md)|
|[Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)|
|[Anpassad OIDC](active-directory-b2c-setup-oidc-idp.md)|

##<a name="update-your-application-references"></a>Uppdatera din programreferenser

Ditt program förmodligen avser `login.microsoftonline.com` på flera platser, t.ex din princip-referenser och token-slutpunkter.  Kontrollera att din auktoriseringsslutpunkt och tokenslutpunkten utfärdare har uppdaterats.  

##<a name="set-validateauthorityfalse-in-msal"></a>Ange `ValidateAuthority=false` i MSAL

Om du använder MSAL, måste du ange `ValidateAuthority=false`.  Mer information finns i [den här dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase?view=azure-dotnet).