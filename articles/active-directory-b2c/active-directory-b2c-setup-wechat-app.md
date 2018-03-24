---
title: 'Azure Active Directory B2C: WeChat konfiguration | Microsoft Docs'
description: Ange registrering och inloggning för konsumenter med WeChat konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.openlocfilehash: ca12c84042f92dafff67dc10ce6b56b77c0456eb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Azure Active Directory B2C: Ange registrering och inloggning för konsumenter med WeChat konton

> [!NOTE]
> Den här funktionen är i förhandsgranskningen.
> 

## <a name="create-a-wechat-application"></a>Skapa ett WeChat program

Om du vill använda WeChat som en identitetsleverantör i Azure Active Directory (AD Azure) B2C måste du skapa ett WeChat program och ange rätt parametrar. Du behöver en WeChat-konto för att göra detta. Om du inte har någon kan skaffa du en genom att registrera dig genom en av sina mobila appar eller genom att använda QT-nummer. Efter det att få ditt konto som har registrerats med utvecklarprogram WeChat. Du hittar mer information [här](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrera en WeChat-program

1. Gå till [ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) och logga in.
2. Klicka på**管理中心**(management center).
3. Följ de nödvändiga stegen för att registrera ett nytt program.
4. För**授权回调域**(återanrop URL), ange `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Till exempel om din `tenant_name` är contoso.onmicrosoft.com, ange URL som ska vara `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Hitta och kopiera den **APP-ID** och **APPKEY**. Du behöver dessa senare.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurera WeChat som en identitetsleverantör i din klientorganisation
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure-portalen.
2. Klicka på B2C-funktionsbladet **identitetsleverantörer**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för providerkonfigurationen identitet. Till exempel ange ”WeChat”.
5. Klicka på **identitet providertyp**väljer **WeChat**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsleverantören.**
7. Ange den **Appkey** som du kopierade tidigare som den **klient-ID**.
8. Ange den **App hemlighet** som du kopierade tidigare som den **Klienthemlighet**.
9. Klicka på **OK** och klicka sedan på **skapa** att spara konfigurationen WeChat.

