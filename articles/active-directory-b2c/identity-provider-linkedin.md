---
title: Konfigurera registrering och inloggning med ett LinkedIn-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med LinkedIn-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10c460775bcb63028f03d0e8d0b1f7ed1507cdb4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259466"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett LinkedIn-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Skapa ett LinkedIn-program

Om du vill använda ett LinkedIn-konto som [identitets leverantör](authorization-code-flow.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klient som representerar det. Om du inte redan har ett LinkedIn-konto kan du registrera dig på [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Logga in på [webbplatsen LinkedIn-utvecklare](https://www.developer.linkedin.com/) med dina LinkedIn-kontoautentiseringsuppgifter.
1. Välj **Mina appar**och klicka sedan på **skapa app**.
1. Ange **appens namn**, **LinkedIn-sida**, **URL för sekretess policy**och **app-logotyp**.
1. Godkänn LinkedIn- **API-villkoren för användning** och klicka på **skapa app**.
1. Välj fliken **autentisering** . Under **nycklar för autentisering**kopierar du värdena för **klient-ID** och **klient hemlighet**. Du behöver båda dessa för att konfigurera LinkedIn som en identitets leverantör i din klient. **Klient hemlighet** är en viktig säkerhets autentiseringsuppgift.
1. Välj Redigera penna bredvid **auktoriserade omdirigerings-URL: er för din app**och välj sedan **Lägg till omdirigerings-URL**. Skriv `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` och Ersätt `your-tenant-name` med namnet på din klient. Du måste använda små bokstäver när du anger ditt klient namn även om klienten har definierats med versaler i Azure AD B2C. Välj **Uppdatera**.
2. Din LinkedIn-app är som standard inte godkänd för omfattningar som är relaterade till inloggning. Om du vill begära en granskning väljer du fliken **produkter** och väljer sedan **Logga in med LinkedIn**. När granskningen är klar kommer de obligatoriska omfattningarna att läggas till i ditt program.
   > [!NOTE]
   > Du kan visa de omfattningar som för närvarande är tillåtna för din app på fliken **auth** i avsnittet **OAuth 2,0-omfång** .

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Konfigurera ett LinkedIn-konto som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj **LinkedIn**.
1. Ange ett **namn**. Till exempel *LinkedIn*.
1. För **klient-ID**anger du klient-ID för LinkedIn-programmet som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.

## <a name="migration-from-v10-to-v20"></a>Migrering från v 1.0 till v 2.0

LinkedIn har nyligen [uppdaterat sina API: er från v 1.0 till v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Som en del av migreringen kan Azure AD B2C bara hämta det fullständiga namnet på LinkedIn-användaren under registreringen. Om en e-postadress är en av de attribut som samlas in under registreringen måste användaren manuellt ange e-postadressen och verifiera den.
