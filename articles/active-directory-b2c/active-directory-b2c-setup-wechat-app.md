---
title: Konfigurera registrering och inloggning med ett WeChat-konto med Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med WeChat konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e88187c5035abc28ca9deecaf8517e8a21e38d1d
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952342"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett WeChat-konto med Azure Active Directory B2C

> [!NOTE]
> Den här funktionen är en förhandsversion.
> 

## <a name="create-a-wechat-application"></a>Skapa ett WeChat-program

Om du vill använda ett WeChat-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett WeChat-konto, du kan få information på [ http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html ](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrera ett WeChat-program

1. Logga in på [ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) med dina autentiseringsuppgifter för WeChat.
2. Välj**管理中心**(management center).
3. Följ stegen för att registrera ett nytt program.
4. Ange `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` i**授权回调域**(Motringnings-URL). Exempel: om din `tenant_name` är contoso.onmicrosoft.com, ange URL: en ska vara `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Kopiera den **APP-ID** och **APPNYCKELN**. Du behöver dessa att lägga till identitetsleverantören till din klient.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurera WeChat som en identitetsprovider i din klient

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**. 

    ![Växla till Azure AD B2C-klientorganisationen](./media/active-directory-b2c-setup-wechat-app/switch-directories.png)

    Välj den katalog som innehåller din klient.

    ![Välj katalog](./media/active-directory-b2c-setup-wechat-app/select-directory.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *WeChat*.
6. Välj **identifiera providertyp**väljer **WeChat (förhandsversion)**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange det ID som du antecknade tidigare som den **klient-ID** och ange APP-NYCKELN som du registrerade som den **klienthemlighet** av den WeChat program som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din WeChat konfiguration.

