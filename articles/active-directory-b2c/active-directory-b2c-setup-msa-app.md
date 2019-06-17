---
title: Konfigurera registrering och inloggning med ett Microsoft-konto – Azure Active Directory B2C
description: Tillhandahålla registrera dig och logga in till kunder med Microsoft-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 63aa93e72d76d430fc7e026478d91e496f27eae6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055109"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Microsoft-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Skapa ett program för Microsoft-konto

Att använda ett microsoftkonto som ett [identitetsprovider](active-directory-b2c-reference-oidc.md) i Azure Active Directory (Azure AD) B2C måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Microsoft-konto, kan du hämta den på [ https://www.live.com/ ](https://www.live.com/).

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **appregistreringar**.
1. Välj **ny registrering**
1. Ange en **namn** för ditt program. Till exempel *MSAapp1*.
1. Under **stöds kontotyper**väljer **konton alla organisationskatalog och personliga Microsoft-konton (t.ex. Skype, Xbox, Outlook.com)** . Det här alternativet riktar sig mot den bredaste uppsättningen Microsoft-identiteter.

   Läs mer på det annat konto typ du valt [Snabbstart: Registrera ett program med Microsoft identity-plattformen](../active-directory/develop/quickstart-register-app.md).
1. Under **omdirigerings-URI (valfritt)** väljer **Web** och ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i textrutan. Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klientorganisation.
1. Välj **registrera**
1. Post i **(klient)-ID: T** visas på översiktssidan för programmet. Du behöver den när du konfigurerar identitetsprovidern i nästa avsnitt.
1. Välj **certifikat och hemligheter**
1. Klicka på **nya klienthemlighet**
1. Ange en **beskrivning** för hemlighet, till exempel *programlösenord 1*, och klicka sedan på **Lägg till**.
1. Anteckna lösenordet för program som visas i den **värdet** kolumn. Du behöver den när du konfigurerar identitetsprovidern i nästa avsnitt.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurera ett Microsoft-konto som identitetsprovider

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
1. Ange en **namn**. Ange till exempel *MSA*.
1. Välj **identifiera providertyp**väljer **Account**, och klicka på **OK**.
1. Välj **ställa in den här identitetsprovidern** och ange det program (klient)-ID som du antecknade tidigare i den **klient-ID** text och ange klienthemlighet som registrerades i den **klienten hemlighet** textrutan.
1. Klicka på **OK** och klicka sedan på **skapa** att spara din konfiguration för Microsoft-konto.
