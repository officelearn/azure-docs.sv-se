---
title: 'Azure Active Directory B2C: Twitter-konfiguration | Microsoft Docs'
description: "Ange registrering och inloggning för konsumenter med Twitter-konton i dina program som skyddas av Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 579a6841-9329-45b8-a351-da4315a6634e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/06/2017
ms.author: parakhj
ms.openlocfilehash: 4dbc70d866a728a63102bd169a124bd26990aeb2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>Azure Active Directory B2C: Ange registrering och inloggning för konsumenter med Twitter-konton

## <a name="create-a-twitter-application"></a>Skapa ett Twitter-program
Om du vill använda Twitter som en identitetsleverantör i Azure Active Directory (AD Azure) B2C måste du skapa ett Twitter-program och ange rätt parametrar. Du behöver ett Twitter-konto för utvecklare att göra detta. Om du inte har något du kan hämta den på [https://dev.twitter.com/](https://dev.twitter.com/).

1. Gå till den [Twitter-utvecklare webbplats](https://dev.twitter.com/) och logga in med dina autentiseringsuppgifter.
2. Klicka på **Mina appar** under **verktyg och Support** och klicka sedan på **Skapa ny App**. 
3. I formuläret anger du ett värde för den **namn**, **beskrivning**, och **webbplats**.
4. För den **motringning URL**, ange `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Ersätt **{klient}** med din klient namn (till exempel contosob2c.onmicrosoft.com).
5. Markera kryssrutan om du accepterar den **Developer avtal** och på **skapa programmet Twitter**.
6. När appen har skapats klickar du på **nycklar och åtkomst-token**.
7. Kopiera värdet för **konsumenten nyckeln** och **konsumenthemlighet**. Du måste båda för att konfigurera Twitter som en identitetsleverantör i din klient.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurera Twitter som en identitetsleverantör i din klientorganisation
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure-portalen.
2. Klicka på B2C-funktionsbladet **identitetsleverantörer**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för providerkonfigurationen identitet. Ange till exempel ”Twitter”.
5. Klicka på **identitet providertyp**väljer **Twitter**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsleverantör** och ange Twitter **konsumentnyckel** för den **klient-id** och på Twitter **konsumenthemlighet** för den **klienthemlighet**.
7. Klicka på **OK**, och klicka sedan på **skapa** spara Twitter-konfigurationen.

## <a name="next-steps"></a>Nästa steg

Skapa eller redigera en [inbyggd princip](active-directory-b2c-reference-policies.md) och Lägg till Twitter som en identitetsleverantör.