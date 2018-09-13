---
title: Konfigurera registrering och inloggning med ett Twitter-konto med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Twitter-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5732293510a75a3c40df5cf3d31978c5ce599791
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720165"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Twitter-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-twitter-application"></a>Skapa ett Twitter-program

Om du vill använda ett Twitter-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Twitter-konto, kan du hämta den på [ https://twitter.com/signup ](https://twitter.com/signup).

1. Logga in på den [Twitter appar](https://apps.twitter.com/) med dina autentiseringsuppgifter för Twitter.
2. Välj **skapa en app**.
3. Ange den **appnamn**, **Programbeskrivning**, och **Webbadress**.
4. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy-name/oauth1/authresp` i **Motringnings-URL: er**. Ersätt `your-tenant-name` med namnet på din klient och `your-policy-name` med namnet på din princip. Till exempel `b2c_1_signupsignin`. Du måste använda gemener när du anger ditt klientnamn och principnamn även om de har definierats med versaler i Azure AD B2C.
5. Godkänn den **Developer-avtalet** och välj **skapa**.
7. Välj den **nycklar och åtkomsttoken** fliken.
8. Kopiera värdena för **API-nyckel** och **API hemlig nyckel**. Du behöver dem för att konfigurera ett Twitter-konto som identitetsprovider i din klient båda.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurera Twitter som identitetsprovider i din klient

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.  

    ![Växla till Azure AD B2C-klientorganisationen](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Twitter*.
6. Välj **identifiera providertyp**väljer **Twitter**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange API-nyckeln för den **klient-ID** och den hemliga nyckeln för API för den **klienthemlighet**.
8. Klicka på **OK**, och klicka sedan på **skapa** att spara din Twitter-konfiguration.