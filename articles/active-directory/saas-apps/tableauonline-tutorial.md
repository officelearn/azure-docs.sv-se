---
title: 'Självstudier: Azure Active Directory-integrering med Tableau Online | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.openlocfilehash: 352ad9473a1c1a9360ddceb720ff968f4e97e012
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64876647"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Självstudier: Azure Active Directory-integrering med Tableau Online

I den här självstudien får du lära dig hur du integrerar Tableau Online med Azure Active Directory (AD Azure).
Integrera Tableau Online med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Tableau Online.
* Du kan aktivera användarna att vara automatiskt inloggad till Tableau Online (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Tableau Online, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Tableau Online enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Tableau Online stöder **SP** -initierad SSO

## <a name="adding-tableau-online-from-the-gallery"></a>Att lägga till Tableau Online från galleriet

För att konfigurera integrering av Tableau Online i Azure AD, som du behöver lägga till Tableau Online från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Tableau Online från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Tableau Online**väljer **Tableau Online** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Tableau Online i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Tableau Online baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Tableau Online upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Tableau Online, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Tableau Online enkel inloggning](#configure-tableau-online-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Tableau Online testanvändare](#create-tableau-online-test-user)**  – du har en motsvarighet för Britta Simon i Tableau Online som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Tableau Online:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Tableau Online** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Tableau Online domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I rutan **Inloggnings-URL** anger du följande URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. I textrutan **Identifierare (entitets-ID)** anger du URL:en: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Du får den `<entityid>` värdet från den **konfigurera Tableau Online** avsnitt i den här självstudien. ID-värdet entitet kommer att **Azure AD-identifierare** värde i **konfigurera Tableau Online** avsnittet.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera Tableau Online** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-tableau-online-single-sign-on"></a>Konfigurera Tableau Online enkel inloggning

1. I ett annat webbläsarfönster inloggning till programmets Tableau Online. Gå till **inställningar** och sedan **autentisering**.

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Aktivera SAML, Under **autentiseringstyper** avsnittet. Kontrollera **aktivera ytterligare en autentiseringsmetod** och kontrollera **SAML** kryssrutan.

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Rulla nedåt upp till **importfilen metadata i Tableau Online** avsnittet.  Klicka på Bläddra och importera metadatafilen, som du har laddat ned från Azure AD. Klicka sedan på **tillämpa**.

   ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. I den **matchar intyg** avsnittet, infoga motsvarande identitetsprovider assertion namn för **e-postadress**, **Förnamn**, och **efternamn**. Hämta den här informationen från Azure AD: 
  
    a. I Azure-portalen går du vidare den **Tableau Online** integrering programsidan.

    b. I den **användarattribut och anspråk** klickar du på redigeringsikonen.

   ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/attributesection.png)

    c. Kopiera namnområdesvärdet för dessa attribut: givenname, e-post- och efternamn med hjälp av följande steg:

   ![Azure AD enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Klicka på **user.givenname** värde

    e. Kopiera värdet från den **Namespace** textrutan.

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/attributesection2.png)

    f. Värden för e-post och efternamn upprepar du ovanstående steg om du vill kopiera namnområdet.

    g. Växla till programmet Tableau Online och ange sedan den **användarattribut och anspråk** avsnittet på följande sätt:

    * E-postadress: **e** eller **userprincipalname**

    * Förnamn: **givenname**

    * Efternamn: **efternamn**

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

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
    Till exempel BrittaSimon\@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Tableau Online.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Tableau Online**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Tableau Online**.

    ![Länken Tableau Online i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-tableau-online-test-user"></a>Skapa Tableau Online testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Tableau Online.

1. På **Tableau Online**, klickar du på **inställningar** och sedan **autentisering** avsnittet. Rulla ned till **hantera användare** avsnittet. Klicka på **Lägg till användare** och klicka sedan på **ange e-postadresser**.
  
    ![Skapa en Azure AD-användare för testning](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Välj **lägga till användare för autentisering (SAML)**. I den **RETUR e-postadresser** textrutan Lägg till britta.simon\@contoso.com
  
    ![Skapa en Azure AD-användare för testning](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Klicka på **lägga till användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Tableau Online i åtkomstpanelen, bör det vara loggas in automatiskt i Tableau online som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
