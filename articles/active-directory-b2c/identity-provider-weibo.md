---
title: Konfigurera registrering och inloggning med ett Weibo-konto
titleSuffix: Azure AD B2C
description: Ge registrering och inloggning till kunder med Weibo-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e99b5717cdcc32d8fd138d1edf5fe1bd3283c70e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187907"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Weibo-konto med Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Skapa ett Weibo-program

Om du vill använda ett Weibo-konto som identitetsprovider i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klientorganisation som representerar det. Om du inte redan har ett Weibo-konto [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us)kan du registrera dig på .

1. Logga in på [Weibos utvecklarportal](https://open.weibo.com/) med dina Weibo-kontouppgifter.
1. När du har loggat in väljer du ditt visningsnamn i det övre högra hörnet.
1. I listrutan **väljer** du (redigera utvecklarinformation).
1. Ange den information som krävs och **välj** (skicka).
1. Slutför e-postverifieringsprocessen.
1. Gå till [sidan identitetsverifiering](https://open.weibo.com/developers/identity/edit).
1. Ange den information som krävs och **välj** (skicka).

### <a name="register-a-weibo-application"></a>Registrera en Weibo-applikation

1. Gå till den [nya registreringssidan för Weibo-appen](https://open.weibo.com/apps/new).
1. Ange nödvändig ansökningsinformation.
1. Välj**中ン**(skapa).
1. Kopiera värdena för **Appnyckel** och **App Secret**. Du behöver båda dessa för att lägga till identitetsprovidern till din klientorganisation.
1. Ladda upp de nödvändiga bilderna och ange nödvändig information.
1. Välj**中ン**ンンン (spara).
1. Välj **高级信息** (avancerad information).
1. Välj **(redigera)** bredvid fältet för OAuth2.0 **授权设置** (omdirigerings-URL).
1. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` för OAuth2.0 **授权设置**中ンン (omdirigera URL). Om klientnamnet till exempel är contoso anger `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`du webbadressen som .
1. Välj**中ン**(skicka).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Konfigurera ett Weibo-konto som identitetsleverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **Weibo (Förhandsversion)**.
1. Ange ett **namn**. Till exempel *Weibo*.
1. För **klient-ID**anger du appnyckeln för Weibo-programmet som du skapade tidigare.
1. För **klienthemligheten**anger du den Apphemlighet som du spelade in.
1. Välj **Spara**.
