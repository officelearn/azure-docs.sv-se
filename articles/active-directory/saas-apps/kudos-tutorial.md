---
title: 'Självstudier: Azure Active Directory-integrering med bra jobbat av | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bra jobbat av.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 00492ba379a4b4d8ae7437d18f9b2880dae03e44
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917456"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Självstudier: Azure Active Directory-integrering med bra jobbat av

I den här självstudien får du lära dig hur du integrerar Bra jobbat med Azure Active Directory (AD Azure).
Bra jobbat av integrerar med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har tillgång till Bra jobbat av.
* Du kan aktivera användarna att vara automatiskt inloggad till Bra jobbat av (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med bra jobbat av, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Bra jobbat av enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Bra jobbat av stöder **SP** -initierad SSO

## <a name="adding-kudos-from-the-gallery"></a>Bra jobbat av för att lägga till från galleriet

För att konfigurera integrering av Bra jobbat av i Azure AD, som du behöver lägga till Bra jobbat av från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Bra jobbat av från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Bra jobbat av**väljer **Bra jobbat av** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Bra jobbat av i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med bra jobbat av baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Bra jobbat av upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med bra jobbat av, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Bra jobbat av enkel inloggning](#configure-kudos-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Bra jobbat av testanvändare](#create-kudos-test-user)**  – du har en motsvarighet för Britta Simon i Bra jobbat av som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med bra jobbat av:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Bra jobbat av** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Bra jobbat av domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<company>.kudosnow.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Bra jobbat av klienten supportteamet](http://success.kudosnow.com/home) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Bra jobbat av** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-kudos-single-sign-on"></a>Konfigurera Bra jobbat av enkel inloggning

1. Logga in på webbplatsen Bra jobbat av företag som en administratör i ett annat webbläsarfönster.

1. Klicka på menyn längst upp **inställningsikonen**.

    ![Inställningar](./media/kudos-tutorial/ic787806.png "Inställningar")

1. Klicka på **integreringar > SSO** och utför följande steg:

    ![SSO](./media/kudos-tutorial/ic787807.png "SSO")

    a. I **inloggnings-URL** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    b. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textrutan

    c. I **URL för utloggning till** textrutan klistra in värdet för **URL för utloggning** som du har kopierat från Azure-portalen.

    d. I den **din Bra jobbat av URL** textrutan skriver du namnet på ditt företag.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Bra jobbat av.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Bra jobbat av**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Bra jobbat av**.

    ![Bra jobbat av länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-kudos-test-user"></a>Skapa Bra jobbat av testanvändare

För att aktivera Azure AD-användare att logga in på Bra jobbat av etableras de i Bra jobbat av. När det gäller Bra jobbat av är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **Bra jobbat av** företagets plats som administratör.

1. Klicka på menyn längst upp **inställningsikonen**.

   ![Inställningar](./media/kudos-tutorial/ic787806.png "Inställningar")

1. Klicka på **Användaradministration**.

1. Klicka på den **användare** fliken och klicka sedan på **lägga till en användare**.

   ![Användaradministration](./media/kudos-tutorial/ic787809.png "Användaradministration")

1. I den **lägga till en användare** avsnittet, utför följande steg:

    ![Lägga till en användare](./media/kudos-tutorial/ic787810.png "lägga till en användare")

    a. Skriv den **Förnamn**, **efternamn**, **e-post** och annan information om ett giltigt Azure Active Directory-konto som du vill etablera till relaterade textrutor.

    b. Klicka på **Skapa användare**.

> [!NOTE]
> Du kan använda alla andra Bra jobbat av användarens konto verktyg för att skapa eller API: er som tillhandahålls av Bra jobbat av att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Bra jobbat av åtkomstpanelen bör det vara loggas in automatiskt till belöningar som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
