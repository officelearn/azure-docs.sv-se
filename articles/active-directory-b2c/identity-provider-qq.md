---
title: Konfigurera registrering och inloggning med ett QQ-konto med hjälp av Azure Active Directory B2C
description: Tillhandahålla registrering och inloggning till kunder med QQ-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d230bc8a1e9bf388e1cca4e3a3a691223146d734
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387991"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett QQ-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Skapa ett QQ-program

Om du vill använda ett QQ-konto som identitets leverantör i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klient som representerar det. Om du inte redan har ett QQ-konto kan du registrera dig på [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) .

### <a name="register-for-the-qq-developer-program"></a>Registrera dig för QQ Developer-programmet

1. Logga in på [QQ Developer-portalen](http://open.qq.com) med dina QQ-kontoautentiseringsuppgifter.
1. När du har loggat in går du till [https://open.qq.com/reg](https://open.qq.com/reg) för att registrera dig som utvecklare.
1. Välj**个人**(enskild utvecklare).
1. Ange den information som krävs och välj**下一步**(nästa steg).
1. Slutför verifierings processen för e-post. Du måste vänta några dagar innan du har registrerat dig som utvecklare.

### <a name="register-a-qq-application"></a>Registrera ett QQ-program

1. Gå till [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. Välj**应用管理**(app Management).
1. Välj**创建应用**(skapa app) och ange den information som krävs.
1. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` i**授权回调域**(återanrops-URL). Om din till exempel `tenant_name` är contoso anger du URL: en `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` .
1. Välj**创建应用**(skapa app).
1. På sidan bekräftelse väljer du**应用管理**(app Management) för att gå tillbaka till sidan för hantering av appar.
1. Välj**查看**(Visa) bredvid den app som du skapade.
1. Välj**修改**(redigera).
1. Kopiera **app-ID** och **app-nyckel**. Du behöver båda dessa värden för att lägga till identitets leverantören till din klient organisation.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurera QQ som identitets leverantör

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **QQ (för hands version)**.
1. Ange ett **namn**. Till exempel *QQ*.
1. För **klient-ID**anger du app-ID: t för det QQ-program som du skapade tidigare.
1. Ange den APP-nyckel som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.
