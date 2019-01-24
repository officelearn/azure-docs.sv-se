---
title: Konfigurera registrering och inloggning med ett Google-konto med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Google-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c53c948df295365df4c9643c06a6d309d9a88d2b
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54843948"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Google-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-google-application"></a>Skapa ett Google-program

Om du vill använda ett Google-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Google-konto kan du hämta den på [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Logga in på den [Google utvecklare konsolen](https://console.developers.google.com/) med dina Google-kontouppgifter.
2. Välj **skapa projekt**, och klicka sedan på **skapa**. Om du har skapat projekt innan du kan välja projektlistan och välj sedan **nytt projekt**.
3. Ange en **projektnamn**, klickar du på **skapa**, och kontrollera att du använder det nya projektet.
3. Välj **autentiseringsuppgifter** i den vänstra menyn och välj sedan **skapa autentiseringsuppgifter** > **Oauth klient-ID**.
4. Välj **konfigurera godkännandeskärmen**.
5. Välj eller ange en giltig **e-postadress**, ange en **produktnamn som visas för användare**, lägga till `b2clogin.com` till **auktoriserade domäner**, och klicka på **spara** .
6. Under **programtyp**väljer **webbprogram**.
7. Ange en **namn** för ditt program ange `https://your-tenant-name.b2clogin.com` i **behörighet JavaScript ursprung**, och `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **auktoriserade omdirigerings-URI: er**. Ersätt `your-tenant-name` med namnet på din klient. Du måste använda gemener när du anger ditt klientnamn även om klienten har definierats med versaler i Azure AD B2C.
8. Klicka på **Skapa**.
9. Kopiera värdena för **klient-ID** och **klienthemlighet**. Du måste båda för att konfigurera Google som en identitetsprovider i din klient. **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurera ett Google-konto som identitetsprovider

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Google*.
6. Välj **identifiera providertyp**väljer **Google**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange klient-ID som du antecknade tidigare som den **klient-ID** och ange Klienthemligheten som du registrerade som den **klienthemlighet**av Google-programmet som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din Google-konfiguration.

