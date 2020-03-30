---
title: Konfigurera registrering och inloggning med ett LinkedIn-konto
titleSuffix: Azure AD B2C
description: Ge registrering och inloggning till kunder med LinkedIn-konton i dina program med Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188108"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett LinkedIn-konto med Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Skapa ett LinkedIn-program

Om du vill använda ett LinkedIn-konto som [identitetsprovider](authorization-code-flow.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klientorganisation som representerar det. Om du inte redan har ett LinkedIn-konto [https://www.linkedin.com/](https://www.linkedin.com/)kan du registrera dig på .

1. Logga in på [LinkedIn Developers webbplats](https://www.developer.linkedin.com/) med dina LinkedIn-kontouppgifter.
1. Välj **Mina appar**och klicka sedan på Skapa **program**.
1. Ange **företagsnamn,** **programnamn,** **programbeskrivning,** **programlogotyp,** **programanvändning,** **webbadress,** **företags-e-post**och **företagstelefon**.
1. Godkänn användarvillkoren för **LinkedIn API** och klicka på **Skicka**.
1. Kopiera värdena för **klient-ID** och **klienthemlighet**. Du hittar dem under **Autentiseringsnycklar**. Du behöver båda för att konfigurera LinkedIn som en identitetsleverantör i din klientorganisation. **Klienthemlighet** är en viktig säkerhetsautentiseringsinformation.
1. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **auktoriserade omdirigeringsadresser**. Ersätt `your-tenant-name` med namnet på din klient. Du måste använda alla gemener när du anger ditt klientnamn även om klienten definieras med versaler i Azure AD B2C. Välj **Lägg till**och klicka sedan på **Uppdatera**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Konfigurera ett LinkedIn-konto som en identitetsleverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **LinkedIn**.
1. Ange ett **namn**. *LinkedIn*.
1. För **klient-ID**anger du klient-ID för LinkedIn-programmet som du skapade tidigare.
1. För **klienthemligheten**anger du den klienthemlighet som du spelade in.
1. Välj **Spara**.

## <a name="migration-from-v10-to-v20"></a>Migrering från v1.0 till v2.0

LinkedIn uppdaterade nyligen [sina API:er från v1.0 till v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Som en del av migreringen kan Azure AD B2C bara få det fullständiga namnet på LinkedIn-användaren under registreringen. Om en e-postadress är ett av de attribut som samlas in under registreringen måste användaren manuellt ange e-postadressen och validera den.
