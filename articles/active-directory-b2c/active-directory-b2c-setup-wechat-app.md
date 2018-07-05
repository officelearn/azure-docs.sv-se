---
title: WeChat konfiguration i Azure Active Directory B2C | Microsoft Docs
description: Ge registrera dig och logga in till konsumenter med WeChat konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a18d41a4f45b147790a17664156659d282e710d4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445978"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Azure Active Directory B2C: Ge registrera dig och logga in till konsumenter med WeChat konton

> [!NOTE]
> Den här funktionen är en förhandsversion.
> 

## <a name="create-a-wechat-application"></a>Skapa ett WeChat-program

Om du vill använda WeChat som en identitetsprovider i Azure Active Directory (Azure AD) B2C måste du skapa ett WeChat program och ange rätt parametrar. Du behöver en WeChat-konto för att göra detta. Om du inte har någon kan skaffa du en genom att registrera dig genom en av sina mobila appar eller genom att använda ditt QT nummer. Efter det att få din kontoinformation som registrerats med WeChat-utvecklarprogrammet. Du hittar mer information [här](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrera ett WeChat-program

1. Gå till [https://open.weixin.qq.com/](https://open.weixin.qq.com/) och logga in.
2. Klicka på**管理中心**(management center).
3. Följ de nödvändiga stegen för att registrera ett nytt program.
4. För**授权回调域**(Motringnings-URL), ange `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Exempel: om din `tenant_name` är contoso.onmicrosoft.com, ange URL: en ska vara `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Hitta och kopiera den **APP-ID** och **APPNYCKELN**. Du behöver dessa senare.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurera WeChat som en identitetsprovider i din klient
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure portal.
2. Klicka på B2C-funktionsbladet **identitetsprovidrar**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för konfigurationen av identity-providern. Till exempel ange ”WeChat”.
5. Klicka på **identifiera providertyp**väljer **WeChat**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsprovidern**
7. Ange den **Appnyckeln** som du kopierade tidigare som den **klient-ID**.
8. Ange den **Apphemlighet** som du kopierade tidigare som den **Klienthemlighet**.
9. Klicka på **OK** och klicka sedan på **skapa** att spara din WeChat konfiguration.

