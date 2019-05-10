---
title: 'Självstudier: Azure Active Directory-integrering med Displayr | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9694790ea02bc778bf3b9db212e61fabb90a28a8
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441442"
---
# <a name="tutorial-azure-active-directory-integration-with-displayr"></a>Självstudier: Azure Active Directory-integrering med Displayr

I den här självstudien får du lära dig hur du integrerar Displayr med Azure Active Directory (AD Azure).
Integrera Displayr med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Displayr.
* Du kan aktivera användarna att vara automatiskt inloggad till Displayr (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Displayr, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Displayr enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Displayr **SP** -initierad SSO

## <a name="adding-displayr-from-the-gallery"></a>Att lägga till Displayr från galleriet

För att konfigurera integrering av Displayr i Azure AD, som du behöver lägga till Displayr från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Displayr från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Displayr**väljer **Displayr** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Displayr i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Displayr baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Displayr upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Displayr, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Displayr Single Sign-On](#configure-displayr-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Displayr](#create-displayr-test-user)**  – du har en motsvarighet för Britta Simon i Displayr som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Displayr:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Displayr** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Displayr domän och URL: er med enkel inloggning för information](common/sp-intiated.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://app.displayr.com/Login`

5. I den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned den **certifikat (Raw)** från de angivna alternativen enligt dina behov och spara den på din dator.

    ![Länk för hämtning av certifikat](common/certificateraw.png)

6. Displayr program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

7. Förutom ovanstående Displayr program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I den **användarattribut och anspråk** avsnittet på den **gruppanspråk (förhandsversion)** dialogrutan utför följande steg:

    a. Klicka på den **penna** bredvid **grupper returneras i anspråk**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Välj **alla grupper** från listan över radio.

    c. Välj **källattributet** av **grupp-ID:**.

    d. Kontrollera **har ändrat namnet på gruppanspråket**.

    e. Kontrollera **generera grupper som rollanspråk**.

    f. Klicka på **Spara**.

8. På den **konfiguration Displayr** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-displayr-single-sign-on"></a>Konfigurera Displayr Single Sign-On

1. I ett annat webbläsarfönster, loggar du in Displayr som administratör.

2. Klicka på **inställningar** gå sedan till **konto**.

    ![Konfiguration](./media/displayr-tutorial/config01.png)

3. Växla till **inställningar** från den översta menyn och rulla nedåt på sidan för att klicka på **Konfigurera enkel inloggning på (SAML)**.

    ![Konfiguration](./media/displayr-tutorial/config02.png)

4. På den **enkel inloggning på (SAML)** utför följande steg:

    ![Konfiguration](./media/displayr-tutorial/config03.png)

    a. Kontrollera den **aktivera enkel inloggning på (SAML)** box.

    b. I den **utfärdare** text rutan, klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen.

    c. I den **inloggnings-URL** text rutan, klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    d. I den **URL för utloggning** text rutan, klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

    e. Öppna certifikat (Raw) i anteckningar, kopiera innehållet och klistra in den i den **certifikat** textrutan.

    f. **Gruppera mappningar** är valfria.

    g. Klicka på **Spara**.  

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Displayr.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Displayr**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Displayr**.

    ![Länken Displayr i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-displayr-test-user"></a>Skapa Displayr testanvändare

Om du vill aktivera Azure AD-användare registrera i att Displayr, de måste etableras i Displayr. I Displayr är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Displayr som administratör.

2. Klicka på **inställningar** gå sedan till **konto**.

    ![Displayr konfiguration](./media/displayr-tutorial/config01.png)

3. Växla till **inställningar** från den översta menyn och rulla nedåt på sidan tills **användare** avsnittet och klicka sedan på **ny användare**.

    ![Displayr konfiguration](./media/displayr-tutorial/config07.png)

4. På den **ny användare** utför följande steg:

    ![Displayr konfiguration](./media/displayr-tutorial/config06.png)

    a. I **namn** text, ange namnet på användaren som **Brittasimon**.

    b. I **e-post** text, ange den e-postadressen för användaren som `Brittasimon@contoso.com`.

    c. Välj din lämpliga **gruppmedlemskap**.

    d. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Displayr i åtkomstpanelen, bör det vara loggas in automatiskt till Displayr som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

