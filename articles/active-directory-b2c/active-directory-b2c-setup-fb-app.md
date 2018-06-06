---
title: Facebook-konfigurationen i Azure Active Directory B2C | Microsoft Docs
description: Ange registrering och inloggning för konsumenter med Facebook-konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 8/7/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ceab8df79337796bed314e04bacc37bf2ebe1d00
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712325"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C: Ange registrering och inloggning för konsumenter med Facebook-konton
## <a name="create-a-facebook-application"></a>Skapa ett Facebook-program
Om du vill använda Facebook som en identitetsleverantör i Azure Active Directory (AD Azure) B2C måste du skapa ett Facebook-program och ange rätt parametrar. Du behöver en Facebook-konto för att göra detta. Om du inte har något du kan hämta den på [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Gå till den [Facebook för utvecklare](https://developers.facebook.com/) webbplatsen och logga in med dina Facebook kontoautentiseringsuppgifter.
2. Om du inte redan har gjort det så, behöver du registrera som en Facebook-utvecklare. Gör detta genom att klicka på **registrera** (i det övre högra hörnet på sidan) och acceptera Facebooks principer och slutföra registreringen stegen.
3. Klicka på **Mina appar** och klicka sedan på **lägga till en ny App**. 
4. I formuläret kan tillhandahålla en **visningsnamn** och en giltig **kontakta e-post**.
5. Klicka på **skapa App-ID**. Du kan behöva du acceptera Facebook-plattformen principer och slutföra en kontroll av online-säkerhet.
6. Klicka i den vänstra kolumnen **inställningar** och välj sedan **grundläggande** om du inte redan har markerats.
7. Välj en **kategori**. 
8. Klicka på **+ Lägg till plattformen** och välj **webbplats**.
   
    ![Facebook - inställningar](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - inställningar - webbplats](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Ange `https://login.microsoftonline.com/` i den **Webbadress** fältet och klickar sedan på **spara ändringar** längst ned på sidan.
   
    ![Facebook - Webbadress](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Kopiera värdet för **App-ID**. Klicka på **visa** och kopiera värdet för **App hemlighet**. Du måste båda för att konfigurera Facebook som en identitetsleverantör i din klient. **Appen hemlighet** är en viktig säkerhetsuppgift för autentisering.
   
    ![Facebook - App-ID & App hemlighet](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. Klicka på **+ Lägg till produkten** i navigeringen till vänster och sedan den **konfigurera** knappen för **Facebook inloggningen**.
   
    ![Facebook - Facebook-inloggning](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Klicka på **inställningar** på rätt nav under **Facebook-inloggning**

    ![Facebook - Facebook inloggningsinställningar](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. Ange `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i den **omdirigerings-URI: er för giltig OAuth** i den **OAuth klientinställningar** avsnitt. Ersätt **{klient}** med din klient namn (till exempel contosob2c.onmicrosoft.com). Klicka på **spara ändringar** längst ned på sidan.
    
    ![Facebook - OAuth omdirigerings-URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Om du vill göra Facebook-programmet kan användas av Azure AD B2C som du behöver göra den tillgänglig för allmänheten. Du kan göra detta genom att klicka på **App granska** i navigeringen till vänster och genom att aktivera växeln längst upp på sidan för att **Ja** och klicka på **Bekräfta**.
    
    ![Facebook - App offentliga](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Konfigurera Facebook som en identitetsleverantör i din klientorganisation
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure-portalen.
2. Klicka på B2C-funktionsbladet **identitetsleverantörer**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för providerkonfigurationen identitet. Till exempel ange ”Facebook”.
5. Klicka på **identitet providertyp**väljer **Facebook**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsleverantör** och ange den app hemligheten (för Facebook-programmet som du skapade tidigare) och app-ID i den **klient-ID** och **klienthemlighet** respektive fält.
7. Klicka på **OK**, och klicka sedan på **skapa** spara Facebook-konfigurationen.

> [!NOTE]
> Lägga till en **identitetsleverantör** till din klient inte ändra din befintliga principer. Kom ihåg att uppdatera dina principer genom att inkludera den identitetsleverantör som du nyss skapade.
>