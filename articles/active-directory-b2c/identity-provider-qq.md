---
title: Konfigurera registrering och inloggning med ett QQ-konto med Azure Active Directory B2C
description: Ge registrering och inloggning till kunder med QQ-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b0f15124c64e5cca54112987d486ddadaca79452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187995"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett QQ-konto med Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Skapa ett QQ-program

Om du vill använda ett QQ-konto som identitetsprovider i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klientorganisation som representerar det. Om du inte redan har ett QQ-konto [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)kan du registrera dig på .

### <a name="register-for-the-qq-developer-program"></a>Registrera dig för QQ-utvecklarprogrammet

1. Logga in på [QQ-utvecklarportalen](http://open.qq.com) med dina QQ-kontouppgifter.
1. När du har [https://open.qq.com/reg](https://open.qq.com/reg) loggat in går du till för att registrera dig som utvecklare.
1. Välj**中ン**(enskild utvecklare).
1. Ange den information som krävs och välj**中ン**(nästa steg).
1. Slutför e-postverifieringsprocessen. Du måste vänta några dagar för att godkännas efter registrering som utvecklare.

### <a name="register-a-qq-application"></a>Registrera en QQ-applikation

1. Gå [https://connect.qq.com/index.html](https://connect.qq.com/index.html)till .
1. Välj **应用管理** (apphantering).
1. Välj 中**ン**ンン (skapa app) och ange den information som krävs.
1. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` **i**中ンンン (motringning URL). Om du `tenant_name` till exempel är contoso anger `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`du webbadressen som .
1. Välj 中**ンンン**(skapa app).
1. På bekräftelsesidan **väljer** du (apphantering) för att återgå till sidan för apphantering.
1. Välj**中ン (visa) bredvid** appen du skapade.
1. Välj **(redigera).**
1. Kopiera **APP-ID** och **APP-NYCKEL**. Du behöver båda dessa värden för att lägga till identitetsprovidern till din klientorganisation.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurera QQ som identitetsprovider

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj ikonen **Katalog + Prenumeration** i portalverktygsfältet och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Sök efter och välj **Azure AD B2C**i Azure-portalen .
1. Välj **Identitetsleverantörer**och välj sedan **QQ (Förhandsgranska).**
1. Ange ett **namn**. Till exempel *QQ*.
1. För **klient-ID**anger du APP-ID:t för QQ-programmet som du skapade tidigare.
1. För **klienthemligheten**anger du APP-tangenten som du spelade in.
1. Välj **Spara**.
