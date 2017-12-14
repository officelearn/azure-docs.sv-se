---
title: 'Azure Active Directory B2C: Google + configuration | Microsoft Docs'
description: "Ange registrering och inloggning för konsumenter med Google + konton i dina program som skyddas av Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mtillman
editor: bryanla
ms.assetid: 4dcca66f-29e4-4b4d-8840-50baad736bd7
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 93589352094fdd556811ba906ee27e7b8ac1d8b5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory B2C: Ange registrering och inloggning för konsumenter med Google + konton
## <a name="create-a-google-application"></a>Skapa ett Google +-program
Om du vill använda Google + som en identitetsleverantör i Azure Active Directory (AD Azure) B2C, måste du skapa ett Google +-program och ange rätt parametrar. Du behöver ett Google +-konto om du vill. Om du inte har något du kan hämta den på [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Gå till den [Google utvecklare konsolen](https://console.developers.google.com/) och logga in med Google + autentiseringsuppgifterna för ditt konto.
2. Klicka på **skapa projekt**, ange en **projektnamn**, och klicka sedan på **skapa**.
   
    ![Google + - komma igång](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google + - nytt projekt](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. Klicka på **API-hanterare** och klicka sedan på **autentiseringsuppgifter** i det vänstra navigeringsfönstret.
4. Klicka på den **OAuth-medgivande skärmen** högst upp.
   
    ![Google + - autentiseringsuppgifter](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Välj eller ange en giltig **e-postadress**, ange en **produktnamn**, och klicka på **spara**.
   
    ![Google + - skärmen för OAuth-medgivande](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Klicka på **nya autentiseringsuppgifter** och välj sedan **OAuth-klient-ID**.
   
    ![Google + - skärmen för OAuth-medgivande](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. Under **programtyp**väljer **webbprogrammet**.
   
    ![Google + - skärmen för OAuth-medgivande](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. Ange en **namn** för ditt program ange `https://login.microsoftonline.com` i den **behörighet JavaScript ursprung** fältet och `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i den **auktoriserad omdirigerings-URI: er** fältet. Ersätt **{klient}** med din klient namn (till exempel contosob2c.onmicrosoft.com). Den **{klient}** värdet är skiftlägeskänsligt. Klicka på **Skapa**.
   
    ![Google + - skapar klient-ID](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Kopiera värdena i **klient-ID** och **klienthemlighet**. Du måste båda för att konfigurera Google + som en identitetsleverantör i din klient. **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.
   
    ![Google + - klienthemlighet](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Konfigurera Google + som en identitetsleverantör i din klientorganisation
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure-portalen.
2. Klicka på B2C-funktionsbladet **identitetsleverantörer**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för providerkonfigurationen identitet. Till exempel ange ”G +”.
5. Klicka på **identitet providertyp**väljer **Google**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsleverantör** och ange klient-ID och klienthemlighet på Google +-programmet som du skapade tidigare.
7. Klicka på **OK** och klicka sedan på **skapa** att spara din Google +-konfiguration.

