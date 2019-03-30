---
title: 'Självstudier: Azure Active Directory-integrering med Pega system | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pega system.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 59dc9f82251e7a406e6fe1339fdb55b4880cd74d
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649194"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Självstudier: Azure Active Directory-integrering med Pega system

I den här självstudien får du lära dig hur du integrerar Pega system med Azure Active Directory (AD Azure).
Integrera Pega system med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Pega system.
* Du kan aktivera användarna att vara automatiskt inloggad till Pega system (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Pega system behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Pega system med enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Pega system **SP** och **IDP** -initierad SSO

## <a name="adding-pega-systems-from-the-gallery"></a>Att lägga till Pega system från galleriet

Om du vill konfigurera integreringen av Pega system till Azure AD, som du behöver lägga till Pega system från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Pega system från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Pega system**väljer **Pega system** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Pega system i listan med resultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Pega system baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Pega system upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Pega system, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Pega-system](#configure-pega-systems-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Pega system testanvändare](#create-pega-systems-test-user)**  – du har en motsvarighet för Britta Simon i Pega system som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Pega system:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Pega system** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Pega system domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Pega system domän och URL: er med enkel inloggning för information](common/both-advanced-urls.png)

    a. I den **inloggnings-URL** text skriver tecken i URL-värdet.

    b. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare, svars-URL, URL: en inloggning och vidarebefordra tillstånd URL: en. Du hittar du värdet för identifieraren och svars-URL från Pega program som beskrivs senare i den här självstudien. Vidarebefordransstatus, kontakta [Pega system klienten supportteamet](https://www.pega.com/contact-us) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Pega System program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen  **Redigera** för att öppna dialogrutan **Användarattribut** .

    ![image](common/edit-attribute.png)

7. Förutom ovanstående Pega System program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | ------------------- | -------------------- |
    | uid | *********** |
    | CN  | *********** |
    | e-post | *********** |
    | accessgroup | *********** |
    | organisation | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | Telefon | *********** |

    > [!NOTE]
    > Det här är kund specifika värden. Ange din lämpliga värden.

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

8. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

9. På den **konfigurera Pega system** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-pega-systems-single-sign-on"></a>Konfigurera enkel inloggning för Pega-system

1. Att konfigurera enkel inloggning på **Pega system** sida, öppna den **Pega Portal** med administratörskonto i ett annat webbläsarfönster.

2. Välj **skapa** -> **SysAdmin** -> **autentiseringstjänst**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Utför följande åtgärder på **skapa autentiseringstjänst** skärmen:

    ![Konfigurera enkel inloggning – knappen Spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Välj **SAML 2.0** från typen

    b. I den **namn** textrutan Ange alla namn t.ex Azure AD SSO

    c. I den **kort beskrivning** textrutan Ange någon beskrivning  

    d. Klicka på **skapa och öppna** 
    
4. I **identitetsprovider (IdP) information** klickar du på **Import IdP metadata** och bläddra i metadatafilen som du har hämtat från Azure-portalen. Klicka på **skicka** att läsa in metadata.

    ![Konfigurera enkel inloggning – knappen Spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
5. Det här fylls IdP-data som visas nedan.

    ![Konfigurera enkel inloggning – knappen Spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Utför följande åtgärder på **Service Provider (SP) inställningar** avsnittet:

    ![Konfigurera enkel inloggning – knappen Spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Kopiera den **enhetens identifiering** och klistra in den i **identifierare** -textrutan i den **SAML grundkonfiguration** i Azure-portalen.

    b. Kopiera den **Assertion konsument Service (ACS) plats** och klistra in den i **svars-URL** -textrutan i den **grundkonfiguration SAML** i Azure-portalen.

    c. Välj **inaktivera Begär signering**.

7. Klicka på **Spara**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Pega system.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Pega system**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Pega system**.

    ![Länken Pega system i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-pega-systems-test-user"></a>Skapa Pega system testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Pega system. Arbeta med [Pega system klienten supportteamet](https://www.pega.com/contact-us) att skapa användare i Pega system.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Pega system i åtkomstpanelen, bör det vara loggas in automatiskt till Pega system som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)