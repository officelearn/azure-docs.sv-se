---
title: Konfigurera registrering och inloggning med ett Facebook-konto med Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Facebook-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 68016f9bc2c6be9bdc0ac06b454563b6ae135d38
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714861"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Facebook-konto med Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Skapa ett Facebook-program

Om du vill använda en Facebook-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Facebook-konto, kan du hämta den på [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Logga in på [Facebook för utvecklare](https://developers.facebook.com/) med dina autentiseringsuppgifter för Facebook-konto.
2. Om du inte redan har gjort det så, behöver du registrera som Facebook-utvecklare. Om du vill göra detta, Välj **registrera** acceptera Facebooks principer i det övre högra hörnet på sidan och gör registreringen.
3. Välj **Mina appar** och klicka sedan på **lägga till en ny App**. 
4. Ange en **visningsnamn** och en giltig **kontakta e-post**.
5. Klicka på **skapa App-ID**. Du kan behöva du acceptera Facebook-plattformen principer och slutföra en online säkerhetskontrollen.
6. Välj **inställningar** > **grundläggande**.
7. Välj en **kategori**, till exempel `Business and Pages`. Det här värdet krävs av Facebook, men inte använts för Azure AD B2C.
8. Längst ned på sidan Välj **Lägg till plattform**, och välj sedan **webbplats**.
9. I **webbplatsens URL**, ange `https://your-tenant-name.b2clogin.com/` ersätter `your-tenant-name` med namnet på din klient. Ange en URL för den **princip Sekretesswebbadress**, till exempel `http://www.contoso.com`. Princip-URL är en sida som ska underhållas för att tillhandahålla Sekretessinformation för ditt program.
10. Välj **spara ändringar**.
11. Kopiera värdet för längst ned på sidan **App-ID**. 
12. Klicka på **visa** och kopiera värdet för **Apphemlighet**. Du kan använda båda för att konfigurera Facebook som en identitetsprovider i din klient. **Apphemlighet** är en viktig säkerhetsuppgift för autentisering.
13. Välj **produkter**, och välj sedan **konfigurera** under **Facebook-inloggning**.
14. Välj **inställningar** under **Facebook-inloggning**.
15. I **giltig OAuth omdirigerings-URI: er**, ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Ersätt `your-tenant-name` med namnet på din klient. Klicka på **spara ändringar** längst ned på sidan.
16. För att ditt Facebook-program tillgängliga för Azure AD B2C, Välj **App granska**anger **gör My Application offentliga?** till **Ja**, och klicka sedan på **bekräfta** .

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurera ett Facebook-konto som identitetsprovider

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient. 

    ![Växla till Azure AD B2C-klientorganisationen](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Facebook*.
6. Välj **identifiera providertyp**väljer **Facebook**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange det ID som du antecknade tidigare som den **klient-ID** och ange App-hemlighet som du registrerade som den **klienthemlighet** av Facebook-programmet som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din Facebook-konfiguration.