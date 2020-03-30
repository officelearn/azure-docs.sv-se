---
title: Konfigurera registrering och inloggning med ett Facebook-konto
titleSuffix: Azure AD B2C
description: Ge registrering och inloggning till kunder med Facebook-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cd0e19de88a6a65d72a2e7e19f7fca2a94d8da55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188281"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Facebook-konto med Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Skapa en Facebook-applikation

Om du vill använda ett Facebook-konto som [identitetsprovider](authorization-code-flow.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klientorganisation som representerar det. Om du inte redan har ett Facebook-konto [https://www.facebook.com/](https://www.facebook.com/)kan du registrera dig på .

1. Logga in på [Facebook för utvecklare](https://developers.facebook.com/) med dina Facebook-kontouppgifter.
1. Om du inte redan har gjort det måste du registrera dig som Facebook-utvecklare. Det gör du genom att välja **Kom igång i** det övre högra hörnet på sidan, acceptera Facebooks policyer och slutföra registreringsstegen.
1. Välj **Mina appar** och skapa sedan **appen**.
1. Ange ett **visningsnamn** och ett giltigt **kontaktmeddelande**.
1. Välj **Skapa app-ID**. Detta kan kräva att du accepterar Facebook-plattformspolicyer och slutför en säkerhetskontroll online.
1. Välj**Grundläggande** **inställningar** > .
1. Välj en **kategori** `Business and Pages`, till exempel . Det här värdet krävs av Facebook, men används inte för Azure AD B2C.
1. Längst ned på sidan väljer du **Lägg till plattform**och väljer sedan **Webbplats**.
1. I Url för `https://your-tenant-name.b2clogin.com/` `your-tenant-name` **webbplats**anger du att ersätta med namnet på din klient. Ange en url för **webbadressen** `http://www.contoso.com`till sekretesspolicyn , till exempel . Principadressen är en sida som du underhåller för att tillhandahålla sekretessinformation för ditt program.
1. Välj **Spara ändringar**.
1. Högst upp på sidan kopierar du värdet för **App-ID**.
1. Välj **Visa** och kopiera värdet för **App Secret**. Du använder båda för att konfigurera Facebook som en identitetsleverantör i din klientorganisation. **App Secret** är en viktig säkerhetsautentisering.
1. Välj plustecknet bredvid **PRODUKTER**och välj sedan **Konfigurera** under **Facebook-inloggning**.
1. Under **Facebook-inloggning**väljer du **Inställningar**.
1. Ange i **giltiga OAuth-omdirigerings-URI:er** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Ersätt `your-tenant-name` med namnet på din klient. Välj **Spara ändringar** längst ned på sidan.
1. Om du vill göra ditt Facebook-program tillgängligt för Azure AD B2C väljer du statusväljaren längst upp **till** höger på sidan och aktiverar det för att göra programmet offentligt och väljer sedan **Växla läge**.  Nu ska statusen ändras från **utveckling** till **live**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurera ett Facebook-konto som identitetsleverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **Facebook**.
1. Ange ett **namn**. Till exempel *Facebook*.
1. För **klient-ID**anger du app-ID:t för Facebook-programmet som du skapade tidigare.
1. För **klienthemligheten**anger du den Apphemlighet som du spelade in.
1. Välj **Spara**.
