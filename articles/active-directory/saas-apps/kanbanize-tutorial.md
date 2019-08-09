---
title: 'Självstudier: Azure Active Directory integrering med Kanbanize | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kanbanize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69103ea0e6088b4a823df34ebd982c67e2502cb3
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879507"
---
# <a name="tutorial-integrate-kanbanize-with-azure-active-directory"></a>Självstudier: Integrera Kanbanize med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Kanbanize med Azure Active Directory (Azure AD). När du integrerar Kanbanize med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Kanbanize.
* Gör det möjligt för användarna att logga in automatiskt till Kanbanize med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Kanbanize för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Kanbanize stöder **SP-och IDP** -INITIERAd SSO
* Kanbanize stöder **just-in-Time** User-etablering

## <a name="adding-kanbanize-from-the-gallery"></a>Lägga till Kanbanize från galleriet

Om du vill konfigurera integreringen av Kanbanize i Azure AD måste du lägga till Kanbanize från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Kanbanize** i sökrutan.
1. Välj **Kanbanize** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD SSO med Kanbanize med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Kanbanize.

Om du vill konfigurera och testa Azure AD SSO med Kanbanize, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera KANBANIZE SSO](#configure-kanbanize-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testuser-för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa Kanbanize test User](#create-kanbanize-test-user)** -om du vill ha en motsvarighet till B. Simon i Kanbanize som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Kanbanize** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<subdomain>.kanbanize.com/`

    b. Skriv en URL med följande mönster i textrutan **Svars-URL**: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Klicka på **Ange ytterligare URL:er**.

    d. I text rutan för **relä tillstånd** anger du en URL:`/ctrl_login/saml_login`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Kanbanize client support team](mailto:support@ms.kanbanize.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Kanbanize-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärm bild visar en lista över standardattribut, där as NameIdentifier mappas med **User. UserPrincipalName**. Kanbanize-programmet förväntar sig att NameIdentifier mappas med **User. mail**, så du måste redigera mappningen av attribut genom att klicka på ikonen Redigera och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. I avsnittet **Konfigurera Kanbanize** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-kanbanize-sso"></a>Konfigurera Kanbanize SSO

1. Logga in på Kanbanize som säkerhets administratör i ett annat webbläsarfönster.

2. Gå till överst till höger på sidan, klicka på **Inställningar** logo typ.

    ![Kanbanize-inställningar](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

3. På sidan administrations panel på den vänstra sidan av menyn klickar du på **integration** och aktiverar sedan **enkel inloggning**.

    ![Kanbanize-integreringar](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

4. Under avsnittet integrations klickar du på **Konfigurera** för att öppna integrations sidan för **enkel inloggning** .

    ![Kanbanize-konfiguration](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

5. Utför följande steg på integrations sidan för **enkel inloggning** under **konfigurationer**:

    ![Kanbanize-integreringar](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. I text rutan för **entitets-ID för IDP** klistrar du in värdet för **Azure AD**-identifieraren, som du har kopierat från Azure Portal.

    b. I text rutan **IDP-inloggnings slut punkt** klistrar du in värdet för inloggnings- **URL: en**som du har kopierat från Azure Portal.

    c. I text rutan IDP-utloggnings **slut punkt** klistrar du in värdet för utloggnings- **URL**, som du har kopierat från Azure Portal.

    d. Ange det här värdet i rutan **attributnamn för e-post** .`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Ange det här värdet i text rutan **attribut namn för förnamn**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Ange det här värdet i text rutan **attribut namn för efter namn**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Du kan hämta dessa värden genom att kombinera namn rymder och namn värden för respektive attribut från avsnittet användarattribut i Azure Portal.

    g. I anteckningar öppnar du det bas-64-kodade certifikatet som du laddade ned från Azure Portal, kopierar dess innehåll (utan start-och slut markeringar) och klistrar in det i rutan **IDP X. 509** .

    h. Markera **Aktivera inloggning med både SSO och Kanbanize**.

    i. Klicka på **Spara inställningar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Kanbanize.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Kanbanize**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-kanbanize-test-user"></a>Skapa Kanbanize test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Kanbanize. Kanbanize stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Kanbanize skapas en ny efter autentiseringen. Om du behöver skapa en användare manuellt kontaktar du [Kanbanize client support team](mailto:support@ms.kanbanize.com).

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Kanbanize på åtkomst panelen, bör du loggas in automatiskt på den Kanbanize som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

