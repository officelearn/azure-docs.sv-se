---
title: LinkedIn-konfiguration i Azure Active Directory B2C | Microsoft Docs
description: Ge registrera dig och logga in till konsumenter med LinkedIn-konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7588711bd1c2a02e2e9a100d2ba182f43e7df488
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446114"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure Active Directory B2C: Ge registrera dig och logga in till konsumenter med LinkedIn-konton
## <a name="create-a-linkedin-application"></a>Skapa ett program med LinkedIn
För att använda LinkedIn som en identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett LinkedIn-program och ange rätt parametrar. Du behöver en LinkedIn-konto för att göra detta. Om du inte har någon kan du hämta den på [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Gå till den [LinkedIn utvecklare webbplats](https://www.developer.linkedin.com/) och logga in med autentiseringsuppgifterna för ditt LinkedIn-konto.
2. Klicka på **Mina appar** i den övre menyraden och sedan på **skapa program**.
   
    ![LinkedIn - ny app](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. I den **skapa ett nytt program** formuläret, Fyll i den relevanta informationen (**företagsnamn**, **namn**, **beskrivning**, **Programmets logotyp URL**, **användningen av**, **Webbadress**, **företags-e-** och **Företagstelefon**).
4. Godkänn den **LinkedIn API användningsvillkor** och klicka på **skicka**.
   
    ![LinkedIn - registrera app](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. Kopiera värdena för **klient-ID** och **Klienthemlighet**. (Du hittar dem under **autentiseringsnycklar**.) Du måste båda för att konfigurera LinkedIn som en identitetsprovider i din klient.
   
   > [!NOTE]
   > **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.
   > 
   > 
6. Ange `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i den **behörighet omdirigerings-URL: er** fält (under **OAuth 2.0**). Ersätt **{klient}** med klientens namn (till exempel contoso.onmicrosoft.com). Klicka på **Lägg till**, och klicka sedan på **uppdatering**. Den **{klient}** . värdet bör vara gemener.
   
    ![LinkedIn - installationsprogrammet app](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Konfigurera LinkedIn som en identitetsprovider i din klient
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure portal.
2. Klicka på B2C-funktionsbladet **identitetsprovidrar**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för konfigurationen av identity-providern. Ange till exempel ”LI”.
5. Klicka på **identifiera providertyp**väljer **LinkedIn**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsprovidern** och ange klient-ID och klienthemlighet för LinkedIn-programmet som du skapade tidigare.
7. Klicka på **OK** och klicka sedan på **skapa** att spara din LinkedIn-konfiguration.

