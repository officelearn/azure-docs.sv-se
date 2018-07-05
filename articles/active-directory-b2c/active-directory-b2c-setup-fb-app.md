---
title: Facebook-konfiguration i Azure Active Directory B2C | Microsoft Docs
description: Ge registrera dig och logga in till konsumenter med hjälp av Facebook-konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 8/7/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 316e44ea92a25ab804c8cc499f91c45e4a66ef02
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445508"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C: Ge registrera dig och logga in till konsumenter med hjälp av Facebook-konton
## <a name="create-a-facebook-application"></a>Skapa ett Facebook-program
För att använda Facebook som en identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett Facebook-program och ange rätt parametrar. Du behöver ett Facebook-konto för att göra detta. Om du inte har någon kan du hämta den på [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Gå till den [Facebook för utvecklare](https://developers.facebook.com/) webbplatsen och logga in med ditt Facebook kontoautentiseringsuppgifter.
2. Om du inte redan har gjort det så, behöver du registrera som Facebook-utvecklare. Gör detta genom att klicka på **registrera** (i det övre högra hörnet på sidan), acceptera Facebooks principer och gör registreringen.
3. Klicka på **Mina appar** och klicka sedan på **lägga till en ny App**. 
4. I formuläret, tillhandahåller en **visningsnamn** och en giltig **kontakta e-post**.
5. Klicka på **skapa App-ID**. Du kan behöva du acceptera Facebook-plattformen principer och slutföra en online säkerhetskontrollen.
6. I den vänstra kolumnen klickar du på **inställningar** och välj sedan **grundläggande** om inte redan är valt.
7. Välj en **kategori**. 
8. Klicka på **+ Lägg till plattform** och välj **webbplats**.
   
    ![Facebook - inställningar](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - Settings - webbplats](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Ange `https://login.microsoftonline.com/` i den **webbplatsens URL** fält och klicka på **spara ändringar** längst ned på sidan.
   
    ![Facebook - webbplatsens URL](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Kopiera värdet för **App-ID**. Klicka på **visa** och kopiera värdet för **Apphemlighet**. Du måste båda för att konfigurera Facebook som en identitetsprovider i din klient. **Apphemlighet** är en viktig säkerhetsuppgift för autentisering.
   
    ![Facebook - App-ID & Apphemlighet](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. Klicka på **+ Lägg till produkt** i det vänstra navigeringsfönstret och sedan den **Set Up** för **Facebook-inloggning**.
   
    ![Facebook - Facebook-inloggning](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Klicka på **inställningar** på det högra navigeringsfältet under **Facebook-inloggning**

    ![Facebook - inställningar för Facebook-inloggning](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. Ange `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i den **giltig OAuth omdirigerings-URI: er** i den **OAuth klientinställningar** avsnittet. Ersätt **{klient}** med klientens namn (exempel: contosob2c.onmicrosoft.com). Klicka på **spara ändringar** längst ned på sidan.
    
    ![Facebook - OAuth omdirigerings-URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Om du vill göra dina Facebook-programmet kan användas av Azure AD B2C måste du göra den tillgänglig offentligt. Du kan göra detta genom att klicka på **App granska** i det vänstra navigeringsfönstret och genom att stänga växeln överst på sidan för att **Ja** och klicka på **Bekräfta**.
    
    ![Facebook - App offentliga](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Konfigurera Facebook som en identitetsprovider i din klient
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure portal.
2. Klicka på B2C-funktionsbladet **identitetsprovidrar**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för konfigurationen av identity-providern. Till exempel ange ”Facebook”.
5. Klicka på **identifiera providertyp**väljer **Facebook**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsprovidern** och ange det app-ID och en apphemlighet (för Facebook-programmet som du skapade tidigare) i den **klient-ID** och **klienthemlighet** respektive fält.
7. Klicka på **OK**, och klicka sedan på **skapa** att spara din Facebook-konfiguration.

> [!NOTE]
> Att lägga till en **identitetsprovider** till din klient inte ändra de befintliga principerna. Kom ihåg att uppdatera dina principer genom att inkludera den identitetsprovider som du nyss skapade.
>