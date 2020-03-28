---
title: 'Självstudiekurs: Azure Active Directory-integrering med Work.com | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7a6dc16eef1bb36a5bd6cbf0502a83481230bc0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087086"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Självstudiekurs: Azure Active Directory-integrering med Work.com

I den här självstudien får du lära dig hur du integrerar Work.com med Azure Active Directory (Azure AD).
Genom att integrera Work.com med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Work.com.
* Du kan aktivera dina användare så att de automatiskt loggas in på Work.com (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Work.com behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Work.com enda inloggningsaktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Work.com stöder **SP** initierade SSO

## <a name="adding-workcom-from-the-gallery"></a>Lägga till Work.com från galleriet

Om du vill konfigurera integreringen av Work.com i Azure AD måste du lägga till Work.com från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Work.com från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Work.com i**sökrutan och välj **Work.com** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![Work.com i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Work.com baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Work.com upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Work.com måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Work.com enkel inloggning](#configure-workcom-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Work.com testanvändare](#create-workcom-test-user)** – om du vill ha en motsvarighet till Britta Simon i Work.com som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

>[!NOTE]
>Om du vill konfigurera enkel inloggning måste du konfigurera ett anpassat Work.com domännamnet ännu. Du måste definiera minst ett domännamn, testa ditt domännamn och distribuera det till hela organisationen.

Så här konfigurerar du en enkel Azure AD-inloggning med Work.com:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Work.com** **programintegrering**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Work.com domän och webbadresser enkel inloggningsinformation](common/sp-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Work.com client support team](https://help.salesforce.com/articleView?id=000159855&type=3) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Work.com.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-workcom-single-sign-on"></a>Konfigurera Work.com enkel inloggning

1. Logga in på din Work.com klient som administratör.

2. Gå till **installationsprogrammet**.
   
    ![Installation](./media/work-com-tutorial/ic794108.png "Installation")

3. Klicka på **Domänhantering** i avsnittet **Administrera** i det vänstra navigeringsfönstret för att expandera det relaterade avsnittet och klicka sedan på **Min domän** för att öppna sidan **Min domän.** 
   
    ![Min domän](./media/work-com-tutorial/ic767825.png "Min domän")

4. Kontrollera att domänen har konfigurerats korrekt genom att kontrollera att den finns i "**Steg 4 distribuerad till användare**" och granska dina " Mina**domäninställningar**".
   
    ![Domän som distribueras till användare](./media/work-com-tutorial/ic784377.png "Domän som distribueras till användare")

5. Logga in på din Work.com innehavare.

6. Gå till **installationsprogrammet**.
    
    ![Installation](./media/work-com-tutorial/ic794108.png "Installation")

7. Expandera **menyn Säkerhetskontroller** och klicka sedan **på Inställningar för enkel inloggning**.
    
    ![Inställningar för enkel inloggning](./media/work-com-tutorial/ic794113.png "Inställningar för enkel inloggning")

8. Gör följande på dialogsidan För enkel **inloggningsinställningar:**
    
    ![SAML aktiverat](./media/work-com-tutorial/ic781026.png "SAML aktiverat")
    
    a. Välj **SAML-aktiverat **.
    
    b. Klicka på **Ny**.

9. Gör följande i avsnittet **Saml-inställningar för enkel inloggning:**
    
    ![SAML Enkel inloggningsinställning](./media/work-com-tutorial/ic794114.png "SAML Enkel inloggningsinställning")
    
    a. I textrutan **Namn** skriver du ett namn för konfigurationen.  
       
    > [!NOTE]
    > Om du anger ett värde för **Namn** fylls textrutan **för API Name** automatiskt.
    
    b. I **Textrutan Utfärdare** klistrar du in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen.
    
    c. Om du vill ladda upp det hämtade certifikatet från Azure-portalen klickar du på **Bläddra**.
    
    d. Skriv `https://salesforce-work.com`i textrutan **Entitets-ID** .
    
    e. Som **SAML-identitetstyp**väljer du **Kontroll innehåller federations-ID:et från användarobjektet**.
    
    f. Som **SAML-identitetsplats**väljer du **Identitet i elementet NameIdentfier i ämnesutdraget**.
    
    g. Klistra in värdet för **inloggningsadressen** som du har kopierat från Azure-portalen i url-textrutan för **identitetsprovider** inloggning.

    h. I **URL-textrutan för identitetsprovsanloggning** klistrar du in värdet **för url för utloggning** som du har kopierat från Azure-portalen.
    
    i. När **tjänstprovider initierade begäran bindning**väljer du **HTTP Post**.
    
    j. Klicka på **Spara**.

10. Klicka på **Domänhantering** i den vänstra navigeringsfönstret i Work.com klassiska portalen och klicka sedan på **Min domän** för att öppna sidan **Min domän.** 
    
    ![Min domän](./media/work-com-tutorial/ic794115.png "Min domän")

11. Klicka på **Redigera**i avsnittet **Branding av inloggningssida** på sidan **Min domän.**
    
    ![Varumärkesprofilering för inloggningssida](./media/work-com-tutorial/ic767826.png "Varumärkesprofilering för inloggningssida")

12. På sidan Branding av **inloggningssida** visas namnet på **dina SAML SSO-inställningar** i avsnittet **Autentiseringstjänst.** Markera den och klicka sedan på **Spara**.
    
    ![Varumärkesprofilering för inloggningssida](./media/work-com-tutorial/ic784366.png "Varumärkesprofilering för inloggningssida")

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Work.com.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Work.com**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Work.com**i programlistan .

    ![Länken Work.com i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-workcom-test-user"></a>Skapa Work.com testanvändare

För att Azure Active Directory-användare ska kunna logga in måste de etableras för att Work.com. När det gäller Work.com är etablering en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurera användaretablering genom att utföra följande steg:

1. Logga in på din Work.com företagswebbplats som administratör.

2. Gå till **installationsprogrammet**.
   
    ![Installation](./media/work-com-tutorial/IC794108.png "Installation")

3. Gå till **Hantera användare \> **.
   
    ![Hantera användare](./media/work-com-tutorial/IC784369.png "Hantera användare")

4. Klicka på **Ny användare**.
   
    ![Alla användare](./media/work-com-tutorial/IC794117.png "Alla användare")

5. I avsnittet Användarredigering utför du följande steg i attribut för ett giltigt Azure AD-konto som du vill etablera i relaterade textrutor:
   
    ![Redigera användare](./media/work-com-tutorial/ic794118.png "Redigera användare")
   
    a. Skriv **förnamnet för** användaren **Britta**i textrutan **Förnamn** .
    
    b. Skriv **efternamnet** för användaren **Simon**i textrutan **Efternamn** .
    
    c. Skriv **namnet** på användaren **BrittaS**i textrutan **Alias** .
    
    d. Skriv Brittasimon@contoso.com **användarens e-postadress** i **textrutan e-post** .
    
    e. Skriv **User Name** ett användarnamn för användaren som Brittasimon@contoso.com.
    
    f. Skriv ett **smeknamn för** användaren **Simon**i textrutan **Smeknamn** .
    
    g. Välj **Roll,** **Användarlicens**och **Profil**.
    
    h. Klicka på **Spara**.  
      
    > [!NOTE]
    > Azure AD-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt.
    > 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på den Work.com panelen på Åtkomstpanelen bör du automatiskt loggas in på den Work.com som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

