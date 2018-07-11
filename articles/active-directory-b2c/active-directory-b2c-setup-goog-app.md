---
title: Konfigurera registrering och inloggning med ett Google-konto med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Google-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dd0bf50d73b70e37195e8e5e45336b68e4e883e7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915647"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Google-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-google-application"></a>Skapa ett Google-program

Om du vill använda ett Google-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Google-konto kan du hämta den på [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Logga in på den [Google utvecklare konsolen](https://console.developers.google.com/) med dina Google-kontouppgifter.
2. Välj **skapa projekt**, och klicka sedan på **skapa**. Om du har skapat projekt innan du kan välja projektlistan och välj sedan **nytt projekt**.
3. Ange en **projektnamn**, och klicka sedan på **skapa**.
3. Välj **autentiseringsuppgifter** i den vänstra menyn och välj sedan **skapa autentiseringsuppgifter** > **Oauth klient-ID**.
4. Välj **konfigurera godkännandeskärmen**.
5. Välj eller ange en giltig **e-postadress**, ange en **produktnamn som visas för användare**, och klicka på **spara**.
6. Under **programtyp**väljer **webbprogram**.
7. Ange en **namn** för ditt program ange `https://login.microsoftonline.com` i **behörighet JavaScript ursprung**, och `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i **auktoriserade omdirigerings-URI: er**. Ersätt **{klient}** med klientens namn (exempel: contosob2c.onmicrosoft.com).
8. Klicka på **Skapa**.
9. Kopiera värdena för **klient-ID** och **klienthemlighet**. Du måste båda för att konfigurera Google som en identitetsprovider i din klient. **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurera ett Google-konto som identitetsprovider

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**. 

    ![Växla till Azure AD B2C-klientorganisationen](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Välj den katalog som innehåller din klient.

    ![Välj katalog](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Google*.
6. Välj **identifiera providertyp**väljer **Google**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange klient-ID som du antecknade tidigare som den **klient-ID** och ange Klienthemligheten som du registrerade som den **klienthemlighet**av Google-programmet som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din Google-konfiguration.

