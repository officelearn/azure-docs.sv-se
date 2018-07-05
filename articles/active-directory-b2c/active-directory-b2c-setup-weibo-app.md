---
title: Weibo konfiguration i Azure Active Directory B2C | Microsoft Docs
description: Ge registrera dig och logga in till konsumenter med Weibo konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bfd7dde290bd040f8457e6d095fdf896e802764b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444797"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Azure Active Directory B2C: Ge registrera dig och logga in till konsumenter med Weibo konton

> [!NOTE]
> Den här funktionen är en förhandsversion. Använd inte den här identitetsprovidern i din produktionsmiljö.
> 

## <a name="create-a-weibo-application"></a>Skapa ett Weibo-program

Om du vill använda Weibo som en identitetsprovider i Azure Active Directory (Azure AD) B2C måste du skapa ett Weibo program och ange rätt parametrar. Du behöver en Weibo-konto för att göra detta. Om du inte har någon kan du skaffa en vid [ http://weibo.com/signup/signup.php?lang=en-us ](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Registrera dig för utvecklarprogram Weibo

1. Gå till den [Weibo utvecklarportalen](http://open.weibo.com/) och logga in med autentiseringsuppgifterna för ditt Weibo.
2. När du loggar in, klickar du på ditt namn i det övre högra hörnet.
3. Välj i listrutan**编辑开发者信息**(redigera information för utvecklare).
4. Ange nödvändig information i formuläret och klicka på**提交**(skicka).
5. Slutför verifieringen e-post.
6. Gå till den [verifiering sidan](http://open.weibo.com/developers/identity/edit).
7. Ange nödvändig information i formuläret och klicka på**提交**(skicka).

### <a name="register-a-weibo-application"></a>Registrera ett Weibo-program

1. Gå till den [nya Weibo appregistreringssidan](http://open.weibo.com/apps/new).
2. Ange informationen som programmet.
3. Klicka på**创建**(skapa).
4. Kopiera värdena för **Appnyckeln** och **Apphemlighet**. Du behöver det senare.
5. Överföra foton som krävs och ange nödvändig information.
6. Klicka på**保存以上信息**(spara).
7. Klicka på**高级信息**(avancerad information).
8. Klicka på**编辑**(redigera) bredvid fältet för OAuth2.0**授权设置**(omdirigerings-URL).
9. Ange `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` för OAuth2.0**授权设置**(omdirigerings-URL). Exempel: om din `tenant_name` är contoso.onmicrosoft.com, ange URL: en ska vara `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Klicka på**提交**(skicka).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>Konfigurera Weibo som en identitetsprovider i din klient
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure portal.
2. Klicka på B2C-funktionsbladet **identitetsprovidrar**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för konfigurationen av identity-providern. Till exempel ange ”Weibo”.
5. Klicka på **identifiera providertyp**väljer **Weibo**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsprovidern**
7. Ange den **Appnyckeln** som du kopierade tidigare som den **klient-ID**.
8. Ange den **Apphemlighet** som du kopierade tidigare som den **Klienthemlighet**.
9. Klicka på **OK** och klicka sedan på **skapa** att spara din Weibo konfiguration.

