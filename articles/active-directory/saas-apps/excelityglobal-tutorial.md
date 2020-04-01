---
title: 'Självstudiekurs: Azure Active Directory-integrering med ExcelityGlobal | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ExcelityGlobal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e84becde-64bf-4089-be06-7555616b0965
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: a8ea503343f05f8b7ae5f121f3365f82794be2f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73156786"
---
# <a name="tutorial-azure-active-directory-integration-with-excelityglobal"></a>Självstudiekurs: Azure Active Directory-integrering med ExcelityGlobal

I den här självstudien får du lära dig hur du integrerar ExcelityGlobal med Azure Active Directory (Azure AD).
Genom att integrera ExcelityGlobal med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till ExcelityGlobal.
* Du kan aktivera dina användare så att de automatiskt loggas in i ExcelityGlobal (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med ExcelityGlobal behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ExcelityGlobal enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ExcelityGlobal stöder **IDP-initierad** SSO

## <a name="adding-excelityglobal-from-the-gallery"></a>Lägga till ExcelityGlobal från galleriet

Om du vill konfigurera integreringen av ExcelityGlobal i Azure AD måste du lägga till ExcelityGlobal från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till ExcelityGlobal från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **ExcelityGlobal**i sökrutan och välj **ExcelityGlobal** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![ExcelityGlobal i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med ExcelityGlobal baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i ExcelityGlobal upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med ExcelityGlobal måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ExcelityGlobal Single Sign-On](#configure-excelityglobal-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ExcelityGlobal-testanvändare](#create-excelityglobal-test-user)** – om du vill ha en motsvarighet till Britta Simon i ExcelityGlobal som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med ExcelityGlobal:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **ExcelityGlobal-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![ExcelityGlobal domän och webbadresser enkel inloggningsinformation](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    **För produktionsmiljö:**`https://ess.excelityglobal.com`

    **För Sandbox Miljö:**`https://s6.excelityglobal.com`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    **För produktionsmiljö:**`https://ess.excelityglobal.com/ACS`

    **För Sandbox Miljö:**`https://s6.excelityglobal.com/ACS`

5. ExcelityGlobal-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. ExcelityGlobal-programmet förväntar sig att **namnidentifieraren** mappas med **user.mail**, så du måste redigera attributmappningen genom att klicka på **Redigera-ikonen** och ändra attributmappningen.
 
    ![image](common/edit-attribute.png)

6. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

7. Kopiera **tumavtrycket** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

8. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera ExcelityGlobal.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-excelityglobal-single-sign-on"></a>Konfigurera ExcelityGlobal enkel inloggning

Om du vill konfigurera enkel inloggning på **ExcelityGlobal-sidan** måste du skicka **tumavtrycksvärdet** och lämpliga kopierade url:er från Azure-portalen till [ExcelityGlobal supportteam](https://www.excelityglobal.com/contact-us). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** skriver **du\@brittasimon yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till ExcelityGlobal.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **ExcelityGlobal**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **ExcelityGlobal**i programlistan .

    ![Länken ExcelityGlobal i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-excelityglobal-test-user"></a>Skapa ExcelityGlobal-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i ExcelityGlobal. Arbeta med [ExcelityGlobal supportteam](https://www.excelityglobal.com/contact-us) för att lägga till användarna i ExcelityGlobal-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen ExcelityGlobal på åtkomstpanelen bör du automatiskt loggas in i excelityglobal som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)