---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med RStudio Server Pro SAML-autentisering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RStudio Server Pro SAML-autentisering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: ecefc7c585f2f556e76efe6a3a272e38de98e297
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181568"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rstudio-server-pro-saml-authentication"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med RStudio Server Pro SAML-autentisering

I den här självstudien får du lära dig hur du integrerar RStudio Server Pro SAML-autentisering med Azure Active Directory (Azure AD). När du integrerar RStudio Server Pro SAML-autentisering med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till RStudio Server Pro SAML-autentisering.
* Gör det möjligt för användarna att logga in automatiskt till RStudio Server Pro SAML-autentisering med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* RStudio Server Pro SAML Authentication med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* RStudio Server Pro SAML-autentisering stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-rstudio-server-pro-saml-authentication-from-the-gallery"></a>Lägga till RStudio Server Pro SAML-autentisering från galleriet

Om du vill konfigurera integreringen av RStudio Server Pro SAML-autentisering i Azure AD måste du lägga till RStudio Server Pro SAML-autentisering från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **RSTUDIO Server Pro SAML Authentication** i sökrutan.
1. Välj **RStudio Server Pro SAML Authentication** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-server-pro-saml-authentication"></a>Konfigurera och testa Azure AD SSO för RStudio Server Pro SAML-autentisering

Konfigurera och testa Azure AD SSO med RStudio Server Pro SAML-autentisering med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i RStudio Server Pro SAML-autentisering.

Utför följande steg för att konfigurera och testa Azure AD SSO med RStudio Server Pro SAML-autentisering:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera RStudio Server Pro SAML Authentication SSO](#configure-rstudio-server-pro-saml-authentication-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa RStudio Server Pro SAML Authentication test User](#create-rstudio-server-pro-saml-authentication-test-user)** -för att få en motsvarighet till B. Simon i RStudio Server Pro SAML-autentisering som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **RStudio Server Pro SAML Authentication** Application Integration hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<SUBDOMAIN>.rstudioservices.com/<PATH>/saml/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.rstudioservices.com/<PATH>/saml/acs`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.rstudioservices.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [RStudio Server Pro SAML Authentication client support team](mailto:support@rstudio.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Exempelvis `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till RStudio Server Pro SAML-autentisering.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **RStudio Server Pro SAML Authentication**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-rstudio-server-pro-saml-authentication-sso"></a>Konfigurera RStudio Server Pro SAML Authentication SSO

Om du vill konfigurera enkel inloggning på **RStudio Server Pro SAML Authentication** -sidan måste du skicka **URL: en för appens Federations-metadata** till [support teamet för RStudio Server Pro SAML-autentisering](mailto:support@rstudio.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-rstudio-server-pro-saml-authentication-test-user"></a>Skapa test användare av RStudio-Server Pro SAML-autentisering

I det här avsnittet skapar du en användare som heter B. Simon i RStudio Server Pro SAML-autentisering. Arbeta med [RStudio Server Pro SAML Authentication support team](mailto:support@rstudio.com) för att lägga till användare i RStudio Server Pro SAML Authentication Platform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till RStudio Server Pro SAML Authentication Sign on URL där du kan starta inloggnings flödet.  

* Gå till RStudio Server Pro SAML Authentication inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal och du bör logga in automatiskt till den RStudio Server Pro SAML-autentisering som du har konfigurerat SSO för 

Du kan också använda Microsoft Access-panelen för att testa programmet i vilket läge som helst. När du klickar på panelen RStudio Server Pro SAML Authentication på åtkomst panelen, om den har kon figurer ATS i SP-läge omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på den RStudio Server Pro SAML-autentisering som du har konfigurerat SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat RStudio Server Pro SAML-autentisering kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).