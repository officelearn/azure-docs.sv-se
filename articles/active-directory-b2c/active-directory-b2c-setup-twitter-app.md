---
title: Konfigurera registrering och inloggning med ett Twitter-konto med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Twitter-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bf5ae39d83fd021775fbd18cf23d2e6b9078e748
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927904"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Twitter-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-twitter-application"></a>Skapa ett Twitter-program

Om du vill använda ett Twitter-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Twitter-konto, kan du hämta den på [ https://twitter.com/signup ](https://twitter.com/signup).

1. Logga in på den [Twitter appar](https://apps.twitter.com/) med dina autentiseringsuppgifter för Twitter.
2. Välj **Skapa ny App**.
3. Ange den **namn**, **beskrivning**, och **webbplats**.
4. Ange `https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp` i **Motringnings-URL: er**. Ersätt **{klient}** med klientens namn (exempel: contosob2c.onmicrosoft.com) och **{policyId}** med princip-ID (till exempel b2c_1_policy). Du bör lägga till en Motringnings-URL för alla principer som använder Twitter-konto. Se till att använda `b2clogin.com` i stället för ` login.microsoftonline.com` om du använder den i ditt program.
5. Godkänn den **Developer-avtalet** och välj **skapa ditt Twitter-program**.
7. Välj den **nycklar och åtkomsttoken** fliken.
8. Kopiera värdet för **använda nyckeln** och **Konsumenthemligheten**. Du behöver dem för att konfigurera ett Twitter-konto som identitetsprovider i din klient båda.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurera Twitter som identitetsprovider i din klient

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**. 

    ![Växla till Azure AD B2C-klientorganisationen](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

    Välj den katalog som innehåller din klient.

    ![Välj katalog](./media/active-directory-b2c-setup-twitter-app/select-directory.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Twitter*.
6. Välj **identifiera providertyp**väljer **Twitter**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange den konsument-nyckeln för den **klient-ID** och **Konsumenthemligheten** för den **klienthemlighet**.
8. Klicka på **OK**, och klicka sedan på **skapa** att spara din Twitter-konfiguration.