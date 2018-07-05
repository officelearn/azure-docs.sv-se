---
title: Google +-konfiguration i Azure Active Directory B2C | Microsoft Docs
description: Ge registrera dig och logga in till konsumenter med Google +-konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fe5722e8d5a0a8a5bf172577777ccb899fb7b94d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443434"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory B2C: Ge registrera dig och logga in till konsumenter med Google +-konton
## <a name="create-a-google-application"></a>Skapa ett Google +-program
För att använda Google + som en identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett Google +-program och ange rätt parametrar. Du behöver en Google +-konto om du vill. Om du inte har någon kan du hämta den på [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Gå till den [Google utvecklare konsolen](https://console.developers.google.com/) och logga in med dina Google +-kontouppgifter.
2. Klicka på **skapa projekt**, ange en **projektnamn**, och klicka sedan på **skapa**.
   
    ![Google + - komma igång](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google + - nytt projekt](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. Klicka på **API-hanterare** och klicka sedan på **autentiseringsuppgifter** i det vänstra navigeringsfönstret.
4. Klicka på den **OAuth godkännandeskärmen** fliken högst upp.
   
    ![Google + - autentiseringsuppgifter](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Välj eller ange en giltig **e-postadress**, ange en **produktnamn**, och klicka på **spara**.
   
    ![Google + - skärmen för OAuth-medgivande](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Klicka på **nya autentiseringsuppgifter** och välj sedan **OAuth klient-ID**.
   
    ![Google + - skärmen för OAuth-medgivande](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. Under **programtyp**väljer **webbprogram**.
   
    ![Google + - skärmen för OAuth-medgivande](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. Ange en **namn** för ditt program ange `https://login.microsoftonline.com` i den **behörighet JavaScript ursprung** fält, och `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i den **auktoriserade omdirigerings-URI: er** fältet. Ersätt **{klient}** med klientens namn (exempel: contosob2c.onmicrosoft.com). Den **{klient}** värdet är skiftlägeskänsligt. Klicka på **Skapa**.
   
    ![Google + - skapa klient-ID](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Kopiera värdena för **klient-ID** och **klienthemlighet**. Du måste båda för att konfigurera Google + som en identitetsprovider i din klient. **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.
   
    ![Google + - klienthemlighet](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Konfigurera Google + som en identitetsprovider i din klient
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure portal.
2. Klicka på B2C-funktionsbladet **identitetsprovidrar**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för konfigurationen av identity-providern. Till exempel ange ”G +”.
5. Klicka på **identifiera providertyp**väljer **Google**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsprovidern** och ange klient-ID och klienthemlighet på Google +-programmet som du skapade tidigare.
7. Klicka på **OK** och klicka sedan på **skapa** att spara din Google +-konfiguration.

