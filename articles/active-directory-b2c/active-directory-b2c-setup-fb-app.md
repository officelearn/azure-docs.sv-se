---
title: Konfigurera registrering och inloggning med ett Facebook-konto med Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med Facebook-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 985285b463d66770f97a431705d5b9198b632592
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344614"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Facebook-konto med Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Skapa ett Facebook-program

Om du vill använda en Facebook-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett Facebook-konto, kan du hämta den på [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Logga in på [Facebook för utvecklare](https://developers.facebook.com/) med dina autentiseringsuppgifter för Facebook-konto.
2. Om du inte redan har gjort det så, behöver du registrera som Facebook-utvecklare. Om du vill göra detta, Välj **registrera** acceptera Facebooks principer i det övre högra hörnet på sidan och gör registreringen.
3. Välj **Mina appar** och klicka sedan på **Lägg till ny App**. 
4. Ange en **visningsnamn** och en giltig **kontakta e-post**.
5. Klicka på **skapa App-ID**. Du kan behöva du acceptera Facebook-plattformen principer och slutföra en online säkerhetskontrollen.
6. Välj **inställningar** > **grundläggande**.
7. Längst ned på sidan Välj **Lägg till plattform**, och välj sedan **webbplats**.
8. Ange `https://{tenantname}.b2clogin.com/` i **webbadress**. Ange en URL för den **princip Sekretesswebbadress**, till exempel `http://www.contoso.com`.
9. Välj **spara ändringar**.
11. Kopiera värdet för längst ned på sidan **App-ID**. 
12. Klicka på **visa** och kopiera värdet för **Apphemlighet**. Du kan använda båda för att konfigurera Facebook som en identitetsprovider i din klient. **Apphemlighet** är en viktig säkerhetsuppgift för autentisering.
13. Välj **produkter**, och välj sedan **konfigurera** under **Facebook-inloggning**.
14. Välj **inställningar** under **Facebook-inloggning**.
15. Ange `https://{tenantname}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` i **giltig OAuth omdirigerings-URI: er** . Ersätt **{klient}** med klientens namn (till exempel contosob2c). Klicka på **spara ändringar** längst ned på sidan.
16. För att ditt Facebook-program tillgängliga för Azure AD B2C, Välj **App granska**anger **gör My Application offentliga?** till **Ja**, välja en kategori, till exempel `Business and Pages`och klicka sedan på **Bekräfta**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurera ett Facebook-konto som identitetsprovider

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör för din Azure AD B2C-klient.
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj din prenumerationsinformation och välj därefter **Växla katalog**. 

    ![Växla till Azure AD B2C-klientorganisationen](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Välj den katalog som innehåller din klient.

    ![Välj katalog](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *Facebook*.
6. Välj **identifiera providertyp**väljer **Facebook**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange det ID som du antecknade tidigare som den **klient-ID** och ange App-hemlighet som du registrerade som den **klienthemlighet** av Facebook-programmet som du skapade tidigare).
8. Klicka på **OK** och klicka sedan på **skapa** att spara din Facebook-konfiguration.