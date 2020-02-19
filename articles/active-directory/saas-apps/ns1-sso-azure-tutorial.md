---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med NS1 SSO för Azure | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och NS1 SSO för Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 24a1afb6-b8b6-4787-bd4b-8fe3a32f8def
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5ba34aeb97808c19c78f187dcc68309893d1d1b
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429749"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med NS1 SSO för Azure

I den här självstudien får du lära dig hur du integrerar NS1 SSO för Azure med Azure Active Directory (Azure AD). När du integrerar NS1 SSO för Azure med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till NS1 SSO för Azure.
* Gör det möjligt för användarna att logga in automatiskt till NS1 SSO för Azure med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* NS1 SSO för Azure Single Sign-on (SSO)-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* NS1 SSO för Azure stöder **SP-och IDP** -INITIERAd SSO
* När du har konfigurerat NS1 SSO för Azure kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-ns1-sso-for-azure-from-the-gallery"></a>Lägga till NS1 SSO för Azure från galleriet

Om du vill konfigurera integrationen av NS1 SSO för Azure i Azure AD måste du lägga till NS1 SSO för Azure från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **ns1 SSO för Azure** i sökrutan.
1. Välj **ns1 SSO för Azure** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Konfigurera och testa enkel inloggning med Azure AD för NS1 SSO för Azure

Konfigurera och testa Azure AD SSO med NS1 SSO för Azure med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i NS1 SSO för Azure.

Om du vill konfigurera och testa Azure AD SSO med NS1 SSO för Azure slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ns1 SSO för Azure SSO](#configure-ns1-sso-for-azure-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa ns1 SSO för Azure test User](#create-ns1-sso-for-azure-test-user)** -om du vill ha en motsvarighet till B. Simon i ns1 SSO för Azure som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för **ns1 SSO för Azure** -programintegration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du URL:en: `https://api.nsone.net/saml/metadata`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://api.nsone.net/saml/sso/<ssoid>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > Värdet för svars-URL är inte verkligt. Uppdatera värdet för svars-URL med den faktiska svars-URL:en. Kontakta [ns1 SSO för Azure client support-teamet](mailto:techops@nsone.net) för att hämta värdet. Du kan även se de mönster som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. NS1 SSO för Azure-program förväntar sig SAML-intyg i ett särskilt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![Avsnittet Attribute](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Klicka på attributnamnet för att redigera anspråket.

    ![Avsnittet Attribute](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Välj **omvandling**.

    ![Avsnittet Attribute](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. I avsnittet **Hantera omvandling** utför du följande steg:

    ![Avsnittet Attribute](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Välj **ExactMailPrefix ()** som **omvandling**.

    1. Välj **User. UserPrincipalName** som **parameter 1**.

    1. Klicka på **Lägg till**.

    1. Klicka på **Spara**

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **Ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till NS1 SSO för Azure.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **ns1 SSO för Azure**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-ns1-sso-for-azure-sso"></a>Konfigurera NS1 SSO för Azure SSO

Om du vill konfigurera enkel inloggning på **ns1 SSO för Azure** -sidan måste du skicka **URL: en för appens Federations-metadata** till [ns1 SSO för Azure support team](mailto:techops@nsone.net). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-ns1-sso-for-azure-test-user"></a>Skapa NS1 SSO för Azure test User

I det här avsnittet skapar du en användare som heter B. Simon i NS1 SSO för Azure. Arbeta med NS1 SSO för Azure support team för att lägga till användarna i NS1 SSO för Azure-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen NS1 SSO för Azure på åtkomst panelen, bör du loggas in automatiskt på NS1 SSO för Azure som du ställer in SSO för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova NS1 SSO för Azure med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)