---
title: Konfigurera registrering och inloggning med ett Amazon-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Amazon-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d1a771cb13fcfa76449500ad71c67dcf7c446fa4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388450"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Amazon-konto med hjälp av Azure Active Directory B2C

## <a name="create-an-app-in-the-amazon-developer-console"></a>Skapa en app i Amazon Developer-konsolen

Om du vill använda ett Amazon-konto som en federerad identitets leverantör i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i dina [Amazon Developer-tjänster och-tekniker](https://developer.amazon.com). Om du inte redan har ett Amazon-konto kan du registrera dig på [https://www.amazon.com/](https://www.amazon.com/) .

> [!NOTE]  
> Använd följande URL: er i **steg 8** nedan och Ersätt `your-tenant-name` med namnet på din klient. När du anger ditt klient namn använder du enbart gemener, även om klienten har definierats med versaler i Azure AD B2C.
> - För **tillåtna ursprung**anger du`https://your-tenant-name.b2clogin.com` 
> - För **tillåtna retur-URL: er**, ange`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurera ett Amazon-konto som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **Amazon**.
1. Ange ett **namn**. Till exempel *Amazon*.
1. För **klient-ID**anger du klient-ID för det Amazon-program som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.
