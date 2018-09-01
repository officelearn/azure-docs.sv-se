---
title: Konfigurera registrering och inloggning med en Amazon-konto med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Amazon konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c64b32656db2d3b821833450b4e866b9e33e44cd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337353"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med en Amazon-konto med hjälp av Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Skapa ett Amazon-program

Om du vill använda ett Amazon-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har en Amazon-konto kan du hämta den på [ http://www.amazon.com/ ](http://www.amazon.com/).

1. Logga in på den [Amazon Developer Center](https://login.amazon.com/) med autentiseringsuppgifterna för ditt Amazon.
2. Om du inte redan har gjort det, klickar du på **registrera dig**, av utvecklare registrering anvisningarna och acceptera principen.
3. Välj **registrera nya program**.
4. Ange en **namn**, **beskrivning**, och **meddelande Sekretesswebbadress**, och klicka sedan på **spara**.
5. I den **webbinställningar** och kopiera värdena för **klient-ID**. Välj **visa hemligheten** att hämta klienthemligheten och kopiera den. Du måste båda du konfigurerar ett Amazon-konto som identitetsprovider i din klient. **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.
6. I den **webbinställningar** väljer **redigera**, och ange sedan `https://{tenant}.b2clogin.com` i **tillåtna ursprung för JavaScript** och `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` i **tillåten Returnerar URL: er**. Ersätt **{klient}** med klientens namn (till exempel contosob2c). 
7. Klicka på **Spara**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurera ett Amazon-konto som identitetsprovider

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**. 

    ![Växla till Azure AD B2C-klientorganisationen](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Välj den katalog som innehåller din klient.

    ![Välj katalog](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Amazon*.
6. Välj **identifiera providertyp**väljer **Amazon**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange klient-ID som du antecknade tidigare som den **klient-ID** och ange Klienthemligheten som du registrerade som den **klienthemlighet**till Amazon-programmet som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din konfiguration för Amazon.

