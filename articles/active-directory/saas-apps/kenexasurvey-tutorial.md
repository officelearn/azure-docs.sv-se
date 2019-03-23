---
title: 'Självstudier: Azure Active Directory-integrering med IBM Kenexa undersökningen Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IBM Kenexa undersökningen Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: ed34267aa1f18b4c66fe841164e6a2cde4e27d47
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361055"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Självstudier: Azure Active Directory-integrering med IBM Kenexa undersökningen Enterprise

Lär dig hur du integrerar IBM Kenexa undersökningen Enterprise med Azure Active Directory (AD Azure) i den här självstudien.
Integrera IBM Kenexa undersökningen Enterprise med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till IBM Kenexa undersökningen Enterprise.
* Du kan aktivera användarna att vara automatiskt inloggad till IBM Kenexa undersökningen Enterprise (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med IBM Kenexa undersökningen Enterprise, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* IBM Kenexa undersökningen Enterprise enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för IBM Kenexa undersökningen Enterprise **IDP** -initierad SSO

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Att lägga till IBM Kenexa undersökningen Enterprise från galleriet

Om du vill konfigurera integreringen av IBM Kenexa undersökningen Enterprise till Azure AD, som du behöver lägga till IBM Kenexa undersökningen Enterprise från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till IBM Kenexa undersökningen Enterprise från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **IBM Kenexa undersökningen Enterprise**väljer **IBM Kenexa undersökningen Enterprise** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![IBM Kenexa undersökningen Enterprise i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med IBM Kenexa undersökningen Enterprise baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i IBM Kenexa undersökningen Enterprise upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med IBM Kenexa undersökningen Enterprise, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera IBM Kenexa undersökningen Enterprise enkel inloggning](#configure-ibm-kenexa-survey-enterprise-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare för IBM Kenexa undersökningen Enterprise](#create-ibm-kenexa-survey-enterprise-test-user)**  – du har en motsvarighet för Britta Simon i IBM Kenexa undersökningen företag som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med IBM Kenexa undersökningen Enterprise:

1. I den [Azure-portalen](https://portal.azure.com/)på den **IBM Kenexa undersökningen Enterprise** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![IBM Kenexa undersökningen Enterprise domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://surveys.kenexa.com/<companycode>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [IBM Kenexa undersökningen Enterprise Client supportteamet](https://www.ibm.com/support/home/?lnk=fcw) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. IBM Kenexa undersökningen Enterprise-programmet förväntas ta emot Security intyg Markup Language (SAML) intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar konfigurationen av SAML-tokenattribut. Värdet för användar-ID-anspråk i svaret måste matcha SSO-ID som har konfigurerats i Kenexa-system. Om du vill mappa lämplig användaridentifieraren i din organisation som SSO Internet Datagram Protocol (IDP) som fungerar med den [IBM Kenexa undersökningen Enterprise support-teamet](https://www.ibm.com/support/home/?lnk=fcw).

    Som standard Anger användar-ID som värde för användarens huvudnamn (UPN) i Azure AD. Du kan ändra värdet på den **användarattribut** fliken enligt följande skärmbild. Integrationen fungerar bara när du har slutfört mappningen korrekt.

    ![image](common/edit-attribute.png)

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. På den **konfigurera IBM Kenexa undersökningen Enterprise** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Konfigurera IBM Kenexa undersökningen Enterprise enkel inloggning

Att konfigurera enkel inloggning på **IBM Kenexa undersökningen Enterprise** sida, som du behöver skicka de hämtade **certifikat (Base64)** och lämpliga kopierade URL: er från Azure portal för att [IBM Kenexa Enkät Enterprise support-teamet](https://www.ibm.com/support/home/?lnk=fcw). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till IBM Kenexa undersökningen Enterprise.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **IBM Kenexa undersökningen Enterprise**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **IBM Kenexa undersökningen Enterprise**.

    ![IBM Kenexa undersökningen Enterprise-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>Skapa IBM Kenexa undersökningen Enterprise testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i IBM Kenexa undersökningen företag.

För att skapa användare i systemet för IBM Kenexa undersökningen Enterprise och mappa SSO-ID för dem, kan du arbeta med den [IBM Kenexa undersökningen Enterprise support-teamet](https://www.ibm.com/support/home/?lnk=fcw). Det här SSO-ID-värdet bör också mappas till användar-ID-värde från Azure AD. Du kan ändra denna standardinställning på den **attributet** fliken.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen IBM Kenexa undersökningen Enterprise i åtkomstpanelen, bör det vara loggas in automatiskt till IBM Kenexa undersökningen Enterprise som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

