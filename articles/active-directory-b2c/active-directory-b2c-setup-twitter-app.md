---
title: Konfigurera registrering och inloggning med ett Twitter-konto med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Twitter-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f0f0b8e0cbb5fbab81a07a28a9d4a2c264be6545
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52719869"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Twitter-konto med hjälp av Azure Active Directory B2C

## <a name="create-an-application"></a>Skapa ett program

Om du vill använda Twitter som identitetsprovider i Azure AD B2C måste du skapa ett Twitter-program. Om du inte redan har ett Twitter-konto, kan du hämta den på [ https://twitter.com/signup ](https://twitter.com/signup).

1. Logga in på den [Twitter utvecklare](https://developer.twitter.com/en/apps) webbplats med dina autentiseringsuppgifter för Twitter-konto.
2. Välj **skapa en app**.
3. Ange en **appnamn** och en **Programbeskrivning**.
4. I **Webbadress**, ange `https://your-tenant.b2clogin.com`. Ersätt `your-tenant` med namnet på din klient. Till exempel https://contosob2c.b2clogin.com.
5. För den **Motringnings-URL**, ange `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Ersätt `your-tenant` med namnet på ditt klientnamn och `your-user-flow-Id` med identifieraren för ditt användarflöde. Till exempel `b2c_1A_signup_signin_twitter`. Du måste använda gemener när du anger ditt klientnamn även om klienten har definierats med versaler i Azure AD B2C.
6. Längst ned på sidan Läs och Godkänn villkoren och välj sedan **skapa**.
7. På den **appinformation** väljer **Redigera > Redigera information**, markera kryssrutan för **aktivera logga in med Twitter**, och välj sedan **spara**.
8. Välj **nycklar och token** och registrera den **konsument-API-nyckel** och **hemlig nyckel för konsument-API** värden som ska användas senare.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurera Twitter som identitetsprovider i din klient

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Twitter*.
6. Välj **identifiera providertyp**väljer **Twitter**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange API-nyckeln för den **klient-ID** och den hemliga nyckeln för API för den **klienthemlighet**.
8. Klicka på **OK**, och klicka sedan på **skapa** att spara din Twitter-konfiguration.