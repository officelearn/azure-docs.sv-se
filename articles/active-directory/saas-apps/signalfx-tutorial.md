---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SignalFx | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea81f0046d7f73d845ed49325a3d621e6b7735e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443269"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SignalFx

I den här självstudien får du lära dig hur du integrerar SignalFx med Azure Active Directory (Azure AD). När du integrerar SignalFx med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SignalFx.
* Gör det möjligt för användarna att logga in automatiskt till SignalFx med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SignalFx för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SignalFx stöder **IDP** INITIERAd SSO
* SignalFx stöder **just-in-Time** User-etablering

## <a name="adding-signalfx-from-the-gallery"></a>Lägga till SignalFx från galleriet

Om du vill konfigurera integreringen av SignalFx i Azure AD måste du lägga till SignalFx från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **SignalFx** i sökrutan.
1. Välj **SignalFx** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-signalfx"></a>Konfigurera och testa enkel inloggning med Azure AD för SignalFx

Konfigurera och testa Azure AD SSO med SignalFx med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SignalFx.

Om du vill konfigurera och testa Azure AD SSO med SignalFx, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SIGNALFX SSO](#configure-signalfx-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa SignalFx test User](#create-signalfx-test-user)** -om du vill ha en motsvarighet till B. Simon i SignalFx som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **SignalFx** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du in en URL: `https://api.signalfx.com/v1/saml/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE]
    > Föregående värde är inte ett reellt värde. Du uppdaterar värdet med den faktiska svars-URL: en som beskrivs senare i självstudien.

1. SignalFx-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![mallar](common/default-attributes.png)

1. Utöver ovan förväntar sig SignalFx-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Namn |  Källattribut|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User. email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera SignalFx** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SignalFx.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **SignalFx**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-signalfx-sso"></a>Konfigurera SignalFx SSO

1. Logga in på din SignalFx företags webbplats som administratör.

1. I SignalFx klickar du på **integreringarna** överst för att öppna sidan integrations.

    ![SignalFx-integrering](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. Klicka på panelen **Azure Active Directory** under avsnittet **inloggnings tjänster** .

    ![SignalFx SAML](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. Klicka på **ny integrering** och under fliken **Installera** utför du följande steg:

    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. I text rutan **namn** anger du ett nytt integrations namn, t. ex. **OurOrgName SAML SSO**.

    b. Kopiera värdet för **integrations-ID** och Lägg till i **svars-URL: en** i stället för `<integration ID>` i text rutan **svars-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    c. Klicka på **överför fil** för att ladda upp **Base64-kodat certifikat** som hämtats från Azure Portal i text rutan **certifikat** .

    d. I text rutan **utfärdar-URL** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    e. I text rutan **metadata-URL** klistrar du in **inloggnings-URL: en** som du har kopierat från Azure Portal.

    f. Klicka på **Spara**.

### <a name="create-signalfx-test-user"></a>Skapa SignalFx test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i SignalFx. SignalFx stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas vid ett försök att komma åt SignalFx om den inte redan finns.

När en användare loggar in på SignalFx från SAML SSO för första gången, skickar [SignalFx support-teamet](mailto:kmazzola@signalfx.com) ett e-postmeddelande som innehåller en länk som de måste klicka på för att autentisera. Detta sker bara första gången användaren loggar in. efterföljande inloggnings försök kräver ingen e-postvalidering.

> [!Note]
> Kontakta [SignalFx support team](mailto:kmazzola@signalfx.com) om du behöver skapa en användare manuellt

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SignalFx på åtkomst panelen, bör du loggas in automatiskt på den SignalFx som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SignalFx med Azure AD](https://aad.portal.azure.com/)