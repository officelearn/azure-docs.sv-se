---
title: Konfigurera registrering och inloggning med ett Microsoft-konto med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Microsoft-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 16e4dbac4c8146b048d4d9b76544677a6111e2a5
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900837"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Microsoft-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Skapa ett program för Microsoft-konto

Om du vill använda ett Microsoft-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Microsoft-konto, kan du hämta den på [ https://www.live.com/ ](https://www.live.com/).

1. Logga in på den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) med autentiseringsuppgifterna för ditt Microsoft-konto.
2. I det övre högra hörnet väljer **lägga till en app**.
3. Ange en **namn** för dina program och klicka på **skapa**.
4. På registreringssidan, kopierar du värdet för **program-Id**. Du kan använda den för att konfigurera ditt Microsoft-konto som identitetsprovider i din klient.
5. Välj **Lägg till plattform**, och sedan och välj **Web**.
6. Ange `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i **omdirigera URL: er**. Ersätt **{klient}** med klientens namn (exempel: contosob2c.onmicrosoft.com).
7. Välj **generera ett nytt lösenord** under **Programhemligheter**. Kopiera det nya lösenordet som visas på skärmen. Du behöver den för att konfigurera ett Microsoft-konto som en identitetsprovider i din klient. Det här lösenordet är en viktig säkerhetsuppgift för autentisering.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurera ett Microsoft-konto som identitetsprovider

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**. 

    ![Växla till Azure AD B2C-klientorganisationen](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

    Välj den katalog som innehåller din klient.

    ![Välj katalog](./media/active-directory-b2c-setup-msa-app/select-directory.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *MSA*.
6. Välj **identifiera providertyp**väljer **Account**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange det program-Id som du antecknade tidigare som den **klient-ID** och ange lösenordet som du registrerade som den **klienthemlighet**av programmet för Microsoft-konto som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din konfiguration för Microsoft-konto.

