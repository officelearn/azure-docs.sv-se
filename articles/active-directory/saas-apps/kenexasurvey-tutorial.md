---
title: 'Självstudiekurs: Azure Active Directory-integrering med IBM Kenexa Survey Enterprise | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IBM Kenexa Survey Enterprise.
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
ms.openlocfilehash: c649b966b3e210f6b026b06a9654761e0f97aea1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099063"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Självstudiekurs: Azure Active Directory-integrering med IBM Kenexa Survey Enterprise

I den här självstudien får du lära dig hur du integrerar IBM Kenexa Survey Enterprise med Azure Active Directory (Azure AD).
Genom att integrera IBM Kenexa Survey Enterprise med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till IBM Kenexa Survey Enterprise.
* Du kan aktivera dina användare automatiskt inloggade på IBM Kenexa Survey Enterprise (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med IBM Kenexa Survey Enterprise behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* IBM Kenexa Survey Enterprise enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* IBM Kenexa Survey Enterprise stöder **IDP** initierad SSO

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Lägga till IBM Kenexa Survey Enterprise från galleriet

Om du vill konfigurera integreringen av IBM Kenexa Survey Enterprise i Azure AD måste du lägga till IBM Kenexa Survey Enterprise från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till IBM Kenexa Survey Enterprise från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **IBM Kenexa Survey Enterprise**i sökrutan och välj IBM **Kenexa Survey Enterprise** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![IBM Kenexa Survey Enterprise i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med IBM Kenexa Survey Enterprise baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i IBM Kenexa Survey Enterprise upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med IBM Kenexa Survey Enterprise måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera IBM Kenexa Survey Enterprise Single Sign-On](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa IBM Kenexa Survey Enterprise testanvändare](#create-ibm-kenexa-survey-enterprise-test-user)** - att ha en motsvarighet till Britta Simon i IBM Kenexa Survey Enterprise som är kopplad till Azure AD representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med IBM Kenexa Survey Enterprise:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på ibm **Kenexa Survey** **Enterprise-programintegrationssidan**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![IBM Kenexa Survey Enterprise Domain och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://surveys.kenexa.com/<companycode>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [IBM Kenexa Survey Enterprise Client supportteam](https://www.ibm.com/support/home/?lnk=fcw) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. IBM Kenexa Survey Enterprise-programmet förväntar sig att få SAML-påståenden (Security Assertions Markup Language) i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av dina SAML-tokenattribut. Värdet för anspråk på användaridentifierare i svaret måste matcha SSO-ID:et som är konfigurerat i Kenexa-systemet. Om du vill mappa lämplig användaridentifierare i organisationen som SSO Internet Datagram Protocol (IDP) arbetar du med [IBM Kenexa Survey Enterprise-supportteamet](https://www.ibm.com/support/home/?lnk=fcw).

    Som standard anger Azure AD användaridentifieraren som UPN-värdet (User Principal Name). Du kan ändra det här värdet på fliken **Användarattribut,** vilket visas i följande skärmbild. Integreringen fungerar bara när du har slutfört mappningen korrekt.

    ![image](common/edit-attribute.png)

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera IBM Kenexa Survey Enterprise.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Konfigurera IBM Kenexa Survey Enterprise Enkel inloggning

Om du vill konfigurera enkel inloggning på **IBM Kenexa Survey Enterprise-sidan** måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade url:er från Azure-portalen till [IBM Kenexa Survey Enterprise supportteam](https://www.ibm.com/support/home/?lnk=fcw). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till IBM Kenexa Survey Enterprise.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **IBM Kenexa Survey Enterprise**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **IBM Kenexa Survey Enterprise**i programlistan .

    ![IBM Kenexa Survey Enterprise länk i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>Skapa IBM Kenexa Survey Enterprise testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i IBM Kenexa Survey Enterprise.

Om du vill skapa användare i IBM Kenexa Survey Enterprise-systemet och mappa SSO-ID:t för dem kan du arbeta med [IBM Kenexa Survey Enterprise supportteam](https://www.ibm.com/support/home/?lnk=fcw). Det här SSO-ID-värdet ska också mappas till användaridentifieringsvärdet från Azure AD. Du kan ändra den här standardinställningen på fliken **Attribut.**

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen IBM Kenexa Survey Enterprise på åtkomstpanelen bör du automatiskt loggas in på IBM Kenexa Survey Enterprise som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

