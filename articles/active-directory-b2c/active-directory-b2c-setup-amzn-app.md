---
title: Konfigurera registrering och inloggning med en Amazon-konto med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Amazon konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 207a33931d7be88355241ea8c282dc7c39c9971c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55169968"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med en Amazon-konto med hjälp av Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Skapa ett Amazon-program

Om du vill använda ett Amazon-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har en Amazon-konto kan du hämta den på [ https://www.amazon.com/ ](https://www.amazon.com/).

1. Logga in på den [Amazon Developer Center](https://login.amazon.com/) med autentiseringsuppgifterna för ditt Amazon.
2. Om du inte redan har gjort det, klickar du på **registrera dig**, av utvecklare registrering anvisningarna och acceptera principen.
3. Välj **registrera nya program**.
4. Ange en **namn**, **beskrivning**, och **meddelande Sekretesswebbadress**, och klicka sedan på **spara**. Sekretessmeddelandet är en sida som du hanterar och som innehåller Sekretessinformation för användare.
5. I den **webbinställningar** och kopiera värdena för **klient-ID**. Välj **visa hemligheten** att hämta klienthemligheten och kopiera den. Du måste båda du konfigurerar ett Amazon-konto som identitetsprovider i din klient. **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.
6. I den **webbinställningar** väljer **redigera**, och ange sedan `https://your-tenant-name.b2clogin.com` i **tillåtna ursprung för JavaScript** och `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **tillåten Returnerar URL: er**. Ersätt `your-tenant-name` med namnet på din klient. Du måste använda gemener när du anger ditt klientnamn även om klienten har definierats med versaler i Azure AD B2C.
7. Klicka på **Spara**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurera ett Amazon-konto som identitetsprovider

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Amazon*.
6. Välj **identifiera providertyp**väljer **Amazon**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange klient-ID som du antecknade tidigare som den **klient-ID** och ange Klienthemligheten som du registrerade som den **klienthemlighet**till Amazon-programmet som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din konfiguration för Amazon.

