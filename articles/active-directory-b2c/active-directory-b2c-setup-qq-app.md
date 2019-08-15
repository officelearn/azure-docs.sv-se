---
title: Konfigurera registrering och inloggning med ett QQ-konto med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrering och inloggning till kunder med QQ-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9679e3216184c0dc5b8dee241c30fc69d5423aeb
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963762"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett QQ-konto med hjälp av Azure Active Directory B2C

> [!NOTE]
> Den här funktionen är en förhandsversion.
> 

## <a name="create-a-qq-application"></a>Skapa ett QQ-program

Om du vill använda ett QQ-konto som identitets leverantör i Azure Active Directory (Azure AD) B2C måste du skapa ett program i din klient organisation som representerar det. Om du inte redan har ett QQ-konto kan du hämta det på [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registrera dig för QQ Developer-programmet

1. Logga in på [QQ Developer-portalen](http://open.qq.com) med dina QQ-kontoautentiseringsuppgifter.
2. När du har loggat in [https://open.qq.com/reg](https://open.qq.com/reg) går du till för att registrera dig som utvecklare.
3. Välj**个人**(enskild utvecklare).
4. Ange den information som krävs och välj**下一步**(nästa steg).
5. Slutför verifierings processen för e-post. Du måste vänta några dagar innan du har registrerat dig som utvecklare. 

### <a name="register-a-qq-application"></a>Registrera ett QQ-program

1. Gå till [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Välj**应用管理**(app Management).
5. Välj**创建应用**(skapa app) och ange den information som krävs.
7. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` i**授权回调域**(återanrops-URL). Om din `tenant_name` till exempel är contoso anger du URL: en `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
8. Välj**创建应用**(skapa app).
9. På sidan bekräftelse väljer du**应用管理**(app Management) för att gå tillbaka till sidan för hantering av appar.
10. Välj**查看**(Visa) bredvid den app som du skapade.
11. Välj**修改**(redigera).
12. Kopiera **app-ID** och **app-nyckel**. Du behöver båda dessa värden för att lägga till identitets leverantören till din klient organisation.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurera QQ som identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Skriv till exempel *QQ*.
6. Välj **typ av identitetsprovider**, Välj **QQ (för hands version)** och klicka på **OK**.
7. Välj **Konfigurera den här identitets leverantören** och ange det app-ID som du registrerade tidigare som **klient-ID** och ange den app-nyckel som du registrerade som **klient hemlighet** för det QQ-program som du skapade tidigare.
8. Klicka på **OK** och sedan på **skapa** för att spara QQ-konfigurationen.

