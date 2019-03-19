---
title: 'Självstudier: Azure Active Directory-integrering med Innoverse | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Innoverse.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d72e4da0-0123-409b-96c2-e613f3f83fb1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdaaf29e5649c6b82606b1e332fea56cabfc9aaa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57861748"
---
# <a name="tutorial-azure-active-directory-integration-with-innoverse"></a>Självstudier: Azure Active Directory-integrering med Innoverse

I den här självstudien lär du dig att integrera Innoverse med Azure Active Directory (AD Azure).
Genom att integrera Innoverse med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Innoverse.
* Du kan göra så att dina användare automatiskt loggas in på Innoverse (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande saker för att konfigurera Azure AD-integrering med Innoverse:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Innoverse-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Innoverse har stöd för **SP- och IDP**-initierad enkel inloggning
* Innoverse stöder **just-in-time**-användaretablering

## <a name="adding-innoverse-from-the-gallery"></a>Lägga till Innoverse från galleriet

För att konfigurera integrering av Innoverse med Azure AD behöver du lägga till Innoverse från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Innoverse från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select_azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise_applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add_new_app.png)

4. I sökrutan skriver du **Innoverse**, väljer **Innoverse** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Innoverse i resultatlistan](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Innoverse baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Innoverse upprättas.

Du behöver slutföra följande byggstenar för att konfigurera och testa enkel inloggning med Azure AD för Innoverse:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Innoverse](#configure-innoverse-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Innoverse-testanvändare](#create-innoverse-test-user)** – för att ha en motsvarighet till Britta Simon i Innoverse som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Innoverse:

1. På [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Innoverse** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select_sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select_saml_option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit_urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Innoverse-domän och information om URL:er för enkel inloggning](common/idp_intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<domainname>.innover.se`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<domainname>.innover.se/auth/saml2/login`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Innoverse-domän och information om URL:er för enkel inloggning](common/metadata_upload_additional_signon.png)

    I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<domainname>.innover.se/auth/saml2/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [supportteamet för Innoverse-klienten ](mailto:support@readify.net) för att hämta de här värdena. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Innoverse-programmet förväntar sig SAML-intyget i ett särskilt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](./media/innovationhub-tutorial/tutorial-innovationhub-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Namn | Källattribut| Namnrymd |
    | ---------------| --------- | ----------------|
    | displayname (visningsnamn) | `user.userprincipalname` | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new_save_attribute.png)

    ![image](common/new_attribute_details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Ange **Namnrymd**.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

8. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på ikonen **kopiera** för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy_metadataurl.png)

### <a name="configure-innoverse-single-sign-on"></a>Konfigurera enkel inloggning för Innoverse

För att konfigurera enkel inloggning på **Innoverse**-sidan behöver du skicka den kopierade **Federation Metadata-URL:en** till [Innoverse-supportteamet](mailto:support@readify.net). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new_user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user_properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.

    b. I den **användarnamn** skriver **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Innoverse.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Innoverse**.

    ![Bladet Företagsprogram](common/enterprise_applications.png)

2. I listan över program skriver och väljer du **Innoverse**.

    ![Innoverse-länken i programlistan](common/all_applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users_groups_blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add_assign_user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-innoverse-test-user"></a>Skapa Innoverse-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Innoverse. Innoverse stöder **just-in-time-etablering**, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Innoverse skapas en ny när du försöker komma åt Innoverse.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Innoverse-panelen i åtkomstpanelen bör du automatiskt loggas in på Innoverse som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
