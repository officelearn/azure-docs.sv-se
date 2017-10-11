---
title: "Azure Active Directory B2C: Konfigurationen för Microsoft-kontot | Microsoft Docs"
description: "Ange registrering och inloggning för konsumenter med Microsoft-konton i dina program som skyddas av Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 06407322-142c-4cb3-9106-a8d752c4c853
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 59879dc0b3fc1d7af3e2a1f67f1701f451de9126
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory B2C: Ange registrering och inloggning för konsumenter med Microsoft-konton
## <a name="create-a-microsoft-account-application"></a>Skapa ett program för Microsoft-konto
Om du vill använda Microsoft-konto som en identitetsleverantör i Azure Active Directory (AD Azure) B2C måste du skapa ett program för Microsoft-konto och ange rätt parametrar. Du behöver ett Microsoft-konto för att göra detta. Om du inte har något du kan hämta den på [https://www.live.com/](https://www.live.com/).

1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) och logga in med ditt Microsoft-kontouppgifter.
2. Klicka på **Lägg till en app**.
   
    ![Microsoft-konto – Lägg till en ny app](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. Ange en **namn** för dina program och klickar på **skapa program**.
   
    ![Microsoft-konto - programnamn](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. Kopiera värdet för **program-Id**. Du behöver det för att konfigurera Microsoft-konto som en identitetsleverantör i din klient.
   
    ![Microsoft-konto - program-Id](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. Klicka på **Lägg till plattformen** och välj **Web**.
   
    ![Microsoft-konto – Lägg till plattformen](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Microsoft-konto - webbtjänst](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. Ange `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i den **omdirigerings-URI: er** fältet. Ersätt **{klient}** med din klient namn (till exempel contosob2c.onmicrosoft.com).
   
    ![Microsoft-konto - omdirigerings-URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. Klicka på **generera nya lösenord** under den **programmet hemligheter** avsnitt. Kopiera det nya lösenordet som visas på skärmen. Du behöver det för att konfigurera Microsoft-konto som en identitetsleverantör i din klient. Lösenordet är en viktig säkerhetsuppgift för autentisering.
   
    ![Microsoft-konto – Skapa nytt lösenord](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Microsoft-konto - nytt lösenord](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. Markera kryssrutan som säger **Live SDK stöd** under den **avancerade alternativ** avsnitt. Klicka på **Spara**.
   
    ![Microsoft-konto - stöd för Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Konfigurera Microsoft-konto som en identitetsleverantör i din klientorganisation
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure-portalen.
2. Klicka på B2C-funktionsbladet **identitetsleverantörer**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för providerkonfigurationen identitet. Till exempel ange ”MSA”.
5. Klicka på **identitet providertyp**väljer **Microsoft-konto**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsleverantör** och ange program-Id och lösenord för programmet Microsoft-konto som du skapade tidigare.
7. Klicka på **OK** och klicka sedan på **skapa** att spara konfigurationen för Microsoft-kontot.

