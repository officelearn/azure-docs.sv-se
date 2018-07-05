---
title: Konfigurationen av Microsoft-konto i Azure Active Directory B2C | Microsoft Docs
description: Ange registrera dig och logga in till kunder med Microsoft-konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c788b14a99125a208390cd4f8ead338efed06933
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444175"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory B2C: Ge registrera dig och logga in till konsumenter med hjälp av Microsoft-konton
## <a name="create-a-microsoft-account-application"></a>Skapa ett program för Microsoft-konto
Om du vill använda Microsoft-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program för Microsoft-konto och ange rätt parametrar. Du behöver ett Microsoft-konto för att göra detta. Om du inte har någon kan du hämta den på [ https://www.live.com/ ](https://www.live.com/).

1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) och logga in med autentiseringsuppgifterna för ditt Microsoft-konto.
2. Klicka på **lägga till en app**.
   
    ![Microsoft-konto – lägga till en ny app](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. Ange en **namn** för dina program och klicka på **skapa program**.
   
    ![Microsoft-konto – namn](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. Kopiera värdet för **program-Id**. Du behöver den för att konfigurera Microsoft-konto som en identitetsprovider i din klient.
   
    ![Microsoft-konto - ID: T](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. Klicka på **Lägg till plattform** och välj **Web**.
   
    ![Microsoft-konto – Lägg till plattform](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Microsoft-konto – Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. Ange `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i den **omdirigerings-URI: er** fält. Ersätt **{klient}** med klientens namn (exempel: contosob2c.onmicrosoft.com).
   
    ![Microsoft-konto – omdirigerings-URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. Klicka på **generera nytt lösenord** under den **Programhemligheter** avsnittet. Kopiera det nya lösenordet som visas på skärmen. Du behöver den för att konfigurera Microsoft-konto som en identitetsprovider i din klient. Det här lösenordet är en viktig säkerhetsuppgift för autentisering.
   
    ![Microsoft-konto – generera ett nytt lösenord](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Microsoft-konto – nytt lösenord](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. Markera rutan **Live SDK stöd** under den **avancerade alternativ** avsnittet. Klicka på **Spara**.
   
    ![Microsoft-konto – stöd för Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Konfigurera Microsoft-konto som en identitetsprovider i din klient
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure portal.
2. Klicka på B2C-funktionsbladet **identitetsprovidrar**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för konfigurationen av identity-providern. Till exempel ange ”MSA”.
5. Klicka på **identifiera providertyp**väljer **microsoftkonto**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsprovidern** och ange program-Id och lösenord för programmet för Microsoft-konto som du skapade tidigare.
7. Klicka på **OK** och klicka sedan på **skapa** att spara din konfiguration för Microsoft-konto.

