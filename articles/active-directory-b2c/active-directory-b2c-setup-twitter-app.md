---
title: Twitter-konfiguration för Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Twitter-konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 6/13/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dad35f26496306558a6e0105db86321c497a8306
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342971"
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Ange registrera dig och logga in till kunder med Twitter-konton med hjälp av Azure AD B2C

## <a name="create-a-twitter-application"></a>Skapa ett Twitter-program

För att använda Twitter som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa en Twitter-program och ange rätt parametrar. Du behöver ett Twitter-konto för att göra detta. Om du inte har någon kan du hämta den på [ https://twitter.com/signup ](https://twitter.com/signup).

1. Gå till den [Twitter appar](https://apps.twitter.com/) och logga in med dina autentiseringsuppgifter.
2. Klicka på **Skapa ny App**.
3. I formuläret anger du ett värde för den **namn**, **beskrivning**, och **webbplats**.
4. För den **Motringnings-URL**, ange `https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp`. Ersätt **{klient}** med klientens namn (exempel: contosob2c.onmicrosoft.com) och {policyId} med princip-id (till exempel b2c_1_policy).  Den här återanrop URL: en måste finnas i alla gemener. Du bör lägga till en Motringnings-URL för alla principer som använder Twitter-inloggning. Se till att använda `b2clogin.com` i stället för ` login.microsoftonline.com` om du använder den i ditt program.
5. Markera kryssrutan för att godkänna den **Developer-avtalet** och klicka på **skapa ditt Twitter-program**.
6. När appen har skapats, markerar du den i listan, väljer den **inställningar** fliken och klicka sedan på **uppdatera inställningarna för**.
7. Välj den **nycklar och åtkomsttoken** fliken.
8. Kopiera värdet för **använda nyckeln** och **Konsumenthemligheten**. Du måste båda dem för att konfigurera Twitter som identitetsprovider i din klient.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurera Twitter som identitetsprovider i din klient

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som Global administratör för Azure AD B2C-klient. 
2. Om du vill växla till din Azure AD B2C-klient, väljer du den Azure AD B2C-katalogen i det övre högra hörnet i portalen.
3. Klicka på **alla tjänster**, och välj sedan **Azure AD B2C** från listan över tjänster under **säkerhet + identitet**.
4. Klicka på **identitetsprovidrar**.
5. Ange ett eget **namn** för konfigurationen av identity-providern. Ange till exempel ”Twitter”.
6. Klicka på **identifiera providertyp**väljer **Twitter (förhandsversion)**, och klicka på **OK**.
7. Klicka på **ställa in den här identitetsprovidern** och ange Twitter **använda nyckeln** för den **klient-id** och Twitter **Konsumenthemligheten** för den **klienthemlighet**.
8. Klicka på **OK**, och klicka sedan på **skapa** att spara din Twitter-konfiguration.

## <a name="next-steps"></a>Nästa steg

Skapa eller redigera en [inbyggd princip](active-directory-b2c-reference-policies.md) och lägga till Twitter som identitetsprovider.