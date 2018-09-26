---
title: Konfigurera registrering och inloggning med ett GitHub-konto med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med GitHub-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7f8b2c6dc570f7a610c0d661da0c6df7491647bd
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182186"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett GitHub-konto med hjälp av Azure Active Directory B2C

> [!NOTE]
> Den här funktionen är en förhandsversion.
> 

Om du vill använda ett Github-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Github-konto, kan du hämta den på [ https://www.github.com/ ](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Skapa ett GitHub OAuth-program

1. Logga in på den [GitHub Developer](https://github.com/settings/developers) webbplats med dina autentiseringsuppgifter för GitHub.
2. Välj **OAuth appar** och välj sedan **ny OAuth-App**.
3. Ange en **programnamn** och din **Hemsides-URL**.
4. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **Motringnings-URL för auktorisering**. Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient.
5. Klicka på **registrera program**.
6. Kopiera värdena för **klient-ID** och **Klienthemlighet**. Du måste båda för att lägga till identitetsleverantören i din klient.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurera ett GitHub-konto som identitetsprovider

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Github*.
6. Välj **identifiera providertyp**väljer **Github (förhandsversion)**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange klient-Id som du antecknade tidigare som den **klient-ID** och ange Klienthemligheten som du registrerade som den **klienthemlighet**av programmet för Github-konto som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din konfiguration för Github-konto.