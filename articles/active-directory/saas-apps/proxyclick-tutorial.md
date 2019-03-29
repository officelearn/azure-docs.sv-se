---
title: 'Självstudier: Azure Active Directory-integrering med Proxyclick | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d37e3cc56b4a80fce9dcae6f87ff626867496007
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578347"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Självstudier: Azure Active Directory-integrering med Proxyclick

I den här självstudien får du lära dig hur du integrerar Proxyclick med Azure Active Directory (AD Azure).
Integrera Proxyclick med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Proxyclick.
* Du kan aktivera användarna att vara automatiskt inloggad till Proxyclick (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Proxyclick, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Proxyclick enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Proxyclick **SP** och **IDP** -initierad SSO

## <a name="adding-proxyclick-from-the-gallery"></a>Att lägga till Proxyclick från galleriet

För att konfigurera integrering av Proxyclick i Azure AD, som du behöver lägga till Proxyclick från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Proxyclick från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Proxyclick**väljer **Proxyclick** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Proxyclick i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Proxyclick baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Proxyclick upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Proxyclick, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Proxyclick Single Sign-On](#configure-proxyclick-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Proxyclick](#create-proxyclick-test-user)**  – du har en motsvarighet för Britta Simon i Proxyclick som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Proxyclick:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Proxyclick** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Proxyclick domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://saml.proxyclick.com/init/<companyId>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://saml.proxyclick.com/consume/<companyId>`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Proxyclick domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Dessa värden uppdateras med de faktiska identifierare, svars-URL och inloggnings-URL, vilket beskrivs senare i självstudien.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. På den **konfigurera Proxyclick** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurera Proxyclick Single Sign-On

1. I ett annat webbläsarfönster, loggar du in din Proxyclick företagets webbplats som administratör.

2. Välj **konto och inställningar**.

    ![Proxyclick konfiguration](./media/proxyclick-tutorial/configure1.png)

3. Rulla ned till den **INTEGRERINGAR** och välj **SAML**.

    ![Proxyclick konfiguration](./media/proxyclick-tutorial/configure2.png)

4. I den **SAML** avsnittet, utför följande steg:

    ![Proxyclick konfiguration](./media/proxyclick-tutorial/configure3.png)

    a. Kopiera **URL för SAML-konsument** värde och klistra in den i **svars-URL** -textrutan i **SAML grundkonfiguration** avsnittet på Azure-portalen.

    b. Kopiera **omdirigerings-URL för SAML SSO** värde och klistra in den i **inloggnings-URL** och **identifierare** textrutor i **SAML grundkonfiguration** avsnittet på Azure-portalen.

    c. Välj **metod för SAML-begäran** som **omdirigering för HTTP**.

    d. I den **utfärdare** textrutan klistra in värdet för **Azure AD-identifierare** värde, som du har kopierat från Azure-portalen.

    e. I den **slutpunkts-URL för SAML 2.0** textrutan klistra in värdet för **inloggnings-URL** kopieras från Azure-portalen.

    f. Öppna filen nedladdade certifikatet från Azure-portalen i anteckningar och klistra in den i den **certifikat** textrutan.

    g. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Proxyclick.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Proxyclick**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Proxyclick**.

    ![Länken Proxyclick i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-proxyclick-test-user"></a>Skapa Proxyclick testanvändare

Om du vill aktivera Azure AD-användare att logga in på Proxyclick, måste de etableras i Proxyclick. När det gäller Proxyclick är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Proxyclick företag som administratör.

1. Klicka på **kollegor** från det övre navigeringsfältet.

    ![Lägga till medarbetare](./media/proxyclick-tutorial/user1.png)

1. Klicka på **lägga till en kollega**

    ![Lägga till medarbetare](./media/proxyclick-tutorial/user2.png)

1. I den **lägga till en kollega** avsnittet, utför följande steg:

    ![Lägga till medarbetare](./media/proxyclick-tutorial/user3.png)

    a. I textrutan **E-post** skriver du e-postadressen för användaren: brittasimon@contoso.com.

    b. I textrutan **Förnamn** skriver du förnamnet på användaren som Britta.

    c. I textrutan **Efternamn** skriver du efternamnet som Simon.

    d. Klicka på **Lägg till användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Proxyclick i åtkomstpanelen, bör det vara loggas in automatiskt till Proxyclick som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

