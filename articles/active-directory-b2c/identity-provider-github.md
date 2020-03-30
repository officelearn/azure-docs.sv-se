---
title: Konfigurera registrering och inloggning med ett GitHub-konto
titleSuffix: Azure AD B2C
description: Ge registrering och inloggning till kunder med GitHub-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a7435abf2740800184a6de1aad07bca53cd56cf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188213"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett GitHub-konto med Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Skapa ett GitHub OAuth-program

Om du vill använda ett GitHub-konto som [identitetsprovider](authorization-code-flow.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klientorganisation som representerar det. Om du inte redan har ett GitHub-konto [https://www.github.com/](https://www.github.com/)kan du registrera dig på .

1. Logga in på [GitHub-utvecklarwebbplatsen](https://github.com/settings/developers) med dina GitHub-autentiseringsuppgifter.
1. Välj **OAuth-appar** och välj sedan **Ny OAuth-app**.
1. Ange ett **programnamn** och din **url till startsidan**.
1. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` url **för motadback vid auktorisering**. Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient. Använd alla gemener när du anger ditt klientnamn även om klienten definieras med versaler i Azure AD B2C.
1. Klicka på **Registrera program**.
1. Kopiera värdena för **klient-ID** och **klienthemlighet**. Du måste både lägga till identitetsprovidern till din klient.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurera ett GitHub-konto som en identitetsleverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **GitHub (förhandsversion)**.
1. Ange ett **namn**. Till exempel *GitHub*.
1. För **klient-ID**anger du klient-ID för GitHub-programmet som du skapade tidigare.
1. För **klienthemligheten**anger du den klienthemlighet som du spelade in.
1. Välj **Spara**.
