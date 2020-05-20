---
title: Konfigurera registrering och inloggning med ett Microsoft-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Microsoft-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 375c83445bb559efe5c797e583129cb1b0c2fb65
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83636913"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med en Microsoft-konto med hjälp av Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Skapa ett Microsoft-konto program

Om du vill använda en Microsoft-konto som [identitets leverantör](openid-connect.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i Azure AD-klienten. Azure AD-klienten är inte samma som din Azure AD B2C klient. Om du inte redan har en Microsoft-konto kan du hämta en på [https://www.live.com/](https://www.live.com/) .

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ny registrering**.
1. Ange ett **namn** för ditt program. Till exempel *MSAapp1*.
1. Under **konto typer som stöds**väljer du **konton i valfri organisations katalog (alla Azure AD-klienter-flera klienter) och personliga Microsoft-konton (t. ex. Skype, Xbox)**.

   Mer information om de olika alternativen för konto typer finns i [snabb start: registrera ett program med Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).
1. Under **omdirigerings-URI (valfritt)** väljer du **webb** och anger `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` i text rutan. Ersätt `<tenant-name>` med ditt Azure AD B2C klient namn.
1. Välj **register**
1. Registrera **program-ID (klient)** som visas på sidan program översikt. Du behöver detta när du konfigurerar identitets leverantören i nästa avsnitt.
1. Välj **certifikat & hemligheter**
1. Klicka på **ny klient hemlighet**
1. Ange en **Beskrivning** av hemligheten, till exempel *program lösen ord 1*, och klicka sedan på **Lägg till**.
1. Registrera program lösen ordet som visas i kolumnen **värde** . Du behöver detta när du konfigurerar identitets leverantören i nästa avsnitt.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurera en Microsoft-konto som identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **Microsoft-konto**.
1. Ange ett **namn**. Till exempel *MSA*.
1. För **klient-ID**anger du program-ID: t för det Azure AD-program som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.
