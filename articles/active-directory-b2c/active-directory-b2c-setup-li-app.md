---
title: LinkedIn-konfigurationen i Azure Active Directory B2C | Microsoft Docs
description: Ange registrering och inloggning för konsumenter med LinkedIn-konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6db3832031a1bb960ee40c0e4fb8c3d0591a976c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711706"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure Active Directory B2C: Ange registrering och inloggning för konsumenter med LinkedIn-konton
## <a name="create-a-linkedin-application"></a>Skapa ett LinkedIn-program
Om du vill använda LinkedIn som en identitetsleverantör i Azure Active Directory (AD Azure) B2C måste du skapa ett LinkedIn-program och ange rätt parametrar. Du behöver en LinkedIn-konto för att göra detta. Om du inte har något du kan hämta den på [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Gå till den [LinkedIn utvecklare webbplats](https://www.developer.linkedin.com/) och logga in med autentiseringsuppgifterna för ditt LinkedIn.
2. Klicka på **Mina appar** i övre menyraden och klicka sedan på **skapa program**.
   
    ![LinkedIn - ny app](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. I den **skapa ett nytt program** formuläret, Fyll i den relevanta informationen (**företagsnamn**, **namn**, **beskrivning**, **programmets logotypen URL**, **användningen av**, **Webbadress**, **företags-e-** och **telefon**).
4. Godkänner den **LinkedIn API användningsvillkoren** och på **skicka**.
   
    ![LinkedIn - registrera appen](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. Kopiera värdena i **klient-ID** och **Klienthemlighet**. (Du hittar dem under **autentiseringsnycklar**.) Du måste båda för att konfigurera LinkedIn som en identitetsleverantör i din klient.
   
   > [!NOTE]
   > **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.
   > 
   > 
6. Ange `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i den **behörighet omdirigerings-URL: er** fält (under **OAuth 2.0**). Ersätt **{klient}** med din klient namn (till exempel contoso.onmicrosoft.com). Klicka på **Lägg till**, och klicka sedan på **uppdatering**. Den **{klient}** värdet ska vara gemener.
   
    ![LinkedIn - installationsprogrammet för app](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Konfigurera LinkedIn som en identitetsleverantör i din klientorganisation
1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure-portalen.
2. Klicka på B2C-funktionsbladet **identitetsleverantörer**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange ett eget **namn** för providerkonfigurationen identitet. Ange till exempel ”LI”.
5. Klicka på **identitet providertyp**väljer **LinkedIn**, och klicka på **OK**.
6. Klicka på **ställa in den här identitetsleverantör** och ange klient-ID och klienthemlighet för LinkedIn-program som du skapade tidigare.
7. Klicka på **OK** och klicka sedan på **skapa** spara LinkedIn-konfigurationen.

