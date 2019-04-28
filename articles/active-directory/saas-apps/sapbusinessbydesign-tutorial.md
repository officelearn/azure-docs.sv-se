---
title: 'Självstudier: Azure Active Directory-integrering med SAP Business ByDesign | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bb7caedd440f6591d0e538f69eac4abf4e6d5be
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104807"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Självstudier: Azure Active Directory-integrering med SAP Business ByDesign

I den här självstudien får du lära dig hur du integrerar SAP Business ByDesign med Azure Active Directory (AD Azure).
Integrera SAP Business ByDesign med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SAP Business ByDesign.
* Du kan aktivera användarna att vara automatiskt loggat in till SAP Business ByDesign (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med SAP Business ByDesign, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* SAP Business ByDesign enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för SAP Business ByDesign **SP** -initierad SSO

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Att lägga till SAP Business ByDesign från galleriet

För att konfigurera integrering av SAP Business ByDesign i Azure AD, som du behöver lägga till SAP Business ByDesign från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SAP Business ByDesign från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **SAP Business ByDesign**väljer **SAP Business ByDesign** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![SAP Business ByDesign i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAP Business ByDesign baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAP Business ByDesign upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP Business ByDesign, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SAP Business ByDesign enkel inloggning](#configure-sap-business-bydesign-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SAP Business ByDesign testanvändare](#create-sap-business-bydesign-test-user)**  – du har en motsvarighet för Britta Simon i SAP Business ByDesign som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med SAP Business ByDesign:

1. I den [Azure-portalen](https://portal.azure.com/)på den **SAP Business ByDesign** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SAP Business ByDesign domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<servername>.sapbydesign.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [SAP Business ByDesign Client supportteamet](https://www.sap.com/products/cloud-analytics.support.html) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. SAP Business ByDesign program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. Klicka på den **redigera** ikon för att redigera den **namnge identifierarvärde**.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. På den **hantera användaranspråk** avsnittet, utför följande steg: ![bild](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Välj **omvandling** som en **källa**.

    b. I den **omvandling** listrutan, väljer **ExtractMailPrefix()**.

    c. I den **Parameter 1** listrutan väljer du det användarattribut som du vill använda för din implementering. Om du t.ex. vill använda EmployeeID som unikt användar-ID och du har lagrat attributvärdet i ExtensionAttribute2 väljer du sedan user.extensionattribute2.

    d. Klicka på **Spara**.

8. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

9. På den **konfigurera SAP Business ByDesign** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Konfigurera SAP Business ByDesign Single Sign-On

1. Logga in på din SAP Business ByDesign portal med administratörsbehörighet.

2. Gå till **program och vanliga Användarhanteringsuppgift** och klicka på den **identitetsprovider** fliken.

3. Klicka på **nya identitetsprovider** och välj den XML-fil som du har hämtat från Azure-portalen. Genom att importera metadata överför systemet automatiskt de nödvändiga signatur- och krypteringscertifikaten.

    ![Konfigurera enkel inloggning](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Att inkludera den **URL för Konsumenttjänst för försäkran** i SAML-begäran väljer **inkluderar Assertion URL för Konsumenttjänst**.

5. Klicka på **Aktivera enkel inloggning**.

6. Spara ändringarna.

7. Klicka på fliken **Mina System**.

    ![Konfigurera enkel inloggning](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. I den **Azure AD URL: en inloggning** textrutan klistra in **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    ![Konfigurera enkel inloggning](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Ange om medarbetaren kan manuellt välja mellan att logga in med användar-ID och lösenord eller SSO genom att välja **manuell identitet providern val av**.

10. I den **SSO URL** avsnittet, ange den URL som ska användas av anställda möjlighet att inloggning i systemet.
    I den URL: en skickas till anställda listrutan kan du välja mellan följande alternativ:

    **Inte URL för enkel inloggning**

    Systemet skickar bara den vanliga system-URL:en till den anställde. Medarbetaren måste kan inte inloggning med enkel inloggning, och använda lösenord eller certifikat i stället.

    **URL FÖR ENKEL INLOGGNING**

    Systemet skickar bara den vanliga system-URL:en till den anställde. Medarbetaren kan inloggning med enkel inloggning. Begäran om autentisering dirigeras via IDP:n.

    **Automatiskt val**

    Om enkel inloggning inte ar aktivt skickar systemet bara den vanliga system-URL:en till den anställde. Om enkel inloggning är aktivt kontrollerar systemet om den anställde har ett lösenord. Om ett lösenord är tillgängligt skickas både URL:en för enkel inloggning och URL:en för inloggning utan enkel inloggning till den anställde. Om den anställde inte har något lösenord skickas bara URL:en för enkel inloggning till den anställde.

11. Spara ändringarna.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Business ByDesign.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **SAP Business ByDesign**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **SAP Business ByDesign**.

    ![SAP Business ByDesign länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sap-business-bydesign-test-user"></a>Skapa testanvändare för SAP Business ByDesign

I det här avsnittet skapar du en användare som kallas Britta Simon i SAP Business ByDesign. Kontakta [SAP Business ByDesign Client supportteamet](https://www.sap.com/products/cloud-analytics.support.html) att lägga till användare i SAP Business ByDesign-plattformen. 

> [!NOTE]
> Kontrollera att NameID-värdet måste matcha med fältet för användarnamn i SAP Business ByDesign-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SAP Business ByDesign i åtkomstpanelen, bör det vara loggas in automatiskt till den som du ställer in enkel inloggning i SAP Business ByDesign. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
