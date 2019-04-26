---
title: Konfigurera registrering och inloggning med ett Google-konto – Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Google-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1d4f7460d53e721ca1cba7aba6c6ef9c45111ee5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316416"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Google-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-google-application"></a>Skapa ett Google-program

Att använda ett Google-konto som ett [identitetsprovider](active-directory-b2c-reference-oauth-code.md) i Azure Active Directory (Azure AD) B2C måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Google-konto kan du hämta den på [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Logga in på den [Google utvecklare konsolen](https://console.developers.google.com/) med dina Google-kontouppgifter.
2. I det övre vänstra hörnet på sidan Välj projektlistan och välj sedan **nytt projekt**.
3. Ange en **projektnamn**, klickar du på **skapa**, och kontrollera att du använder det nya projektet.
4. Välj **autentiseringsuppgifter** i den vänstra menyn och välj sedan **skapa autentiseringsuppgifter** > **Oauth klient-ID**.
5. Under **programtyp**väljer **webbprogram**.
6. Ange en **namn** för ditt program ange `https://your-tenant-name.b2clogin.com` i **behörighet JavaScript ursprung**, och `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **auktoriserade omdirigerings-URI: er**. Ersätt `your-tenant-name` med namnet på din klient. Du måste använda gemener när du anger ditt klientnamn även om klienten har definierats med versaler i Azure AD B2C.
7. Klicka på **Skapa**.
8. Kopiera värdena för **klient-ID** och **klienthemlighet**. Du måste båda för att konfigurera Google som en identitetsprovider i din klient. **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurera ett Google-konto som identitetsprovider

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Google*.
6. Välj **identifiera providertyp**väljer **Google**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange klient-ID som du antecknade tidigare som den **klient-ID** och ange Klienthemligheten som du registrerade som den **klienthemlighet**av Google-programmet som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din Google-konfiguration.

