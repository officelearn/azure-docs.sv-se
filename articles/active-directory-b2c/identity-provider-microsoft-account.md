---
title: Konfigurera registrering och inloggning med ett Microsoft-konto
titleSuffix: Azure AD B2C
description: Ge registrering och inloggning till kunder med Microsoft-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25784eb161a860398b0741d1d20375cabd1c4eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188026"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Microsoft-konto med Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Skapa ett Microsoft-kontoprogram

Om du vill använda ett Microsoft-konto som [identitetsprovider](openid-connect.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i Azure AD-klienten. Azure AD-klienten är inte samma sak som din Azure AD B2C-klient. Om du inte redan har ett Microsoft-konto [https://www.live.com/](https://www.live.com/)kan du skaffa ett på .

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrollera att du använder katalogen som innehåller din Azure AD-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din Azure AD-klientorganisation.
1. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen och sök sedan efter och välj **Appregistreringar**.
1. Välj **Ny registrering**.
1. Ange ett **namn** för ditt program. Till exempel *MSAapp1*.
1. Under **Kontotyper som stöds**väljer du Konton i alla **organisationskataloger och personliga Microsoft-konton (t.ex Outlook.com.** Det här alternativet är inriktat på den bredaste uppsättningen Microsoft-identiteter.

   Mer information om de olika valen av kontotyp finns i [Snabbstart: Registrera ett program med Microsofts identitetsplattform](../active-directory/develop/quickstart-register-app.md).
1. Under **Omdirigera URI (valfritt)** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` väljer du **Webben** och anger i textrutan. Ersätt `your-tenant-name` med ditt Azure AD B2C-klientnamn.
1. Välj **registrera**
1. Registrera **programmets (klient)-ID** som visas på sidan Översikt för programmet. Du behöver detta när du konfigurerar identitetsprovidern i nästa avsnitt.
1. Välj **certifikat & hemligheter**
1. Klicka på **Ny klienthemlighet**
1. Ange en **beskrivning** för hemligheten, till exempel *Programlösenord 1*, och klicka sedan på **Lägg till**.
1. Registrera programlösenordet som visas i kolumnen **Värde.** Du behöver detta när du konfigurerar identitetsprovidern i nästa avsnitt.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurera ett Microsoft-konto som en identitetsleverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **Microsoft-konto**.
1. Ange ett **namn**. Till exempel *MSA*.
1. För **klient-ID**anger du program-ID:t för Azure AD-programmet som du skapade tidigare.
1. För **klienthemligheten**anger du klienthemligheten som du spelade in.
1. Välj **Spara**.
