---
title: Konfigurera registrering och inloggning med ett Microsoft-konto – Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Microsoft-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 13e199a56a3cdd4f8e5a21f162fe0397c6f397cd
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428263"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Microsoft-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Skapa ett program för Microsoft-konto

Att använda ett microsoftkonto som ett [identitetsprovider](active-directory-b2c-reference-oidc.md) i Azure Active Directory (Azure AD) B2C måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Microsoft-konto, kan du hämta den på [ https://www.live.com/ ](https://www.live.com/).

1. Logga in på den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) med autentiseringsuppgifterna för ditt Microsoft-konto.
2. I det övre högra hörnet väljer **lägga till en app**.
3. Ange en **namn** för ditt program. Till exempel *MSAapp1*.
4. Välj **generera nytt lösenord** och se till att du kopierar lösenordet som ska användas när du konfigurerar identitetsprovidern. Också kopiera den **program-Id**. 
5. Välj **Lägg till plattform**, och sedan och välj **Web**.
4. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **omdirigera URL: er**. Ersätt `your-tenant-name` med namnet på din klient.
5. Välj **Spara**.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurera ett Microsoft-konto som identitetsprovider

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *MSA*.
6. Välj **identifiera providertyp**väljer **Account**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange det program-Id som du antecknade tidigare som den **klient-ID** och ange lösenordet som du registrerade som den **klienthemlighet**av programmet för Microsoft-konto som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din konfiguration för Microsoft-konto.

