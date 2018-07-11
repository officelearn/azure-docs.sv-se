---
title: Konfigurera registrering och inloggning med en QT-konto med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med QT konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 82668446f139a5a003c33178e2d415a9314c61bc
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952185"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med en QT-konto med hjälp av Azure Active Directory B2C

> [!NOTE]
> Den här funktionen är en förhandsversion.
> 

## <a name="create-a-qq-application"></a>Skapa ett QT-program

Om du vill använda ett QT-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett QT-konto, kan du hämta den på [ https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033 ](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registrera dig för QT developer-programmet

1. Logga in på den [QT utvecklarportalen](http://open.qq.com) med autentiseringsuppgifterna för ditt QT.
2. När du loggar in, gå till [ http://open.qq.com/reg ](http://open.qq.com/reg) att registrera dig själv som utvecklare.
3. Välj**个人**(enskilda utvecklare).
4. Ange informationen som krävs och välj**下一步**(nästa steg).
5. Slutför verifieringen e-post. Du måste vänta ett par dagar att godkänna när du har registrerat som en utvecklare. 

### <a name="register-a-qq-application"></a>Registrera ett QT-program

1. Gå till [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Välj**应用管理**(app management).
5. Välj**创建应用**(skapa app) och ange nödvändig information.
7. Ange `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` i**授权回调域**(Motringnings-URL). Exempel: om din `tenant_name` är contoso.onmicrosoft.com, ange URL: en ska vara `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Välj**创建应用**(skapa app).
9. På bekräftelsesidan väljer**应用管理**(app management) för att återgå till sidan för hantering av appen.
10. Välj**查看**(Visa) bredvid appen som du skapat.
11. Välj**修改**(redigera).
12. Kopiera den **APP-ID** och **APPNYCKELN**. Du måste båda dessa värden för att lägga till identitetsleverantören i din klient.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurera QT som identitetsprovider

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**. 

    ![Växla till Azure AD B2C-klientorganisationen](./media/active-directory-b2c-setup-qq-app/switch-directories.png)

    Välj den katalog som innehåller din klient.

    ![Välj katalog](./media/active-directory-b2c-setup-qq-app/select-directory.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *QT*.
6. Välj **identifiera providertyp**väljer **QT (förhandsversion)**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange det ID som du antecknade tidigare som den **klient-ID** och ange APP-nyckeln som du registrerade som den **klienthemlighet** av QT program som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din QT-konfiguration.

