---
title: 'Självstudier: Azure Active Directory-integrering med PerformanceCentre | Microsoft Docs'
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
ms.openlocfilehash: 0ce92abda9652351712f086ab24aff8575d84c8d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271585"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Självstudier: Azure Active Directory-integrering med PerformanceCentre

I den här självstudien får du lära dig hur du integrerar PerformanceCentre med Azure Active Directory (AD Azure).
Integrera PerformanceCentre med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till PerformanceCentre.
* Du kan aktivera användarna att vara automatiskt inloggad till PerformanceCentre (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med PerformanceCentre, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* PerformanceCentre enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för PerformanceCentre **SP** -initierad SSO

## <a name="adding-performancecentre-from-the-gallery"></a>Att lägga till PerformanceCentre från galleriet

För att konfigurera integrering av PerformanceCentre i Azure AD, som du behöver lägga till PerformanceCentre från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till PerformanceCentre från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **PerformanceCentre**väljer **PerformanceCentre** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![PerformanceCentre i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med PerformanceCentre baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i PerformanceCentre upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med PerformanceCentre, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera PerformanceCentre Single Sign-On](#configure-performancecentre-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare PerformanceCentre](#create-performancecentre-test-user)**  – du har en motsvarighet för Britta Simon i PerformanceCentre som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med PerformanceCentre:

1. I den [Azure-portalen](https://portal.azure.com/)på den **PerformanceCentre** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![PerformanceCentre domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL enligt följande mönster: `http://<companyname>.performancecentre.com/saml/SSO`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `http://<companyname>.performancecentre.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [PerformanceCentre klienten supportteamet](https://www.performancecentre.com/contact-us/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera PerformanceCentre** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-performancecentre-single-sign-on"></a>Konfigurera PerformanceCentre Single Sign-On

1. Inloggning till din **PerformanceCentre** företagets plats som administratör.

2. Klicka på fliken på vänster sida **konfigurera**.
   
    ![Azure AD enkel inloggning][10]

3. Klicka på fliken på vänster sida **diverse**, och klicka sedan på **Single Sign On**.
   
    ![Azure AD enkel inloggning][11]

4. Som **protokollet**väljer **SAML**.
   
    ![Azure AD enkel inloggning][12]

5. Öppna din hämtade metadatafilen i anteckningar, kopiera innehållet, klistra in den i den **identitet providern Metadata** textrutan och klicka sedan på **spara**.
   
    ![Azure AD enkel inloggning][13]

6. Kontrollera att värdena för den **entitet bas-URL** och **Entity ID URL** är korrekta.
    
     ![Azure AD enkel inloggning][14]

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PerformanceCentre.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **PerformanceCentre**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **PerformanceCentre**.

    ![Länken PerformanceCentre i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-performancecentre-test-user"></a>Skapa PerformanceCentre testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i PerformanceCentre.

**Om du vill skapa en användare som kallas Britta Simon i PerformanceCentre, utför du följande steg:**

1. Logga in på webbplatsen PerformanceCentre företag som administratör.

2. I menyn till vänster, klickar du på **Interrelate**, och klicka sedan på **skapa deltagare**.
   
    ![Skapa användare][400]

3. På den **samband – skapa deltagare** dialogrutan utför följande steg:
   
    ![Skapa användare][401]
    
    a. Ange attributen som krävs för Britta Simon i relaterade textrutor.
    
    >[!IMPORTANT]
    >Brittas användarnamn-attribut i PerformanceCentre måste vara samma som användarnamnet i Azure AD.
    
    b. Välj **Klientadministratör** som **väljer du rollen**.
    
    c. Klicka på **Spara**. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen PerformanceCentre i åtkomstpanelen, bör det vara loggas in automatiskt till PerformanceCentre som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png