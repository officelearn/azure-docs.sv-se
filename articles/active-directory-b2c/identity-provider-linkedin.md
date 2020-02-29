---
title: Konfigurera registrering och inloggning med ett LinkedIn-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med LinkedIn-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d32eb80ffe296d86164a6d27a1b7a28181357243
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188108"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett LinkedIn-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Skapa ett LinkedIn-program

Om du vill använda ett LinkedIn-konto som [identitets leverantör](authorization-code-flow.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klient som representerar det. Om du inte redan har ett LinkedIn-konto kan du registrera dig på [https://www.linkedin.com/](https://www.linkedin.com/).

1. Logga in på [webbplatsen LinkedIn-utvecklare](https://www.developer.linkedin.com/) med dina LinkedIn-kontoautentiseringsuppgifter.
1. Välj **Mina appar**och klicka sedan på **skapa program**.
1. Ange **företags namn**, **program namn**, **programbeskrivning**, **program logo typ**, **program användning**, **webbplats-URL**, **företags-e-post**och **företags telefon**.
1. Godkänn LinkedIn- **API-villkoren för användning** och klicka på **Skicka**.
1. Kopiera värdena för **klient-ID** och **klient hemlighet**. Du hittar dem under **nycklar för autentisering**. Du behöver båda dessa för att konfigurera LinkedIn som en identitets leverantör i din klient. **Klient hemlighet** är en viktig säkerhets autentiseringsuppgift.
1. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **behöriga omdirigerings-URL: er**. Ersätt `your-tenant-name` med namnet på din klient. Du måste använda små bokstäver när du anger ditt klient namn även om klienten har definierats med versaler i Azure AD B2C. Välj **Lägg till**och klicka sedan på **Uppdatera**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Konfigurera ett LinkedIn-konto som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj **LinkedIn**.
1. Ange ett **namn**. Till exempel *LinkedIn*.
1. För **klient-ID**anger du klient-ID för LinkedIn-programmet som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.

## <a name="migration-from-v10-to-v20"></a>Migrering från v 1.0 till v 2.0

LinkedIn har nyligen [uppdaterat sina API: er från v 1.0 till v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Som en del av migreringen kan Azure AD B2C bara hämta det fullständiga namnet på LinkedIn-användaren under registreringen. Om en e-postadress är en av de attribut som samlas in under registreringen måste användaren manuellt ange e-postadressen och verifiera den.
