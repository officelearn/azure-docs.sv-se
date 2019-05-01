---
title: Konfigurera registrering och inloggning med ett Weibo-konto med Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Weibo konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9701d1583a19be46c4c72a82d9f376a8db0c625c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704249"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Weibo-konto med Azure Active Directory B2C

> [!NOTE]
> Den här funktionen är en förhandsversion.
> 

## <a name="create-a-weibo-application"></a>Skapa ett Weibo-program

Om du vill använda ett Weibo-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Weibo-konto, kan du hämta den på [ https://weibo.com/signup/signup.php?lang=en-us ](https://weibo.com/signup/signup.php?lang=en-us).

1. Logga in på den [Weibo utvecklarportalen](https://open.weibo.com/) med autentiseringsuppgifterna för ditt Weibo.
2. När du har loggat in väljer du ditt namn i det övre högra hörnet.
3. Välj i listrutan**编辑开发者信息**(redigera information för utvecklare).
4. Ange informationen som krävs och välj**提交**(skicka).
5. Slutför verifieringen e-post.
6. Gå till den [verifiering sidan](https://open.weibo.com/developers/identity/edit).
7. Ange informationen som krävs och välj**提交**(skicka).

### <a name="register-a-weibo-application"></a>Registrera ett Weibo-program

1. Gå till den [nya Weibo appregistreringssidan](https://open.weibo.com/apps/new).
2. Ange informationen som programmet.
3. Välj**创建**(skapa).
4. Kopiera värdena för **Appnyckeln** och **Apphemlighet**. Du måste båda dessa att lägga till identitetsleverantören till din klient.
5. Överföra foton som krävs och ange nödvändig information.
6. Välj**保存以上信息**(spara).
7. Välj**高级信息**(avancerad information).
8. Välj**编辑**(redigera) bredvid fältet för OAuth2.0**授权设置**(omdirigerings-URL).
9. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` för OAuth2.0**授权设置**(omdirigerings-URL). Till exempel om ditt klientnamn är contoso anger du URL: en ska vara `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
10. Välj**提交**(skicka).  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Konfigurera ett Weibo-konto som identitetsprovider

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Weibo*.
6. Välj **identifiera providertyp**väljer **Weibo (förhandsversion)**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange den App-nyckel som du antecknade tidigare som den **klient-ID** och ange App-hemlighet som du registrerade som den **klienthemlighet** av Weibo programmet som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din Weibo konfiguration.
