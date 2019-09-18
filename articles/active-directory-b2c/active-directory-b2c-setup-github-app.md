---
title: Konfigurera registrering och inloggning med ett GitHub-konto – Azure Active Directory B2C
description: Tillhandahålla registrera dig och logga in till kunder med GitHub-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 74c663d8847c2829a5d9466f8e601dd44593a6f8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065204"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett GitHub-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Skapa ett GitHub OAuth-program

Om du vill använda ett GitHub-konto som [identitets leverantör](active-directory-b2c-reference-oauth-code.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klient som representerar det. Om du inte redan har ett GitHub-konto kan du registrera dig på [https://www.github.com/](https://www.github.com/).

1. Logga in på den [GitHub Developer](https://github.com/settings/developers) webbplats med dina autentiseringsuppgifter för GitHub.
1. Välj **OAuth appar** och välj sedan **ny OAuth-App**.
1. Ange en **programnamn** och din **Hemsides-URL**.
1. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **Motringnings-URL för auktorisering**. Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient. Använd gemener när du anger ditt klientnamn även om klienten har definierats med versaler i Azure AD B2C.
1. Klicka på **registrera program**.
1. Kopiera värdena för **klient-ID** och **Klienthemlighet**. Du måste båda för att lägga till identitetsleverantören i din klient.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurera ett GitHub-konto som identitetsprovider

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **GitHub (för hands version)** .
1. Ange ett **namn**. Till exempel *GitHub*.
1. För **klient-ID**anger du klient-ID för det GitHub-program som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.
