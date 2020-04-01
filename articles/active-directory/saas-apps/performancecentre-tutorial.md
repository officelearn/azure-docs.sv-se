---
title: 'Självstudiekurs: Azure Active Directory-integrering med PerformanceCentre | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PerformanceCentre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 08f0f1c1e9bbfcd27f97cd58fa6c6e40f448f9f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094639"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Självstudiekurs: Azure Active Directory-integrering med PerformanceCentre

I den här självstudien får du lära dig hur du integrerar PerformanceCentre med Azure Active Directory (Azure AD).
Genom att integrera PerformanceCentre med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till PerformanceCentre.
* Du kan aktivera dina användare så att de automatiskt loggas in på PerformanceCentre (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med PerformanceCentre behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* PerformanceCentre enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* PerformanceCentre **SP** stöder SP-initierad SSO

## <a name="adding-performancecentre-from-the-gallery"></a>Lägga till PerformanceCentre från galleriet

Om du vill konfigurera integreringen av PerformanceCentre i Azure AD måste du lägga till PerformanceCentre från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till PerformanceCentre från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **PerformanceCentre**i sökrutan och välj **PerformanceCentre** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![PerformanceCentre i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med PerformanceCentre baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i PerformanceCentre upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med PerformanceCentre måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera PerformanceCentre Single Sign-On](#configure-performancecentre-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa PerformanceCentre-testanvändare](#create-performancecentre-test-user)** – om du vill ha en motsvarighet till Britta Simon i PerformanceCentre som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med PerformanceCentre:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **PerformanceCentre-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![PerformanceCentre-domän och webbadresser med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `http://<companyname>.performancecentre.com/saml/SSO`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `http://<companyname>.performancecentre.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta Supportteamet för [PerformanceCentre-klienten](https://www.performancecentre.com/contact-us/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera PerformanceCentre.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-performancecentre-single-sign-on"></a>Konfigurera Enkel inloggning i PerformanceCentre

1. Logga in på **din PerformanceCentre-företagswebbplats** som administratör.

2. Klicka på **Konfigurera**på fliken till vänster.
   
    ![Enkel azure AD-inloggning][10]

3. Klicka på **Diverse**på fliken till vänster och klicka sedan **på Enkel inloggning**.
   
    ![Enkel azure AD-inloggning][11]

4. Som **protokoll**väljer du **SAML**.
   
    ![Enkel azure AD-inloggning][12]

5. Öppna den nedladdade metadatafilen i anteckningar, kopiera innehållet, klistra in den i textrutan **Identitetsprovidermetadata** och klicka sedan på **Spara**.
   
    ![Enkel azure AD-inloggning][13]

6. Kontrollera att värdena för **entitetsbas-URL:en** och **entitets-ID:n** är korrekta.
    
     ![Enkel azure AD-inloggning][14]

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till PerformanceCentre.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **PerformanceCentre**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **PerformanceCentre**i programlistan .

    ![PerformanceCentre-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-performancecentre-test-user"></a>Skapa PerformanceCentre-testanvändare

Syftet med detta avsnitt är att skapa en användare som heter Britta Simon i PerformanceCentre.

**Så här skapar du en användare som heter Britta Simon i PerformanceCentre:**

1. Logga in på din PerformanceCentre-företagswebbplats som administratör.

2. Klicka på **Interrelera**på menyn till vänster och klicka sedan på **Skapa deltagare**.
   
    ![Skapa användare][400]

3. I dialogrutan **Interrelate - Create Participant** utför du följande steg:
   
    ![Skapa användare][401]
    
    a. Skriv de attribut som krävs för Britta Simon i relaterade textrutor.
    
    >[!IMPORTANT]
    >Brittas användarnamnsattribut i PerformanceCentre måste vara samma som användarnamnet i Azure AD.
    
    b. Välj **Klientadministratör** som **Välj roll**.
    
    c. Klicka på **Spara**. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen PerformanceCentre på åtkomstpanelen bör du automatiskt loggas in på den PerformanceCentre som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png