---
title: 'Självstudier: Azure Active Directory-integrering med SAP Cloud for Customer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Cloud for Customer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 6f2fb29e705edde24d6f54faafd17b580ae131b5
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53809870"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Självstudier: Azure Active Directory-integrering med SAP Cloud for Customer

I den här självstudien får du lära dig hur du integrerar SAP Cloud for Customer med Azure Active Directory (Azure AD).
När du integrerar SAP Cloud for Customer med Azure AD innebär det följande fördelar:

* Du kan styra vem som har åtkomst till SAP Cloud for Customer i Azure AD.
* Du kan göra så att dina användare automatiskt loggas in på SAP Cloud for Customer (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med SAP Cloud for Customer behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Enkel inloggning aktiverad för SAP Cloud for Customer

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAP Cloud for Customer stöder **SP**-initierad enkel inloggning

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Lägga till SAP Cloud for Customer från galleriet

Om du vill konfigurera integreringen av SAP Cloud for Customer i Azure AD måste du lägga till SAP Cloud for Customer från galleriet till din lista över hanterade SaaS-appar.

**Lägg till SAP Cloud for Customer från galleriet genom att göra följande:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **SAP Cloud for Customer** i sökrutan, välj **SAP Cloud for Customer** på resultatpanelen och lägg sedan till programmet genom att klicka på knappen **Lägg till**.

     ![SAP Cloud for Customer i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet får du konfigurera och testa enkel inloggning i Azure AD med SAP Cloud for Customer baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAP Cloud for Customer upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med SAP Cloud for Customer måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för SAP Cloud for Customer](#configure-sap-cloud-for-customer-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en testanvändare för SAP Cloud for Customer](#create-sap-cloud-for-customer-test-user)**  – så att du får en motsvarighet till Britta Simon i SAP Cloud for Customer som är länkad till användarens Azure AD-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning i Azure AD med SAP Cloud for Customer genom att göra följande:

1. Välj **Enkel inloggning** på programintegreringssidan för **SAP Cloud for Customer** i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om SAP Cloud for Customer-domän och URL:er för enkel inloggning](common/sp-identifier.png)

    a. Skriv en URL i textrutan **Inloggnings-URL** enligt följande mönster: `https://<server name>.crm.ondemand.com`

    b. Skriv en URL i textrutan **Identifierare (entitets-ID)** enligt följande mönster: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren. Skaffa dess värden genom att kontakta [supportteamet för SAP Cloud for Customer-klienten](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Programmet SAP Cloud for Customer förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. Gör följande i avsnittet **Användarattribut** i dialogrutan **Användarattribut och anspråk**:

    a. Öppna dialogrutan **Hantera användaranspråk** genom att klicka på **redigeringsikonen**.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Välj **Transformering** som **källa**.

    c. Välj **ExtractMailPrefix()** i listan **Transformering**.

    d. Välj det användarattribut som du vill använda för din implementering i listan **Parameter 1**.
    Om du t.ex. vill använda EmployeeID som unikt användar-ID och du har lagrat attributvärdet i ExtensionAttribute2 väljer du sedan user.extensionattribute2.

    e. Klicka på **Spara**.

7. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. Kopiera URL:er som passar dina behov i avsnittet **Konfigurera SAP Cloud for Customer**.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sap-cloud-for-customer-single-sign-on"></a>Konfigurera enkel inloggning för SAP Cloud for Customer
   
1. Logga in på SAP Cloud for Customer-portalen med administratörsbehörighet.
   
2. Navigera till **Gemensam uppgift för program- och användarhantering** och klicka på fliken **Identitetsprovider**.
   
3. Klicka på **Ny identitetsprovider** och välj den metadata-XML-fil som du laddade ned från Azure Portal. Genom att importera metadata överför systemet automatiskt de nödvändiga signatur- och krypteringscertifikaten.
   
    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
4. Azure Active Directory kräver elementets URL för konsumenttjänst för försäkran i SAML-begäran. Markera därför kryssrutan **Inkludera URL för konsumenttjänst för försäkran**.
   
5. Klicka på **Aktivera enkel inloggning**.
   
6. Spara ändringarna.
   
7. Klicka på fliken **Mina System**.
   
    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
8. Klistra in den **inloggnings-URL** som du kopierade från Azure Portal i textrutan **Inloggnings-URL för Azure AD**.
   
    ![Konfigurera enkel inloggning](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
9. Ange om medarbetaren kan manuellt välja mellan att logga in med användar-ID och lösenord eller SSO genom att välja den **manuell identitet providern val av**.
   
10. Ange den URL som ska användas av dina anställda för att logga in i systemet i avsnittet **SSO-URL**. 
    Välj bland följande alternativ i listan **URL som skickats till anställd**:
   
    **Inte URL för enkel inloggning**
   
    Systemet skickar bara den vanliga system-URL:en till den anställde. Den anställde kan inte logga in med enkel inloggning, utan måste använda lösenord eller certifikat i stället.
   
    **URL FÖR ENKEL INLOGGNING** 
   
    Systemet skickar bara den vanliga system-URL:en till den anställde. Den anställde kan logga in med enkel inloggning. Begäran om autentisering dirigeras via IDP:n.
   
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
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet får du aktivera Britta Simon till att använda enkel inloggning i Azure genom att bevilja henne åtkomst till SAP Cloud for Customer.

1. Välj **Företagsprogram**, sedan **Alla program** och därefter **SAP Cloud for Customer** i Azure Portal.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Skriv och välj **SAP Cloud for Customer** i listan över program.

    ![SAP Cloud for Customer-länken i listan över program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Skapa en testanvändare för SAP Cloud for Customer

I det här avsnittet får du skapa en användare som kallas Britta Simon i SAP Cloud for Customer. Lägg till användare på SAP Cloud for Customer-plattformen i samarbete med  [supportteamet för SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). Användare måste skapas och aktiveras innan du använder enkel inloggning.

> [!NOTE]
> Kontrollera att NameID-värdet överensstämmer med fältet för användarnamn på SAP Cloud for Customer-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SAP Cloud for Customer-panelen i åtkomstpanelen bör du automatiskt loggas in på SAP Cloud for Customer som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

