---
title: Konfigurera registrering och inloggning med ett Weibo-konto med Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Weibo konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 06a79250bac977fc4ade7853594c5307bb11d983
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336953"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Weibo-konto med Azure Active Directory B2C

> [!NOTE]
> Den här funktionen är en förhandsversion.
> 

## <a name="create-a-weibo-application"></a>Skapa ett Weibo-program

Om du vill använda ett Weibo-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Weibo-konto, kan du hämta den på [ http://weibo.com/signup/signup.php?lang=en-us ](http://weibo.com/signup/signup.php?lang=en-us).

1. Logga in på den [Weibo utvecklarportalen](http://open.weibo.com/) med autentiseringsuppgifterna för ditt Weibo.
2. När du har loggat in väljer du ditt namn i det övre högra hörnet.
3. Välj i listrutan**编辑开发者信息**(redigera information för utvecklare).
4. Ange informationen som krävs och välj**提交**(skicka).
5. Slutför verifieringen e-post.
6. Gå till den [verifiering sidan](http://open.weibo.com/developers/identity/edit).
7. Ange informationen som krävs och välj**提交**(skicka).

### <a name="register-a-weibo-application"></a>Registrera ett Weibo-program

1. Gå till den [nya Weibo appregistreringssidan](http://open.weibo.com/apps/new).
2. Ange informationen som programmet.
3. Välj**创建**(skapa).
4. Kopiera värdena för **Appnyckeln** och **Apphemlighet**. Du måste båda dessa att lägga till identitetsleverantören till din klient.
5. Överföra foton som krävs och ange nödvändig information.
6. Välj**保存以上信息**(spara).
7. Välj**高级信息**(avancerad information).
8. Välj**编辑**(redigera) bredvid fältet för OAuth2.0**授权设置**(omdirigerings-URL).
9. Ange `https://{tenant_name}.b2clogin.com/te/{tenant_name}.onmicrosoft.com/oauth2/authresp` för OAuth2.0**授权设置**(omdirigerings-URL). Exempel: om din `tenant_name` är contoso, ange URL: en ska vara `https://contoso.b2clogin.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Välj**提交**(skicka).  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Konfigurera ett Weibo-konto som identitetsprovider

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**. 

    ![Växla till Azure AD B2C-klientorganisationen](./media/active-directory-b2c-setup-weibo-app/switch-directories.png)

    Välj den katalog som innehåller din klient.

    ![Välj katalog](./media/active-directory-b2c-setup-weibo-app/select-directory.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Weibo*.
6. Välj **identifiera providertyp**väljer **Weibo (förhandsversion)**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange den App-nyckel som du antecknade tidigare som den **klient-ID** och ange App-hemlighet som du registrerade som den **klienthemlighet** av Weibo programmet som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din Weibo konfiguration.
