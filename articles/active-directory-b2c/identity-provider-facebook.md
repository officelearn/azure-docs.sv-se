---
title: Konfigurera registrering och inloggning med ett Facebook-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Facebook-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0a9026db19d453100971739dcf633629a3f06d43
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85388311"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Facebook-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Skapa ett Facebook-program

Om du vill använda ett Facebook-konto som [identitets leverantör](authorization-code-flow.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klient som representerar det. Om du inte redan har ett Facebook-konto kan du registrera dig på [https://www.facebook.com/](https://www.facebook.com/) .

1. Logga in på [Facebook för utvecklare](https://developers.facebook.com/) med dina Facebook-kontoautentiseringsuppgifter.
1. Om du inte redan har gjort det måste du registrera dig som en Facebook-utvecklare. Om du vill göra det väljer du **Kom igång** i det övre högra hörnet på sidan, accepterar Facebook-principer och slutför registrerings stegen.
1. Välj **Mina appar** och sedan **skapa app**.
1. Ange ett **visnings namn** och en giltig **kontakt-e-postadress**.
1. Välj **skapa app-ID**. Detta kan kräva att du accepterar Facebook-plattforms principer och slutför en säkerhets kontroll online.
1. Välj **Inställningar**  >  **Basic**.
1. Välj en **kategori**, till exempel `Business and Pages` . Det här värdet krävs av Facebook, men används inte för Azure AD B2C.
1. Längst ned på sidan väljer du **Lägg till plattform**och väljer sedan **webbplats**.
1. I **webbplats-URL**skriver du `https://your-tenant-name.b2clogin.com/` ersätta `your-tenant-name` med namnet på din klient. Ange en URL för **Sekretess policyns URL**, till exempel `http://www.contoso.com` . Princip-URL: en är en sida som du upprätthåller för att tillhandahålla sekretess information för ditt program.
1. Välj **Spara ändringar**.
1. Kopiera värdet för **app-ID**överst på sidan.
1. Välj **Visa** och kopiera värdet för **appens hemlighet**. Du använder båda alternativen för att konfigurera Facebook som en identitets leverantör i din klient organisation. **App Secret** är en viktig säkerhets autentiseringsuppgift.
1. Välj plus tecknet bredvid **produkter**och välj sedan **Konfigurera** under **Facebook-inloggning**.
1. Under **Facebook-inloggning**väljer du **Inställningar**.
1. I **giltiga OAuth-omdirigerings-URI: er**anger du `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ersätt `your-tenant-name` med namnet på din klient. Välj **Spara ändringar** längst ned på sidan.
1. Om du vill göra ditt Facebook-program tillgängligt för Azure AD B2C väljer du status väljaren längst upp till höger på sidan och **aktiverar det för att göra** programmet offentligt och väljer sedan **Växla läge**.  I det här läget bör statusen ändras från **utveckling** till **Live**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurera ett Facebook-konto som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **Facebook**.
1. Ange ett **namn**. Till exempel *Facebook*.
1. För **klient-ID**anger du app-ID: t för det Facebook-program som du skapade tidigare.
1. För **klient hemligheten**anger du appens hemlighet som du har spelat in.
1. Välj **Spara**.
