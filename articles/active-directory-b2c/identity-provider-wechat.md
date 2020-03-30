---
title: Konfigurera registrering och inloggning med ett WeChat-konto
titleSuffix: Azure AD B2C
description: Ge registrering och inloggning till kunder med WeChat-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c53210939358255b20d0e976df9c4bff88580a80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184445"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett WeChat-konto med Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Skapa ett WeChat-program

Om du vill använda ett WeChat-konto som identitetsprovider i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klientorganisation som representerar det. Om du inte redan har ett WeChat-konto [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)kan du få information på .

### <a name="register-a-wechat-application"></a>Registrera en WeChat-applikation

1. Logga in [https://open.weixin.qq.com/](https://open.weixin.qq.com/) med dina WeChat-autentiseringsuppgifter.
1. Välj 中ンンン (ledningscenter). **管理中心**
1. Följ stegen för att registrera ett nytt program.
1. Ange `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **i**中ンンン (motringning URL). Om klientnamnet till exempel är contoso anger `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`du webbadressen som .
1. Kopiera **APP-ID** och **APP-NYCKEL**. Du behöver dessa för att lägga till identitetsprovidern till din klientorganisation.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurera WeChat som identitetsleverantör i din klientorganisation

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **WeChat (Förhandsversion).**
1. Ange ett **namn**. Till exempel *WeChat*.
1. För **klient-ID**anger du APP-ID:et för WeChat-programmet som du skapade tidigare.
1. För **klienthemligheten**anger du APP-tangenten som du spelade in.
1. Välj **Spara**.
