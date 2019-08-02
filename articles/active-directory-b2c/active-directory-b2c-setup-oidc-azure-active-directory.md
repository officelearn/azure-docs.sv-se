---
title: Konfigurera inloggning för en Azure Active Directory organisation – Azure Active Directory B2C
description: Konfigurera inloggning för en speciell Azure Active Directory organisation i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 58c6d1b032f5b492c5641ff51da80426124069b1
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716768"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurera inloggning för en speciell Azure Active Directory organisation i Azure Active Directory B2C

>[!NOTE]
> Den här funktionen är en allmänt tillgänglig förhandsversion. Använd inte funktionen i produktions miljöer.

Om du vill använda en Azure Active Directory (Azure AD) som [identitets leverantör](active-directory-b2c-reference-oauth-code.md) i Azure AD B2C måste du skapa ett program som representerar det. Den här artikeln visar hur du aktiverar inloggning för användare från en särskild Azure AD-organisation med hjälp av ett användar flöde i Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Skapa en Azure AD-App

Om du vill aktivera inloggning för användare från en specifik Azure AD-organisation måste du registrera ett program i Azure AD-klienten, som inte är samma som din Azure AD B2C klient organisation.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient. Välj **katalog-och prenumerations filter** på den översta menyn och välj den katalog som innehåller din Azure AD-klient. Detta är inte samma klient som din Azure AD B2C klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
4. Välj **ny registrering**.
5. Ange ett namn för ditt program. Till exempel `Azure AD B2C App`.
6. Godkänn bara valet av **konton i den här organisations katalogen** för det här programmet.
7. För omdirigerings- **URI: n**, godkänn värdet för **webb**och ange följande URL i gemener, där `your-B2C-tenant-name` ersätts med namnet på din Azure AD B2C-klient. Till exempel `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Alla URL: er ska nu använda [b2clogin.com](b2clogin.md).

8. Klicka på **Registrera**. Kopiera **program-ID: t (klienten)** som ska användas senare.
9. Välj **certifikat & hemligheter** på program menyn och välj sedan **ny klient hemlighet**.
10. Ange ett namn för klient hemligheten. Till exempel `Azure AD B2C App Secret`.
11. Välj förfallo period. För det här programmet godkänner du valet på **1 år**.
12. Välj **Lägg till** och kopiera värdet för den nya klient hemlighet som visas senare.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurera Azure AD som en identitets leverantör

1. Kontrol lera att du använder den katalog som innehåller Azure AD B2C klient. Välj **katalog-och prenumerations filter** på den översta menyn och välj den katalog som innehåller Azure AD B2C klient.
2. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
3. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
4. Ange ett **namn**. Ange till exempel `Contoso Azure AD`.
5. Välj **typ av identitetsprovider**, Välj **OpenID Anslut (förhands granskning)** och klicka sedan på **OK**.
6. Välj **Konfigurera den här identitets leverantören**
7. För **metadata-URL**anger du följande URL som `your-AD-tenant-domain` ersätter med domän namnet för din Azure AD-klient. Exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. För **klient-ID**anger du det program-ID som du tidigare har spelat in och för **klient hemlighet**anger du den klient hemlighet som du tidigare har registrerat.
9. Du kan också ange ett värde för **Domain_hint**. Till exempel `ContosoAD`. Detta är det värde som ska användas för att referera till den här identitets leverantören med *domain_hint* i begäran.
10. Klicka på **OK**.
11. Välj **mappa den här identitets leverantörens anspråk** och ange följande anspråk:

    - För **användar-ID**anger `oid`du.
    - I **visnings namn**anger `name`du.
    - För **angivet namn**anger `given_name`du.
    - För efter **namn**anger `family_name`du.
    - För **e-post**anger `unique_name`du.

12. Klicka på **OK**och sedan på **skapa** för att spara konfigurationen.
