---
title: Konfigurera registrering och inloggning med ett Twitter-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Twitter-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8b316fdb185c8a9c78e1a0c9be5f7d57b809141c
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950450"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Twitter-konto med hjälp av Azure Active Directory B2C

## <a name="create-an-application"></a>Skapa ett program

Om du vill använda Twitter som identitets leverantör i Azure AD B2C måste du skapa ett Twitter-program. Om du inte redan har ett Twitter-konto kan du registrera dig på [https://twitter.com/signup](https://twitter.com/signup).

1. Logga in på webbplatsen för [Twitter-utvecklare](https://developer.twitter.com/en/apps) med dina Twitter-kontoautentiseringsuppgifter.
1. Välj **skapa en app**.
1. Ange ett **namn på appen** och en **program beskrivning**.
1. I **webbplats-URL**anger du `https://your-tenant.b2clogin.com`. Ersätt `your-tenant` med namnet på din klient. Till exempel https://contosob2c.b2clogin.com.
1. Ange `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`för **återanrops-URL: en**. Ersätt `your-tenant` med namnet på ditt klient namn och `your-user-flow-Id` med identifieraren för ditt användar flöde. Till exempel `b2c_1A_signup_signin_twitter`. Du måste använda små bokstäver när du anger ditt klient namn och ditt användar flödes-ID även om de definieras med versaler i Azure AD B2C.
1. Längst ned på sidan, Läs och godkänn villkoren och välj sedan **skapa**.
1. På sidan **information om appar** väljer du **Redigera > Redigera information**, markerar kryss rutan **Aktivera inloggning med Twitter**och väljer sedan **Spara**.
1. Välj **nycklar och tokens** och registrera konsument- **API-nyckeln** och de **hemliga nyckel** värden för konsument-API som ska användas senare.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurera Twitter som en identitets leverantör i din klient organisation

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **Twitter**.
1. Ange ett **namn**. Till exempel *Twitter*.
1. För **klient-ID anger du klient-** API-nyckeln för det Twitter-program som du skapade tidigare.
1. För **klient hemligheten**anger du den hemliga nyckel för konsument-API som du har spelat in.
1. Välj **Spara**.
