---
title: 'Självstudier: Azure Active Directory-integrering med Silverback | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 1a104da9ecb28d2109e82056995ef7a8048eafe2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838756"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Självstudier: Azure Active Directory-integrering med Silverback

I den här självstudien får du lära dig hur du integrerar Silverback med Azure Active Directory (AD Azure).
Integrera Silverback med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Silverback.
* Du kan aktivera användarna att vara automatiskt inloggad till Silverback (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Silverback, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Silverback enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Silverback **SP** -initierad SSO

## <a name="adding-silverback-from-the-gallery"></a>Att lägga till Silverback från galleriet

För att konfigurera integrering av Silverback i Azure AD, som du behöver lägga till Silverback från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Silverback från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Silverback**väljer **Silverback** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Silverback i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Silverback baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Silverback upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Silverback, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Silverback Single Sign-On](#configure-silverback-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Silverback testanvändare](#create-silverback-test-user)**  – du har en motsvarighet för Britta Simon i Silverback som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Silverback:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Silverback** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Silverback domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<YOURSILVERBACKURL>.com/ssp`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `<YOURSILVERBACKURL>.com`

    c. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Silverback klienten supportteamet](mailto:helpdesk@matrix42.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Konfigurera Silverback enkel inloggning

1. I en annan webbläsare, logga in på Silverback servern som administratör.

2. Gå till **Admin** > **autentiseringsprovider**.

3. På den **autentiseringsinställningar för providern** utför följande steg:

    ![Administratören](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  Klicka på **importera från URL: en**.

    b.  Klistra in den kopierade Metadata-URL och klicka på **OK**.

    c.  Bekräfta med **OK** och sedan värden fylls i automatiskt.

    d.  Aktivera **visas på inloggningssidan**.

    e.  Aktivera **dynamisk Användargenereringen** om du vill lägga till av Azure AD som är auktoriserade användare automatiskt (valfritt).

    f.  Skapa en **rubrik** för knappen på självbetjäningsportalen.

    g.  Ladda upp en **ikonen** genom att klicka på **Välj fil**.

    h.  Välj bakgrunden **färg** för knappen.

    i.  Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Silverback.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Silverback**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Silverback**.

    ![Länken Silverback i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-silverback-test-user"></a>Skapa Silverback testanvändare

Om du vill aktivera Azure AD-användare att logga in på Silverback, måste de etableras i Silverback. I Silverback är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Silverback servern som administratör.

2. Gå till **användare** och **lägga till en ny enhet användare**.

3. På den **grundläggande** utför följande steg:

    ![Användaren](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. I **användarnamn** text, ange namnet på användaren som **Britta**.

    b. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    c. I textrutan **Efternamn** anger du efternamnet på användaren som **Simon**.

    d. I **e-postadress** text, ange den e-postadressen för användaren som **Brittasimon@contoso.com**.

    e. I den **lösenord** text, ange ditt lösenord.

    f. I den **Bekräfta lösenord** textrutan, skriva in lösenordet igen och bekräfta.

    g. Klicka på **Spara**.

> [!NOTE]
> Om du inte vill skapa varje användare manuellt aktivera den **dynamisk Användargenereringen** kryssrutan under **Admin** > **autentiseringsprovider**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Silverback i åtkomstpanelen, bör det vara loggas in automatiskt till Silverback som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

