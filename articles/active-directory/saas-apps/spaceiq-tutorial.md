---
title: 'Självstudier: Azure Active Directory-integrering med SpaceIQ | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: de2b60ef280ceebdc97ea7dfc208df1cea3956c0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699887"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Självstudier: Azure Active Directory-integrering med SpaceIQ

I den här självstudien får du lära dig hur du integrerar SpaceIQ med Azure Active Directory (AD Azure).
Integrera SpaceIQ med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SpaceIQ.
* Du kan aktivera användarna att vara automatiskt inloggad till SpaceIQ (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med SpaceIQ, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* SpaceIQ enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för SpaceIQ **IDP** -initierad SSO

## <a name="adding-spaceiq-from-the-gallery"></a>Att lägga till SpaceIQ från galleriet

För att konfigurera integrering av SpaceIQ i Azure AD, som du behöver lägga till SpaceIQ från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SpaceIQ från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **SpaceIQ**väljer **SpaceIQ** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![SpaceIQ i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SpaceIQ baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SpaceIQ upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SpaceIQ, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SpaceIQ Single Sign-On](#configure-spaceiq-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare SpaceIQ](#create-spaceiq-test-user)**  – du har en motsvarighet för Britta Simon i SpaceIQ som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med SpaceIQ:

1. I den [Azure-portalen](https://portal.azure.com/)på den **SpaceIQ** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![SpaceIQ domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du URL:en: `https://api.spaceiq.com`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE]
    > Uppdatera dessa värden med de faktiska svars-URL och identifierare som beskrivs senare i självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera SpaceIQ** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-spaceiq-single-sign-on"></a>Konfigurera SpaceIQ Single Sign-On

1. Öppna ett nytt webbläsarfönster och logga sedan in på din SpaceIQ miljö som administratör.

1. När du är inloggad, klicka på tecknet pusselbiten längst upp till höger och sedan klicka på **integreringar**

    ![Kontoinställningar](./media/spaceiq-tutorial/setting1.png) 

1. Under **alla etablering och SSO**, klicka på den **Azure** panelen för att lägga till en instans av Azure som IDP: N.

    ![SAML-ikon](./media/spaceiq-tutorial/setting2.png)

1. I den **SSO** dialogrutan utför följande steg:

    ![Inställningar för SAML-autentisering](./media/spaceiq-tutorial/setting3.png)

    a. I den **utfärdar-URL för SAML** rutan, klistra in den **Azure AD-identifierare** värdet kopieras från konfigurationsfönstret för Azure AD-program.

    b. Kopiera den **SAML återanrop slutpunkts-URL (skrivskyddad)** och klistra in värdet i den **svars-URL** rutan den **SAML grundkonfiguration** avsnitt i Azure-portalen.

    c. Kopiera den **SAML Målgrupps-URI (skrivskyddad)** och klistra in värdet i den **identifierare** rutan den **SAML grundkonfiguration** avsnitt i Azure-portalen.

    d. Öppna filen nedladdade certifikatet i anteckningar, kopiera innehållet och klistra in den i den **X.509-certifikat** box.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SpaceIQ.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **SpaceIQ**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **SpaceIQ**.

    ![Länken SpaceIQ i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-spaceiq-test-user"></a>Skapa SpaceIQ testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i SpaceIQ. Arbete [SpaceIQ supportteamet](mailto:eng@spaceiq.com) att lägga till användare i SpaceIQ-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SpaceIQ i åtkomstpanelen, bör det vara loggas in automatiskt till SpaceIQ som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

