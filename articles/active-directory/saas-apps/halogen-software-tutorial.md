---
title: 'Självstudier: Azure Active Directory-integrering med Halogen Software | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Halogen Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7925e6fe66579c8bf702acbe165c50d2aed536e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60276792"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Självstudier: Azure Active Directory-integrering med Halogen Software

I den här självstudien lär du dig att integrera Halogen Software med Azure Active Directory (AD Azure).
Integreringen av Halogen Software med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Halogen Software.
* Du kan göra så att dina användare loggas in automatiskt på Halogen Software (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Halogen Software behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Halogen Software-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Halogen Software har stöd för **SP**-initierad enkel inloggning

## <a name="adding-halogen-software-from-the-gallery"></a>Lägga till Halogen Software från galleriet

För att konfigurera integreringen av Halogen Software i Azure AD måste du lägga till Halogen Software från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Halogen Software från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Halogen Software**, väljer **Halogen Software** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Halogen Software i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Halogen Software baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Halogen Software upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Halogen Software behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Halogen Software](#configure-halogen-software-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Halogen Software-testanvändare](#create-halogen-software-test-user)** – för att ha en motsvarighet för Britta Simon i Halogen Software som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Halogen Software:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Halogen Software** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Halogen Software-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://global.hgncloud.com/<companyname>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster:

    | |
    |--|
    | `https://global.halogensoftware.com/<companyname>`|
    | `https://global.hgncloud.com/<companyname>`|
    | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för Halogen Software](https://support.halogensoftware.com/) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Halogen Software** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-halogen-software-single-sign-on"></a>Konfigurera enkel inloggning för Halogen Software

1. I ett annat webbläsarfönster loggar du in på **Halogen Software**-programmet som administratör.

2. Klicka på fliken **Alternativ**.
  
    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_12.png)

3. I det vänstra navigeringsfönstret klickar du på **SAML-konfiguration**.
  
    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_13.png)

4. I dialogrutan **SAML-konfiguration** utför du följande steg:

    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_14.png)

    a. För **Unik identifierare** väljer du **NameID**.

    b. För **Unique Identifier Maps To** (Unik identifierare mappar till) väljer du **Användarnamn**.
  
    c. Om du vill ladda upp din nedladdade metadatafil klickar du på **Bläddra** för att välja filen och sedan på **Ladda upp fil**.

    d. Testa konfigurationen genom att klicka på **Kör test**.

    > [!NOTE]
    > Du måste vänta på meddelandet ”*The SAML test is complete. Please close this window*” (SAML-testet är klart. Stäng det här fönstret). Stäng sedan det öppnade webbläsarfönstret. Kryssrutan **Aktivera SAML** är bara tillgänglig om testet har slutförts.

    e. Välj **Aktivera SAML**.

    f. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Halogen Software.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Halogen Software**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Halogen Software**.

    ![Länken för Halogen Software i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-halogen-software-test-user"></a>Skapa Halogen Software-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i Halogen Software.

**Skapa en användare med namnet Britta Simon i Halogen Software genom att utföra följande steg:**

1. Logga in på **Halogen Software**-programmet som administratör.

2. Klicka på fliken **User Center** (Användarcenter) och sedan på **Skapa användare**.

    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_300.png)  

3. I dialogrutan **Ny användare** utför du följande steg:

    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial_halogen_301.png)

    a. I textrutan **Förnamn** skriver du förnamnet på användaren: **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.

    c. I textrutan **Användarnamn** skriver du **Britta Simon**, användarnamnet som i Azure-portalen.

    d. I textrutan **Lösenord** skriver du ett lösenord för Britta.

    e. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Halogen Software-panelen i åtkomstpanelen bör du automatiskt loggas in på Halogen Software som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)