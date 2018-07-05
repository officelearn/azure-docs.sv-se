---
title: QT konfiguration i Azure Active Directory B2C | Microsoft Docs
description: Ge registrera dig och logga in till konsumenter med QT konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1f9a0f56158f08dd3b22078f111c9ec6911b726c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444437"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C: Ge registrera dig och logga in till konsumenter med QT konton

> [!NOTE]
> Den här funktionen är en förhandsversion.
> 

## <a name="create-a-qq-application"></a>Skapa ett QT-program

För att använda QT som en identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett QT program och ange rätt parametrar. Du behöver en QT-konto för att göra detta. Om du inte har någon kan du skaffa en vid [ https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033 ](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registrera dig för QT developer-programmet

1. Gå till den [QT utvecklarportalen](http://open.qq.com) och logga in med autentiseringsuppgifterna för ditt QT.
2. När du loggar in, gå till [ http://open.qq.com/reg ](http://open.qq.com/reg) att registrera dig själv som utvecklare.
3. I menyn, Välj**个人**(enskilda utvecklare).
4. Ange nödvändig information i formuläret och klicka på**下一步**(nästa steg).
5. Slutför verifieringen e-post.

> [!NOTE]
> Du måste vänta ett par dagar att godkänna när du har registrerat som en utvecklare. 

### <a name="register-a-qq-application"></a>Registrera ett QT-program

1. Gå till [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Klicka på**应用管理**(app management).
3. Klicka på**创建应用**(skapa app).
4. Ange appinformation som behövs.
5. Klicka på**创建应用**(skapa app).
6. Ange informationen som krävs.
7. För den**授权回调域**(Motringnings-URL) anger du `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Exempel: om din `tenant_name` är contoso.onmicrosoft.com, ange URL: en ska vara `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Klicka på**创建应用**(skapa app).
9. På bekräftelsesidan klickar du på**应用管理**(app management) för att återgå till sidan för hantering av appen.
10. Klicka på**查看**(Visa) bredvid appen som du nyss skapade.
11. Klicka på**修改**(redigera).
12. Högst upp på sidan, kopiera den **APP-ID** och **APPNYCKELN**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Konfigurera QT som en identitetsprovider i din klient
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure portal.
2. Klicka på B2C-funktionsbladet **identitetsprovidrar**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för konfigurationen av identity-providern. Till exempel ange ”QT”.
5. Klicka på **identifiera providertyp**väljer **QT**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsprovidern**
7. Ange den **Appnyckeln** som du kopierade tidigare som den **klient-ID**.
8. Ange den **Apphemlighet** som du kopierade tidigare som den **Klienthemlighet**.
9. Klicka på **OK** och klicka sedan på **skapa** att spara din QT-konfiguration.

