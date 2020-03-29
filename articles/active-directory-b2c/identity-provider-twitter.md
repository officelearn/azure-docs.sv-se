---
title: Konfigurera registrering och inloggning med ett Twitter-konto
titleSuffix: Azure AD B2C
description: Ge registrering och inloggning till kunder med Twitter-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62a283efb93987d3c4a6564c9b25d2031c269559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051467"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Twitter-konto med Azure Active Directory B2C

## <a name="create-an-application"></a>Skapa ett program

Om du vill använda Twitter som identitetsleverantör i Azure AD B2C måste du skapa ett Twitter-program. Om du inte redan har ett Twitter-konto [https://twitter.com/signup](https://twitter.com/signup)kan du registrera dig på .

1. Logga in på [Twitter Developers](https://developer.twitter.com/en/apps) webbplats med dina Twitter-kontouppgifter.
1. Välj **Skapa en app**.
1. Ange ett **appnamn** och en **programbeskrivning**.
1. I **Webbadress**anger `https://your-tenant.b2clogin.com`du . Ersätt `your-tenant` med namnet på din klient. Till exempel `https://contosob2c.b2clogin.com`.
1. För **motringningsadressen**anger du `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Ersätt `your-tenant` med namnet på ditt `your-user-flow-Id` klientnamn och med identifieraren för ditt användarflöde. Till exempel `b2c_1A_signup_signin_twitter`. Du måste använda alla gemener när du anger ditt klientnamn och användarflödes-ID även om de definieras med versaler i Azure AD B2C.
1. Längst ned på sidan läser och accepterar du villkoren och väljer sedan **Skapa**.
1. På sidan **Appinformation** väljer du **Redigera > Redigera information,** markerar kryssrutan Aktivera **inloggning med Twitter**och väljer sedan **Spara**.
1. Välj **Nycklar och token** och registrera **konsument-API-nyckeln** och **de hemliga nyckelnvärden för konsument-API** som ska användas senare.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurera Twitter som en identitetsleverantör i din klientorganisation

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **Twitter**.
1. Ange ett **namn**. Till exempel *Twitter*.
1. För **klient-ID**anger du konsument-API-nyckeln för Twitter-programmet som du skapade tidigare.
1. För **klienthemligheten**anger du den hemlig nyckel för konsument-API som du har spelat in.
1. Välj **Spara**.
