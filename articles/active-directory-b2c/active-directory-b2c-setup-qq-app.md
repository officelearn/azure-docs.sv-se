---
title: QT konfigurationen i Azure Active Directory B2C | Microsoft Docs
description: Ange registrering och inloggning för konsumenter med QT konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7a33a1b2a68b82b1d65b1187547695cccd7c395f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711679"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C: Ange registrering och inloggning för konsumenter med QT konton

> [!NOTE]
> Den här funktionen är en förhandsversion.
> 

## <a name="create-a-qq-application"></a>Skapa ett QT-program

Om du vill använda QT som en identitetsleverantör i Azure Active Directory (AD Azure) B2C måste du skapa ett QT program och ange rätt parametrar. Du behöver en QT-konto för att göra detta. Om du inte har någon kan du skaffa ett på [ https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033 ](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registrera dig för utvecklarprogram QT

1. Gå till den [QT developer-portalen](http://open.qq.com) och logga in med autentiseringsuppgifterna för ditt QT.
2. När du har loggat in kan du gå till [ http://open.qq.com/reg ](http://open.qq.com/reg) att registrera dig som en utvecklare.
3. Välj på menyn**个人**(enskilda developer).
4. Ange nödvändig information i formuläret och klicka på**下一步**(nästa steg).
5. Slutföra verifieringsprocessen för e-post.

> [!NOTE]
> Du kommer att behöva vänta ett par dagar att godkänna efter registrering som utvecklare. 

### <a name="register-a-qq-application"></a>Registrera en QT-program

1. Gå till [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Klicka på**应用管理**(hantering).
3. Klicka på**创建应用**(skapa app).
4. Ange information om nödvändiga app.
5. Klicka på**创建应用**(skapa app).
6. Ange informationen som krävs.
7. För den**授权回调域**(återanrop URL), ange `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Till exempel om din `tenant_name` är contoso.onmicrosoft.com, ange URL som ska vara `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Klicka på**创建应用**(skapa app).
9. På bekräftelsesidan klickar du på**应用管理**(hantering) kan gå tillbaka till sidan för hantering av app.
10. Klicka på**查看**(Visa) bredvid den app som du nyss skapade.
11. Klicka på**修改**(redigera).
12. Kopiera från sidans överkant i **APP-ID** och **APPKEY**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Konfigurera QT som en identitetsleverantör i din klientorganisation
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure-portalen.
2. Klicka på B2C-funktionsbladet **identitetsleverantörer**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för providerkonfigurationen identitet. Till exempel ange ”QT”.
5. Klicka på **identitet providertyp**väljer **QT**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsleverantören.**
7. Ange den **Appkey** som du kopierade tidigare som den **klient-ID**.
8. Ange den **App hemlighet** som du kopierade tidigare som den **Klienthemlighet**.
9. Klicka på **OK** och klicka sedan på **skapa** spara QT-konfigurationen.

