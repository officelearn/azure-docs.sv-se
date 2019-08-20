---
title: Konfigurera registrering och inloggning med ett Amazon-konto – Azure Active Directory B2C
description: Tillhandahålla registrering och inloggning till kunder med Amazon-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 89dd592e6e5ea1ce71277035654068ce2f782890
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622209"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Amazon-konto med hjälp av Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Skapa ett Amazon-program

Om du vill använda ett Amazon-konto som [identitets leverantör](active-directory-b2c-reference-oauth-code.md) i Azure Active Directory-B2C (Azure AD) måste du skapa ett program i din klient organisation som representerar det. Om du inte redan har ett Amazon-konto kan du registrera dig [https://www.amazon.com/](https://www.amazon.com/)på.

1. Logga in på [Amazon Developer Center](https://login.amazon.com/) med dina Amazon-kontoautentiseringsuppgifter.
1. Om du inte redan har gjort det klickar du på **Registrera**, följer utvecklings registrerings stegen och godkänner principen.
1. Välj **Registrera nytt program**.
1. Ange ett **namn**, en **Beskrivning**och **en URL för sekretess meddelande**och klicka sedan på **Spara**. Sekretess meddelandet är en sida som du hanterar som ger sekretess information till användarna.
1. I avsnittet **webb inställningar** kopierar du värdena för **klient-ID**. Välj **Visa hemlighet** för att hämta klient hemligheten och kopiera den sedan. Du behöver båda dessa för att konfigurera ett Amazon-konto som en identitets leverantör i din klient organisation. **Klient hemlighet** är en viktig säkerhets autentiseringsuppgift.
1. I avsnittet **webb inställningar** väljer du **Redigera**och `https://your-tenant-name.b2clogin.com` anger sedan i **tillåtna JavaScript-ursprung** och `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **tillåtna retur-URL: er**. Ersätt `your-tenant-name` med namnet på din klient. Du måste använda små bokstäver när du anger ditt klient namn även om klienten har definierats med versaler i Azure AD B2C.
1. Klicka på **Spara**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurera ett Amazon-konto som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **Amazon**.
1. Ange ett **namn**. Till exempel *Amazon*.
1. För **klient-ID**anger du klient-ID för det Amazon-program som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.
