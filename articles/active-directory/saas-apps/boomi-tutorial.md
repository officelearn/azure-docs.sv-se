---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Boomi | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2469745edb5b8b3696478603cfe874bcabc8c1ff
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231958"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Boomi

I den här självstudien får du lära dig hur du integrerar Boomi med Azure Active Directory (Azure AD). När du integrerar Boomi med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Boomi.
* Gör det möjligt för användarna att logga in automatiskt till Boomi med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Boomi för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Boomi stöder **IDP**-initierad enkel inloggning

## <a name="adding-boomi-from-the-gallery"></a>Lägga till Boomi från galleriet

För att konfigurera integreringen av Boomi till Azure AD behöver du lägga till Boomi från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Boomi** i sökrutan.
1. Välj **Boomi** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Konfigurera och testa enkel inloggning med Azure AD för Boomi

Konfigurera och testa Azure AD SSO med Boomi med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Boomi.

Om du vill konfigurera och testa Azure AD SSO med Boomi, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera BOOMI SSO](#configure-boomi-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Boomi test User](#create-boomi-test-user)** -om du vill ha en motsvarighet till B. Simon i Boomi som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Boomi** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du in en URL: `https://platform.boomi.com/`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Hämta det här värdet genom att kontakta [supportteamet för Boomi-klienten](https://boomi.com/company/contact/). Du kan även se de mönster som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Boomi-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Boomi-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Namn |  Källattribut|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Boomi** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Boomi.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **Boomi**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-boomi-sso"></a>Konfigurera Boomi SSO

1. Logga in på din Boomi-företags webbplats som administratör i ett annat webbläsarfönster.

1. Gå till **Företagsnamn** och sedan till **Konfigurera**.

1. Klicka på fliken **SSO Options** (SSO-alternativ) och utför stegen nedan.

    ![Konfigurera enkel inloggning på appsidan](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Markera kryssrutan **Enable SAML Single Sign-On** (Aktivera enkel inloggning med SAML).

    b. Klicka på **Importera** för att ladda upp det nedladdade certifikatet från Azure AD till **Certifikat för identitetsprovider**.

    c. I textrutan **Inloggnings-URL för identitetsprovider** anger du värdet för **inloggnings-URL** från konfigurationsfönstret för Azure AD-programmet.

    d. För **Federation Id Location** (Plats för federations-ID) markerar du alternativknappen **Federation Id is in FEDERATION_ID Attribute element** (Federations-ID finns i attributelementet FEDERATION_ID).

    e. Klicka på knappen **Spara**.

### <a name="create-boomi-test-user"></a>Skapa Boomi-testanvändare

För att Azure AD-användare ska kunna logga in på Boomi måste de tillhandahållas i Boomi. När det gäller Boomi är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Logga in på din Boomi-företags webbplats som administratör.

1. När du loggat in går du till **Användarhantering** och sedan till **Användare**.

    ![Användare](./media/boomi-tutorial/tutorial_boomi_001.png "Användare")

1. Klicka på ikonen **+** så öppnas dialogrutan **Add/Maintain User Roles** (Lägg till/underhåll användarroller).

    ![Användare](./media/boomi-tutorial/tutorial_boomi_002.png "Användare")

    ![Användare](./media/boomi-tutorial/tutorial_boomi_003.png "Användare")

    a. I textrutan **User e-mail address** (E-post för användare) skriver du användarens e-postadress som B.Simon@contoso.com.

    b. I text rutan **förnamn** skriver du det första namnet på användaren, t. ex. B.

    c. I textrutan **Efternamn** skriver du efternamnet: Simon.

    d. Ange användarens **Federations-ID**. Varje användare ha ett Federation-ID som unikt identifierar användare i kontot.

    e. Tilldela rollen **Standardanvändare** till användaren. Tilldela inte administratörs rollen eftersom detta ger dem normal åtkomst till atmosfär samt enkel inloggning.

    f. Klicka på **OK**.

    > [!NOTE]
    > Användaren får inget e-postmeddelande med ett välkomst meddelande som innehåller ett lösen ord som kan användas för att logga in på AtomSphere-kontot eftersom lösen ordet hanteras via identitets leverantören. Du kan använda andra Boomi-verktyg eller API: er för användar konton som tillhandahålls av Boomi för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Boomi-panelen i åtkomstpanelen bör du automatiskt loggas in på Boomi som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Boomi med Azure AD](https://aad.portal.azure.com/)