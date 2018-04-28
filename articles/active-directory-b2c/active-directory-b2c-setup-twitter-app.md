---
title: Twitter-konfigurationen för Azure AD B2C | Microsoft Docs
description: Ange registrering och inloggning för kunder med Twitter-konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.openlocfilehash: 40e4c5549414765dabc6f37c5ffb5aea519ae673
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Ange registrering och inloggning för konsumenter med hjälp av Azure AD B2C Twitter-konton

## <a name="create-a-twitter-application"></a>Skapa ett Twitter-program
Om du vill använda Twitter som en identitetsleverantör i Azure Active Directory (AD Azure) B2C måste du skapa ett Twitter-program och ange rätt parametrar. Du behöver ett Twitter-konto för att göra detta. Om du inte har något du kan hämta den på [ https://twitter.com/signup ](https://twitter.com/signup).

1. Gå till den [Twitter appar](https://apps.twitter.com/) och logga in med dina autentiseringsuppgifter.
2. Klicka på **Skapa ny App**. 
3. I formuläret anger du ett värde för den **namn**, **beskrivning**, och **webbplats**.
4. För den **motringning URL**, ange `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Ersätt **{klient}** med din klient namn (till exempel contosob2c.onmicrosoft.com).
5. Markera kryssrutan om du accepterar den **Developer avtal** och på **skapa programmet Twitter**.
6. När appen har skapats klickar du på **nycklar och åtkomst-token**.
7. Kopiera värdet för **konsumenten nyckeln** och **konsumenthemlighet**. Du måste båda för att konfigurera Twitter som en identitetsleverantör i din klient.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurera Twitter som en identitetsleverantör i din klientorganisation
1. Logga in på den [Azure-portalen](https://portal.azure.com/) som Global administratör för Azure AD B2C-klient. 
2. Om du vill växla till din Azure AD B2C-klient, Välj Azure AD B2C-katalogen i övre högra hörnet av portalen.
3. Klicka på **alla tjänster**, och välj sedan **Azure AD B2C** från listan över tjänster under **säkerhet + identitet**.
4. Klicka på **identitetsleverantörer**.
4. Ange ett eget **namn** för providerkonfigurationen identitet. Ange till exempel ”Twitter”.
5. Klicka på **identitet providertyp**väljer **Twitter (förhandsgranskning)**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsleverantör** och ange Twitter **konsumentnyckel** för den **klient-id** och på Twitter **konsumenthemlighet** för den **klienthemlighet**.
7. Klicka på **OK**, och klicka sedan på **skapa** spara Twitter-konfigurationen.

## <a name="next-steps"></a>Nästa steg

Skapa eller redigera en [inbyggd princip](active-directory-b2c-reference-policies.md) och Lägg till Twitter som en identitetsleverantör.