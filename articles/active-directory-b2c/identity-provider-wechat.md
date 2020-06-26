---
title: Konfigurera registrering och inloggning med ett WeChat-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med WeChat-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 462e33c836d8ca0a904e8f7b2e833dc7103311fc
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85387906"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett WeChat-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Skapa ett WeChat-program

Om du vill använda ett WeChat-konto som identitets leverantör i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klient som representerar det. Om du inte redan har ett WeChat-konto kan du hämta information på [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) .

### <a name="register-a-wechat-application"></a>Registrera ett WeChat-program

1. Logga in på [https://open.weixin.qq.com/](https://open.weixin.qq.com/) med dina WeChat-autentiseringsuppgifter.
1. Välj**管理中心**(Management Center).
1. Följ stegen för att registrera ett nytt program.
1. Ange `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i**授权回调域**(återanrops-URL). Om ditt klient namn till exempel är contoso anger du URL: en `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` .
1. Kopiera **app-ID** och **app-nyckel**. Du behöver dessa för att lägga till identitets leverantören till din klient organisation.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurera WeChat som en identitets leverantör i din klient organisation

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **WeChat (för hands version)**.
1. Ange ett **namn**. Till exempel *WeChat*.
1. För **klient-ID**anger du app-ID: t för det WeChat-program som du skapade tidigare.
1. Ange den APP-nyckel som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.
