---
title: 'Självstudier: Azure Active Directory-integrering med Work.com | Microsoft Docs'
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
ms.openlocfilehash: 816f9bfe022b4a00c01c3ee1bc243f87ef56817b
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565945"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Självstudier: Azure Active Directory-integrering med Work.com

I den här självstudien får du lära dig hur du integrerar Work.com med Azure Active Directory (AD Azure).
Integrera Work.com med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Work.com.
* Du kan aktivera användarna att vara automatiskt inloggad till Work.com (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Work.com, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Work.com enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Work.com **SP** -initierad SSO

## <a name="adding-workcom-from-the-gallery"></a>Att lägga till Work.com från galleriet

För att konfigurera integrering av Work.com i Azure AD, som du behöver lägga till Work.com från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Work.com från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Work.com**väljer **Work.com** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Work.com i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Work.com baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Work.com upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Work.com, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Work.com Single Sign-On](#configure-workcom-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Work.com](#create-workcom-test-user)**  – du har en motsvarighet för Britta Simon i Work.com som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

>[!NOTE]
>Om du vill konfigurera enkel inloggning, måste du konfigurera ett anpassat domännamn för Work.com ännu. Du måste ange minst ett domännamn, testa ditt domännamn och distribuera den till hela organisationen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Work.com:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Work.com** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Work.com domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Work.com klienten supportteamet](https://help.salesforce.com/articleView?id=000159855&type=3) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Work.com** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-workcom-single-sign-on"></a>Konfigurera Work.com Single Sign-On

1. Logga in på din Work.com-klient som administratör.

2. Gå till **installationsprogrammet**.
   
    ![Konfiguration](./media/work-com-tutorial/ic794108.png "Konfiguration")

3. I det vänstra navigeringsfönstret i den **administrera** klickar du på **domänhantering** Expandera avsnittet relaterade och klicka sedan på **min domän** att öppna **Min domän** sidan. 
   
    ![My Domain](./media/work-com-tutorial/ic767825.png "My Domain")

4. För att verifiera att din domän har ställts in korrekt, se till att den är i ”**steg 4 distribueras till användarna**” och granska din ”**Mina Domäninställningar**”.
   
    ![Domän som har distribuerats till användaren](./media/work-com-tutorial/ic784377.png "domän som har distribuerats till användare")

5. Logga in på din Work.com-klient.

6. Gå till **installationsprogrammet**.
    
    ![Konfiguration](./media/work-com-tutorial/ic794108.png "Konfiguration")

7. Expandera den **säkerhetskontroller** menyn och klicka sedan på **inställningar för enkel inloggning**.
    
    ![Inställningar för enkel inloggning](./media/work-com-tutorial/ic794113.png "Inställningar för enkel inloggning")

8. På den **inställningar för enkel inloggning** dialogrutan utför följande steg:
    
    ![SAML aktiverat](./media/work-com-tutorial/ic781026.png "SAML aktiverat")
    
    a. Välj **SAML-aktiverat** .
    
    b. Klicka på **Ny**.

9. I den **SAML enkel inloggning inställningar** avsnittet, utför följande steg:
    
    ![SAML enkel inloggning inställningen](./media/work-com-tutorial/ic794114.png "SAML enkel inloggning för inställningen")
    
    a. I textrutan **Namn** skriver du ett namn för konfigurationen.  
       
    > [!NOTE]
    > Att tillhandahålla ett värde för **namn** automatiskt fylla i **API-namn** textrutan.
    
    b. I **utfärdare** textrutan klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen.
    
    c. Om du vill ladda upp det nedladdade certifikatet från Azure-portalen, klickar du på **Bläddra**.
    
    d. I den **entitets-Id** textrutan typ `https://salesforce-work.com`.
    
    e. Som **SAML identitetstypen**väljer **Assertion innehåller Federation-ID från användarobjektet**.
    
    f. Som **SAML identitet plats**väljer **identitet är i NameIdentfier-elementet i instruktionen ämne**.
    
    g. I **inloggnings-URL för identitetsprovider** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    h. I **utloggnings-URL för identitetsprovider** textrutan klistra in värdet för **URL för utloggning** som du har kopierat från Azure-portalen.
    
    i. Som **providern initierade begära Tjänstbindning**väljer **HTTP Post**.
    
    j. Klicka på **Spara**.

10. I den klassiska portalen i Work.com, i det vänstra navigeringsfönstret klickar du på **domänhantering** Expandera avsnittet relaterade och klicka sedan på **min domän** att öppna den **min domän** sidan. 
    
    ![My Domain](./media/work-com-tutorial/ic794115.png "My Domain")

11. På den **min domän** sidan den **inloggning anpassningen** klickar du på **redigera**.
    
    ![Inloggningssidan anpassning](./media/work-com-tutorial/ic767826.png "inloggningssidan för anpassning")

12. På den **inloggning anpassningen** sidan den **autentiseringstjänst** avsnittet, namnet på din **SAML SSO-inställningar** visas. Markera den och klicka sedan på **spara**.
    
    ![Inloggningssidan anpassning](./media/work-com-tutorial/ic784366.png "inloggningssidan för anpassning")

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Work.com.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Work.com**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Work.com**.

    ![Länken Work.com i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-workcom-test-user"></a>Skapa Work.com testanvändare

För Azure Active Directory-användare för att kunna logga in måste de etableras till Work.com. När det gäller Work.com är etablering en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurera användaretablering genom att utföra följande steg:

1. Logga in på webbplatsen Work.com företag som administratör.

2. Gå till **installationsprogrammet**.
   
    ![Konfiguration](./media/work-com-tutorial/IC794108.png "Konfiguration")

3. Gå till **hantera användare \> användare**.
   
    ![Hantera användare](./media/work-com-tutorial/IC784369.png "Hantera användare")

4. Klicka på **Ny användare**.
   
    ![Alla användare](./media/work-com-tutorial/IC794117.png "alla användare")

5. I avsnittet användaren redigera utför du följande steg, i attributen för en giltig Azure AD-konto som du vill etablera till relaterade textrutor:
   
    ![Redigera användare](./media/work-com-tutorial/ic794118.png "Redigera användare")
   
    a. I den **Förnamn** textrutan skriver den **Förnamn** användarens **Britta**.
    
    b. I den **efternamn** textrutan skriver den **efternamn** användarens **Simon**.
    
    c. I den **Alias** textrutan skriver den **namn** användarens **BrittaS**.
    
    d. I den **e-post** textrutan skriver den **e-postadress** användarens Brittasimon@contoso.com.
    
    e. I den **användarnamn** textrutan, skriver du in ett användarnamn för användaren som Brittasimon@contoso.com.
    
    f. I den **smeknamn** textrutan anger du ett **smeknamn** användarens **Simon**.
    
    g. Välj **rollen**, **användarlicens**, och **profil**.
    
    h. Klicka på **Spara**.  
      
    > [!NOTE]
    > Azure AD-kontoinnehavare får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktiv.
    > 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Work.com i åtkomstpanelen, bör det vara loggas in automatiskt till Work.com som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

