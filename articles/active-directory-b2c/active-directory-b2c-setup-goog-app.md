---
title: Konfigurera registrering och inloggning med ett Google-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Google-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1e23c79b1e09f3e3a7aaa21b9257bfe6bd43f7e8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950483"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Google-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-google-application"></a>Skapa ett Google-program

Om du vill använda ett Google-konto som [identitets leverantör](active-directory-b2c-reference-oauth-code.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klient som representerar det. Om du inte redan har ett Google-konto kan du registrera dig på [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Logga in på [Google Developer-konsolen](https://console.developers.google.com/) med dina Google-kontoautentiseringsuppgifter.
1. I det övre vänstra hörnet på sidan väljer du projekt listan och väljer sedan **nytt projekt**.
1. Ange ett **projekt namn**, klicka på **skapa**och kontrol lera att du använder det nya projektet.
1. Välj **autentiseringsuppgifter** på den vänstra menyn och välj sedan **skapa autentiseringsuppgifter** > **OAuth-klient-ID**.
1. Under **program typ**väljer du **webb program**.
1. Ange ett **namn** för programmet, ange `https://your-tenant-name.b2clogin.com` i **behöriga JavaScript-ursprung**och `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **auktoriserade omdirigerings-URI: er**. Ersätt `your-tenant-name` med namnet på din klient. Du måste använda små bokstäver när du anger ditt klient namn även om klienten har definierats med versaler i Azure AD B2C.
1. Klicka på **Skapa**.
1. Kopiera värdena för **klient-ID** och **klient hemlighet**. Du behöver båda dessa för att kunna konfigurera Google som en identitets leverantör i din klient organisation. **Klient hemlighet** är en viktig säkerhets autentiseringsuppgift.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurera ett Google-konto som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **Google**.
1. Ange ett **namn**. Till exempel *Google*.
1. För **klient-ID**anger du klient-ID för det Google-program som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.
