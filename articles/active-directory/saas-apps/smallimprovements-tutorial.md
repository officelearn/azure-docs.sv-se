---
title: 'Självstudiekurs: Azure Active Directory-integrering med små förbättringar | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Små förbättringar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: d2d0bbc7a6e1c680434041d1b9d55e39a96b6f44
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090366"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Självstudiekurs: Azure Active Directory-integrering med små förbättringar

I den här självstudien får du lära dig hur du integrerar små förbättringar med Azure Active Directory (Azure AD).
Genom att integrera små förbättringar med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till små förbättringar.
* Du kan aktivera dina användare så att de automatiskt loggas in på små förbättringar (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med små förbättringar behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Liten förbättringar enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Små förbättringar stöder **SP** initierade SSO

## <a name="adding-small-improvements-from-the-gallery"></a>Lägga till små förbättringar från galleriet

Om du vill konfigurera integreringen av små förbättringar i Azure AD måste du lägga till små förbättringar från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till små förbättringar från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Små förbättringar**i sökrutan och **klicka** sedan på Lägg till knappen **Lägg** till för att lägga till programmet.

     ![Små förbättringar i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med små förbättringar baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i små förbättringar upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med små förbättringar måste du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera små förbättringar enkel inloggning](#configure-small-improvements-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa små förbättringar testanvändare](#create-small-improvements-test-user)** - att ha en motsvarighet till Britta Simon i små förbättringar som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med små förbättringar:

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning på**sidan Integrering av små **förbättringar.**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Domän- och url-url:er för små förbättringar enkel inloggningsinformation](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.small-improvements.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [supportteamet för små förbättringar för](mailto:support@small-improvements.com) att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera små förbättringar.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-small-improvements-single-sign-on"></a>Konfigurera en enkel inloggning för små förbättringar

1. I ett annat webbläsarfönster loggar du in på företagets webbplats för små förbättringar som administratör.

1. Klicka på **Knappen Administration** till vänster på huvudpanelsidan.

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Klicka på **SAML SSO-knappen** från **avsnittet Integrationer.**

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Gör följande på sidan SSO-installationsprogrammet:

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Klistra in värdet **för inloggnings-URL**i **HTTP-slutpunktstextrutan** som du har kopierat från Azure-portalen.

    b. Öppna det nedladdade certifikatet i Anteckningar, kopiera innehållet och klistra sedan in det i textrutan **x509 Certifikat.** 

    c. Om du vill ha SSO och inloggningsformulär autentisering alternativ tillgängligt för användare, sedan kontrollera **Alternativet Aktivera åtkomst via inloggning / lösenord också.**  

    d. Ange lämpligt värde för att namnge SSO-inloggningsknappen i **SMS:en i SAML-prompten.**  

    e. Klicka på **Spara**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till små förbättringar.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan Små **förbättringar**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Små förbättringar**i programlistan .

    ![Länken Små förbättringar i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-small-improvements-test-user"></a>Testa användaren Skapa små förbättringar

Om du vill att Azure AD-användare ska kunna logga in på små förbättringar måste de etableras i små förbättringar. När det gäller små förbättringar är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på företagets webbplats för små förbättringar som administratör.

1. Gå till menyn till vänster på startsidan och klicka på **Administration**.

1. Klicka på knappen **Användarkatalog** från avsnittet Användarhantering.

    ![Skapa en testanvändare för Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Klicka på **Lägg till användare**.

    ![Skapa en testanvändare för Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. Gör följande i dialogrutan **Lägg till användare:** 

    ![Skapa en testanvändare för Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Ange **förnamnet** för användare som **Britta**.

    b. Ange **efternamn** på användaren som **Simon**.

    c. Ange **e-post** **brittasimon@contoso.com**för användare som .

    d. Du kan också välja att ange det personliga meddelandet i **e-postmeddelandet Skicka meddelande.** Om du inte vill skicka meddelandet avmarkerar du den här kryssrutan.

    e. Klicka på **Skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Små förbättringar på åtkomstpanelen bör du automatiskt loggas in på de små förbättringar som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)