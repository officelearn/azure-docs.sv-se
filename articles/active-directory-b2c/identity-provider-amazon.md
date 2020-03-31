---
title: Konfigurera registrering och inloggning med ett Amazon-konto
titleSuffix: Azure AD B2C
description: Ge registrering och inloggning till kunder med Amazon-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4538c1d15aeae624f5d73e9985448bda2fd8f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188468"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Amazon-konto med Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Skapa ett Amazon-program

Om du vill använda ett Amazon-konto som [identitetsprovider](authorization-code-flow.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klientorganisation som representerar det. Om du inte redan har ett Amazon-konto [https://www.amazon.com/](https://www.amazon.com/)kan du registrera dig på .

1. Logga in på [Amazon Developer Center](https://login.amazon.com/) med dina Amazon-kontouppgifter.
1. Om du inte redan har gjort det klickar du på **Registrera dig,** följer stegen för registrering av utvecklare och accepterar principen.
1. Välj **Registrera ett nytt program**.
1. Ange **url:en namn,** **beskrivning**och **sekretessmeddelande**och klicka sedan på **Spara**. Sekretessmeddelandet är en sida som du hanterar och som ger sekretessinformation till användarna.
1. Kopiera värdena för **klient-ID**i avsnittet **Webbinställningar** . Välj **Visa hemlighet** om du vill få klienten hemlig och kopiera den sedan. Du behöver båda för att konfigurera ett Amazon-konto som en identitetsleverantör i din klientorganisation. **Klienthemlighet** är en viktig säkerhetsautentiseringsinformation.
1. I avsnittet **Webbinställningar** **Edit**väljer du Redigera `https://your-tenant-name.b2clogin.com` och anger sedan `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **Tillåtna JavaScript-ursprung** och i **Tillåtna returadresser**. Ersätt `your-tenant-name` med namnet på din klient. Du måste använda alla gemener när du anger ditt klientnamn även om klienten definieras med versaler i Azure AD B2C.
1. Klicka på **Spara**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurera ett Amazon-konto som en identitetsleverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Identitetsleverantörer**och välj sedan **Amazon**.
1. Ange ett **namn**. Till *exempel, Amazon*.
1. För **klient-ID**anger du klient-ID för Amazon-programmet som du skapade tidigare.
1. För **klienthemligheten**anger du den klienthemlighet som du spelade in.
1. Välj **Spara**.
