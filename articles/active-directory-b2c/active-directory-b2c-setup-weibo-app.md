---
title: Konfigurera registrering och inloggning med ett Weibo-konto med hjälp av Azure Active Directory B2C
description: Tillhandahålla registrering och inloggning till kunder med Weibo-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 428149fbc015037fa8c92bad6fe72cbd97aad5d7
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622244"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Weibo-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Skapa ett Weibo-program

Om du vill använda ett Weibo-konto som identitets leverantör i Azure Active Directory (Azure AD) B2C måste du skapa ett program i din klient organisation som representerar det. Om du inte redan har ett Weibo-konto kan du registrera dig på [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us).

1. Logga in på [Weibo Developer-portalen](https://open.weibo.com/) med dina Weibo-kontoautentiseringsuppgifter.
1. När du har loggat in väljer du ditt visnings namn i det övre högra hörnet.
1. I list rutan väljer du**编辑开发者信息**(redigera information om utvecklare).
1. Ange den information som krävs och välj**提交**(skicka).
1. Slutför verifierings processen för e-post.
1. Gå till [sidan med identitets verifiering](https://open.weibo.com/developers/identity/edit).
1. Ange den information som krävs och välj**提交**(skicka).

### <a name="register-a-weibo-application"></a>Registrera ett Weibo-program

1. Gå till den [nya Weibo app Registration-sidan](https://open.weibo.com/apps/new).
1. Ange nödvändig programinformation.
1. Välj**创建**(skapa).
1. Kopiera värdena för **app Key** och **app Secret**. Du behöver båda dessa för att lägga till identitets leverantören till din klient.
1. Överför de nödvändiga fotona och ange nödvändig information.
1. Välj**保存以上信息**(Spara).
1. Välj**高级信息**(avancerad information).
1. Välj**编辑**(redigera) bredvid fältet för OAuth 2.0-**授权设置**(omdirigerings-URL).
1. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` för OAuth 2.0-**授权设置**(omdirigerings-URL). Om ditt klient namn till exempel är contoso anger du URL: en `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Välj**提交**(skicka).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Konfigurera ett Weibo-konto som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **Weibo (för hands version)** .
1. Ange ett **namn**. Till exempel *Weibo*.
1. För **klient-ID**anger du appens nyckel för det Weibo-program som du skapade tidigare.
1. För **klient hemligheten**anger du appens hemlighet som du har spelat in.
1. Välj **Spara**.
