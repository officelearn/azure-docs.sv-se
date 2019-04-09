---
title: 'Självstudier: Azure Active Directory-integrering med Syncplicity | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 041f1e9706c7d815dad1a33104e7dd15b2cc3893
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270242"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Självstudier: Azure Active Directory-integrering med Syncplicity

I den här självstudien får du lära dig hur du integrerar Syncplicity med Azure Active Directory (AD Azure).
Integrera Syncplicity med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Syncplicity.
* Du kan aktivera användarna att vara automatiskt inloggad till Syncplicity (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Syncplicity, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Syncplicity enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Syncplicity **SP** -initierad SSO

## <a name="adding-syncplicity-from-the-gallery"></a>Att lägga till Syncplicity från galleriet

För att konfigurera integrering av Syncplicity i Azure AD, som du behöver lägga till Syncplicity från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Syncplicity från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Syncplicity**väljer **Syncplicity** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Syncplicity i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Syncplicity baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Syncplicity upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Syncplicity, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Syncplicity Single Sign-On](#configure-syncplicity-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Syncplicity](#create-syncplicity-test-user)**  – du har en motsvarighet för Britta Simon i Syncplicity som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Syncplicity:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Syncplicity** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Syncplicity domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL enligt följande mönster: `https://<companyname>.syncplicity.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Syncplicity klienten supportteamet](https://www.syncplicity.com/contact-us) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Syncplicity** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-syncplicity-single-sign-on"></a>Konfigurera Syncplicity Single Sign-On

1. Logga in på din **Syncplicity** klient.

1. Klicka på menyn längst upp **admin**väljer **inställningar**, och klicka sedan på **anpassad domän och enkel inloggning**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. På den **enkel inloggning (SSO)** dialogrutan utför följande steg:

    ![Enkel inloggning \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. I den **Custom Domain** textrutan skriver du namnet på din domän.
  
    b. Välj **aktiverat** som **enkel inloggningsstatus**.

    c. I den **entitets-Id** textrutan klistra in värdet för **Azure Ad-identifierare** som du har kopierat från Azure-portalen.

    d. I den **inloggning sid-URL** textrutan klistra in den **inloggnings-URL** som du har kopierat från Azure-portalen.

    e. I den **sid-URL för utloggning** textrutan klistra in den **URL för utloggning** som du har kopierat från Azure-portalen.

    f. I **providern identitetscertifikat**, klickar du på **Välj fil**, och sedan ladda upp det certifikat som du har hämtat från Azure-portalen.

    g. Klicka på **SPARA ÄNDRINGAR** för att spara ändringarna.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Syncplicity.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Syncplicity**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Syncplicity**.

    ![Länken Syncplicity i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.
    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-syncplicity-test-user"></a>Skapa Syncplicity testanvändare

För AAD-användare för att kunna logga in, måste de etableras till Syncplicity program. Det här avsnittet beskrivs hur du skapar AAD-användarkonton i Syncplicity.

**Om du vill konfigurera ett användarkonto till Syncplicity, utför du följande steg:**

1. Logga in på din **Syncplicity** klient (till exempel: `https://company.Syncplicity.com`).

1. Klicka på **admin** och välj **användarkonton** och klicka sedan på **ADD A USER**.

    ![Hantera användare](./media/syncplicity-tutorial/ic769764.png "Hantera användare")

1. Typen i **e-postadresser** ett Azure AD-konto som du vill etablera, väljer du **användaren** som **rollen**, och klicka sedan på **nästa**.

    ![Kontoinformation](./media/syncplicity-tutorial/ic769765.png "kontoinformation")

    > [!NOTE]
    > Kontoinnehavare för AAD får ett e-postmeddelande, inklusive en länk för att bekräfta och aktivera kontot.

1. Välj en grupp i ditt företag som de nya användarna ska bli medlem i och klicka sedan på **nästa**.

    ![Gruppmedlemskap](./media/syncplicity-tutorial/ic769772.png "gruppmedlemskap")

    > [!NOTE]
    > Om det finns inga grupper som visas, klickar du på **nästa**.

1. Markera de mappar som du vill placera under Syncplicitys kontroll på användarens dator och klicka sedan på **nästa**.

    ![Syncplicity mappar](./media/syncplicity-tutorial/ic769773.png "Syncplicity mappar")

> [!NOTE]
> Du kan använda alla andra Syncplicity användare konto verktyg för att skapa eller API: er som tillhandahålls av Syncplicity att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Syncplicity i åtkomstpanelen, bör det vara loggas in automatiskt till Syncplicity som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
