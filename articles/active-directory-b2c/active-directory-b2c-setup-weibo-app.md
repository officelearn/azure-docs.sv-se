---
title: 'Azure Active Directory B2C: Weibo konfiguration | Microsoft Docs'
description: "Ange registrering och inloggning för konsumenter med Weibo konton i dina program som skyddas av Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 1860de34-94cb-4ceb-851e-102f930f7230
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: 00c5d3781455c80b33bdbb4c872ae354531baf3e
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Azure Active Directory B2C: Ange registrering och inloggning för konsumenter med Weibo konton

> [!NOTE]
> Den här funktionen är i förhandsgranskningen. Använd inte den här identitetsleverantör i produktionsmiljön.
> 

## <a name="create-a-weibo-application"></a>Skapa ett Weibo program

Om du vill använda Weibo som en identitetsleverantör i Azure Active Directory (AD Azure) B2C måste du skapa ett Weibo program och ange rätt parametrar. Du behöver en Weibo-konto för att göra detta. Om du inte har någon kan du skaffa ett på [http://weibo.com/signup/signup.php?lang=en-us](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Registrera dig för utvecklarprogram Weibo

1. Gå till den [Weibo utvecklarportalen](http://open.weibo.com/) och logga in med autentiseringsuppgifterna för ditt Weibo.
2. När du har loggat in, klicka på ditt namn i det övre högra hörnet.
3. Välj i listrutan,**编辑开发者信息**(redigera information för utvecklare).
4. Ange nödvändig information i formuläret och klicka på**提交**(skicka).
5. Slutföra verifieringsprocessen för e-post.
6. Gå till den [identitet bekräftelsesidan](http://open.weibo.com/developers/identity/edit).
7. Ange nödvändig information i formuläret och klicka på**提交**(skicka).

### <a name="register-a-weibo-application"></a>Registrera en Weibo-program

1. Gå till den [nya Weibo app registreringssidan](http://open.weibo.com/apps/new).
2. Ange information om programmet.
3. Klicka på**创建**(skapa).
4. Kopiera värdena i **Appkey** och **App hemlighet**. Du behöver det senare.
5. Överför fotona som krävs och ange nödvändig information.
6. Klicka på**保存以上信息**(spara).
7. Klicka på**高级信息**(Avancerat information).
8. Klicka på**编辑**(redigera) bredvid fältet för OAuth2.0**授权设置**(omdirigera URL).
9. Ange `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` för OAuth2.0**授权设置**(omdirigera URL). Till exempel om din `tenant_name` är contoso.onmicrosoft.com, ange URL som ska vara `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Klicka på**提交**(skicka).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>Konfigurera Weibo som en identitetsleverantör i din klientorganisation
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure-portalen.
2. Klicka på B2C-funktionsbladet **identitetsleverantörer**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för providerkonfigurationen identitet. Till exempel ange ”Weibo”.
5. Klicka på **identitet providertyp**väljer **Weibo**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsleverantören.**
7. Ange den **Appkey** som du kopierade tidigare som den **klient-ID**.
8. Ange den **App hemlighet** som du kopierade tidigare som den **Klienthemlighet**.
9. Klicka på **OK** och klicka sedan på **skapa** att spara konfigurationen Weibo.

