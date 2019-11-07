---
title: Konfigurera registrering och inloggning med ett Microsoft-konto – Azure Active Directory B2C
description: Tillhandahålla registrering och inloggning till kunder med Microsoft-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a921d6110fd0294012e2e59542ba2c82d63ffcde
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73642083"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med en Microsoft-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Skapa ett Microsoft-konto program

Om du vill använda en Microsoft-konto som [identitets leverantör](active-directory-b2c-reference-oidc.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i Azure AD-klienten. Azure AD-klienten är inte samma som din Azure AD B2C klient. Om du inte redan har ett Microsoft-konto kan du hämta ett på [https://www.live.com/](https://www.live.com/).

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ny registrering**.
1. Ange ett **namn** för ditt program. Till exempel *MSAapp1*.
1. Under **konto typer som stöds**väljer du **konton i valfri organisations katalog och personliga Microsoft-konton (t. ex. Skype, Xbox, Outlook.com)** . Det här alternativet riktar sig till den bredaste uppsättningen av Microsoft-identiteter.

   Mer information om de olika alternativen för konto typer finns i [snabb start: registrera ett program med Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).
1. Under **omdirigerings-URI (valfritt)** väljer du **webb** och anger `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i text rutan. Ersätt `your-tenant-name` med ditt Azure AD B2C klient namn.
1. Välj **register**
1. Registrera **program-ID (klient)** som visas på sidan program översikt. Du behöver detta när du konfigurerar identitets leverantören i nästa avsnitt.
1. Välj **certifikat & hemligheter**
1. Klicka på **ny klient hemlighet**
1. Ange en **Beskrivning** av hemligheten, till exempel *program lösen ord 1*, och klicka sedan på **Lägg till**.
1. Registrera program lösen ordet som visas i kolumnen **värde** . Du behöver detta när du konfigurerar identitets leverantören i nästa avsnitt.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurera en Microsoft-konto som identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **Microsoft-konto**.
1. Ange ett **namn**. Till exempel *MSA*.
1. För **klient-ID**anger du program-ID: t för det Azure AD-program som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.
