---
title: 'Självstudiekurs: Azure Active Directory-integrering med SAP Business ByDesign | Microsoft-dokument'
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
ms.openlocfilehash: 0b830f0760b768826b2d937b4a8b2ffbd8e9e2a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091677"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Självstudier: Azure Active Directory-integration med SAP Business ByDesign

I den här självstudien får du lära dig hur du integrerar SAP Business ByDesign med Azure Active Directory (Azure AD).
Genom att integrera SAP Business ByDesign med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SAP Business ByDesign.
* Du kan aktivera dina användare så att de automatiskt loggas in på SAP Business ByDesign (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med SAP Business ByDesign behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* SAP Business ByDesign enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAP Business ByDesign stöder **SP-initierad** SSO

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Lägga till SAP Business ByDesign från galleriet

Om du vill konfigurera integreringen av SAP Business ByDesign i Azure AD måste du lägga till SAP Business ByDesign från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till SAP Business ByDesign från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv SAP Business **ByDesign**i sökrutan och välj **SAP Business ByDesign** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

    ![SAP Business ByDesign i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med SAP Business ByDesign baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAP Business ByDesign upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med SAP Business ByDesign måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SAP Business ByDesign Enkel inloggning](#configure-sap-business-bydesign-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SAP Business ByDesign-testanvändare](#create-sap-business-bydesign-test-user)** – om du vill ha en motsvarighet till Britta Simon i SAP Business ByDesign som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med SAP Business ByDesign:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **SAP Business ByDesign-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Sap Business ByDesign-domän och webbadresser med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<servername>.sapbydesign.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [SAP Business ByDesign-klientsupportteamet](https://www.sap.com/products/cloud-analytics.support.html) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. SAP Business ByDesign-programmet förväntar sig SAML-påståenden i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. Klicka på ikonen **Redigera** om du vill redigera **namnidentifieringsvärdet**.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. I avsnittet **Hantera användarens anspråk** utför ![du följande steg: bild](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Välj **Omvandling** som **källa**.

    b. Välj **ExtractMailPrefix()** i listrutan **Omvandling.**

    c. I **listrutan Parameter 1** väljer du det användarattribut som du vill använda för implementeringen. Om du t.ex. vill använda EmployeeID som unikt användar-ID och du har lagrat attributvärdet i ExtensionAttribute2 väljer du sedan user.extensionattribute2.

    d. Klicka på **Spara**.

8. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

9. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera SAP Business ByDesign.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Konfigurera ENKEL INLOGGNING FÖR SAP Business ByDesign

1. Logga in på DIN SAP Business ByDesign-portal med administratörsrättigheter.

2. Navigera till **Gemensam uppgift för program- och användarhantering** och klicka på fliken **Identitetsprovider.**

3. Klicka på **Ny identitetsprovider** och välj den METADATA-XML-fil som du har hämtat från Azure-portalen. Genom att importera metadata överför systemet automatiskt de nödvändiga signatur- och krypteringscertifikaten.

    ![Konfigurera enkel inloggning](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Om du vill inkludera **url:en för kontrolltjänsten i** SAML-begäran väljer du **Inkludera url för kontrolltjänster för konsumenttjänst**.

5. Klicka på **Aktivera enkel inloggning**.

6. Spara ändringarna.

7. Klicka på fliken **Mina System**.

    ![Konfigurera enkel inloggning](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. I textrutan **Azure AD Sign On URL** klistrar du in **url-värdet för inloggning,** som du har kopierat från Azure-portalen.

    ![Konfigurera enkel inloggning](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Ange om medarbetaren manuellt kan välja mellan att logga in med användar-ID och lösenord eller SSO genom att välja **Val av manuell identitetsprovider**.

10. I avsnittet **SSO URL** anger du den URL som ska användas av medarbetaren för att logga in på systemet.
    I listrutan URL som skickas till medarbetare kan du välja mellan följande alternativ:

    **Inte URL för enkel inloggning**

    Systemet skickar bara den vanliga system-URL:en till den anställde. Medarbetaren kan inte logga in med SSO och måste använda lösenord eller certifikat i stället.

    **URL FÖR ENKEL INLOGGNING**

    Systemet skickar bara den vanliga system-URL:en till den anställde. Medarbetaren kan logga in med SSO. Begäran om autentisering dirigeras via IDP:n.

    **Automatiskt val**

    Om enkel inloggning inte ar aktivt skickar systemet bara den vanliga system-URL:en till den anställde. Om enkel inloggning är aktivt kontrollerar systemet om den anställde har ett lösenord. Om ett lösenord är tillgängligt skickas både URL:en för enkel inloggning och URL:en för inloggning utan enkel inloggning till den anställde. Om den anställde inte har något lösenord skickas bara URL:en för enkel inloggning till den anställde.

11. Spara ändringarna.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Business ByDesign.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan SAP **Business ByDesign**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj SAP Business **ByDesign**i programlistan .

    ![Länken SAP Business ByDesign i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sap-business-bydesign-test-user"></a>Skapa testanvändare för SAP Business ByDesign

I det här avsnittet skapar du en användare som heter Britta Simon i SAP Business ByDesign. Arbeta med [SAP Business ByDesign-klientsupportteamet](https://www.sap.com/products/cloud-analytics.support.html) för att lägga till användarna i SAP Business ByDesign-plattformen. 

> [!NOTE]
> Kontrollera att NameID-värdet ska matchas med användarnamnsfältet på SAP Business ByDesign-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SAP Business ByDesign på åtkomstpanelen bör du automatiskt loggas in på den SAP Business ByDesign som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
