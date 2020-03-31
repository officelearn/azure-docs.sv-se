---
title: Konfigurera registrering och inloggning med ett Google-konto
titleSuffix: Azure AD B2C
description: Ge registrering och inloggning till kunder med Google-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 48955caddb64069f897078f5e47066d9f11d119b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188148"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Google-konto med Azure Active Directory B2C

## <a name="create-a-google-application"></a>Skapa ett Google-program

Om du vill använda ett Google-konto som [identitetsleverantör](authorization-code-flow.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i Google Developers Console. Om du inte redan har ett Google-konto [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)kan du registrera dig på .

1. Logga in på [Google Developers Console](https://console.developers.google.com/) med dina google-kontouppgifter.
1. Markera projektlistan längst upp till vänster på sidan och välj sedan **Nytt projekt**.
1. Ange ett **projektnamn**, välj **Skapa**.
1. Kontrollera att du använder det nya projektet genom att välja projektrullgardinsmenyn längst upp till vänster på skärmen, välj projektet efter namn och välj sedan **Öppna**.
1. Välj **OAuth-medgivandeskärm** i den vänstra menyn, välj **Extern**och välj sedan **Skapa**.
Ange ett **namn** för ditt program. Ange *b2clogin.com* i avsnittet **Auktoriserade domäner** och välj **Spara**.
1. Välj **Autentiseringsuppgifter** på den vänstra menyn och välj sedan **Skapa autentiseringsuppgifter** > **Oauth-klient-ID**.
1. Under **Programtyp**väljer du **Webbprogram**.
1. Ange ett **namn** för `https://your-tenant-name.b2clogin.com` ditt program, ange **auktoriserat JavaScript-ursprung**och `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **Auktoriserade omdirigerings-URI:er**. Ersätt `your-tenant-name` med namnet på din klient. Du måste använda alla gemener när du anger ditt klientnamn även om klienten definieras med versaler i Azure AD B2C.
1. Klicka på **Skapa**.
1. Kopiera värdena för **klient-ID** och **klienthemlighet**. Du behöver båda för att konfigurera Google som en identitetsleverantör i din klientorganisation. **Klienthemlighet** är en viktig säkerhetsautentiseringsinformation.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurera ett Google-konto som identitetsleverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **Google**.
1. Ange ett **namn**. Till exempel *Google*.
1. För **klient-ID**anger du klient-ID för Google-programmet som du skapade tidigare.
1. För **klienthemligheten**anger du den klienthemlighet som du spelade in.
1. Välj **Spara**.
