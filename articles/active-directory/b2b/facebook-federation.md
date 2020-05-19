---
title: Lägg till Facebook som en identitets leverantör – Azure AD
description: Federera med Facebook och aktivera externa användare (gäster) för att logga in på dina Azure AD-appar med sina egna Facebook-konton.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92fbd254f223e2c7eb70a4e86bb7e904294395e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597711"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Lägg till Facebook som identitets leverantör för externa identiteter

Du kan lägga till Facebook i dina självbetjänings registrerings användar flöden (för hands version) så att användarna kan logga in på dina program med sina egna Facebook-konton. För att användarna ska kunna logga in med Facebook måste du först aktivera självbetjänings [registrering](self-service-sign-up-user-flow.md) för din klient. När du har lagt till Facebook som identitets leverantör konfigurerar du ett användar flöde för programmet och väljer Facebook som ett av inloggnings alternativen.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Skapa en app i Facebook-utvecklarens konsol

Om du vill använda ett Facebook-konto som [identitets leverantör](identity-providers.md)måste du skapa ett program i Facebook-utvecklarens konsol. Om du inte redan har ett Facebook-konto kan du registrera dig på [https://www.facebook.com/](https://www.facebook.com) .

> [!NOTE]  
> Använd följande URL: er i steg 9 och 16 nedan.
> - För **webbplats-URL** anger du `https://login.microsoftonline.com` .
> - Ange för **giltiga OAuth-omdirigerings-URI: er** `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp` . Du hittar ditt `<tenant-ID>` i bladet Azure Active Directory översikt.


1. Logga in på [Facebook för utvecklare](https://developers.facebook.com/) med dina Facebook-kontoautentiseringsuppgifter.
2. Om du inte redan har gjort det måste du registrera dig som en Facebook-utvecklare. Om du vill göra det väljer du **Kom igång** i det övre högra hörnet på sidan, accepterar Facebook-principer och slutför registrerings stegen.
3. Välj **Mina appar** och sedan **skapa app**.
4. Ange ett **visnings namn** och en giltig **kontakt-e-postadress**.
5. Välj **skapa app-ID**. Detta kan kräva att du accepterar Facebook-plattforms principer och slutför en säkerhets kontroll online.
6. Välj **Inställningar**  >  **Basic**.
7. Välj en **kategori**, till exempel företag och sidor. Det här värdet krävs av Facebook, men används inte för Azure AD.
8. Längst ned på sidan väljer du **Lägg till plattform**och väljer sedan **webbplats**.
9. I **webbplats-URL**anger du lämplig URL (anges ovan).
10. I **Sekretess policyns URL**anger du webb adressen till sidan där du behåller sekretess information för programmet, till exempel `http://www.contoso.com` .
11. Välj **Spara ändringar**.
12. Kopiera värdet för **app-ID**överst på sidan.
13. Välj **Visa** och kopiera värdet för **appens hemlighet**. Du använder båda alternativen för att konfigurera Facebook som en identitets leverantör i din klient organisation. **App Secret** är en viktig säkerhets autentiseringsuppgift.
14. Välj plus tecknet bredvid **produkter**och välj sedan **Konfigurera** under **Facebook-inloggning**.
15. Under **Facebook-inloggning**väljer du **Inställningar**.
16. I **giltiga OAuth-omdirigerings-URI: er**anger du lämplig URL (anges ovan).
17. Välj **Spara ändringar** längst ned på sidan.
18. Om du vill göra ditt Facebook-program tillgängligt för Azure AD väljer du status väljaren längst upp till höger på sidan och **aktiverar det för att göra** programmet offentligt och väljer sedan **Växla läge**. I det här läget bör statusen ändras från **utveckling** till **Live**.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurera ett Facebook-konto som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din Azure AD-klient.
2. Under **Azure-tjänster**väljer du **Azure Active Directory**.
3. På den vänstra menyn väljer du **externa identiteter**.
4. Välj **alla identitets leverantörer**och välj sedan **Facebook**.
5. För **klient-ID**anger du **app-ID: t** för det Facebook-program som du skapade tidigare.
6. För **klient hemligheten**anger du **appens hemlighet** som du har spelat in.

   ![Skärm bild som visar sidan Lägg till social identitetsprovider](media/facebook-federation/add-social-identity-provider-page.png)

7. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

- [Bjud in externa användare för samarbete](add-users-administrator.md)
- [Lägga till självbetjänings registrering i en app](self-service-sign-up-user-flow.md)
