---
title: Konfigurera registrering och inloggning med ett GitHub-konto med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med GitHub-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 88fffd28319101c112f848eebc6e8ee27f7f863e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952026"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett GitHub-konto med hjälp av Azure Active Directory B2C

> [!NOTE]
> Den här funktionen är en förhandsversion.
> 

Om du vill använda ett Github-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Github-konto, kan du hämta den på [ https://www.github.com/ ](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Skapa ett GitHub OAuth-program

1. Logga in på den [GitHub Developer](https://github.com/settings/developers) webbplats med dina autentiseringsuppgifter för GitHub.
2. Välj **OAuth appar** och välj sedan **registrera ett nytt program**.
3. Ange en **programnamn** och din **Hemsides-URL**.
4. Ange `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i **Motringnings-URL för auktorisering**. Ersätt **{klient}** med Azure AD B2C-klientens namn (exempel: contosob2c.onmicrosoft.com).
5. Klicka på **registrera program**.
6. Kopiera värdena för **klient-ID** och **Klienthemlighet**. Du måste båda för att lägga till identitetsleverantören i din klient.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurera ett GitHub-konto som identitetsprovider

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**. 

    ![Växla till Azure AD B2C-klientorganisationen](./media/active-directory-b2c-setup-github-app/switch-directories.png)

    Välj den katalog som innehåller din klient.

    ![Välj katalog](./media/active-directory-b2c-setup-github-app/select-directory.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Github*.
6. Välj **identifiera providertyp**väljer **Github (förhandsversion)**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange klient-Id som du antecknade tidigare som den **klient-ID** och ange Klienthemligheten som du registrerade som den **klienthemlighet**av programmet för Github-konto som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din konfiguration för Github-konto.