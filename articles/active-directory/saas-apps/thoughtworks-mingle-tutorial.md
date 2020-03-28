---
title: 'Självstudiekurs: Azure Active Directory-integrering med Thoughtworks Mingle | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Thoughtworks Mingle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a12d4dca61734275ef0e56dfe2a800c64dc52540
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233295"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Självstudiekurs: Azure Active Directory-integrering med Thoughtworks Mingle

I den här självstudien får du lära dig hur du integrerar Thoughtworks Mingle med Azure Active Directory (Azure AD).
Genom att integrera Thoughtworks Mingle med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Thoughtworks Mingle.
* Du kan aktivera dina användare så att de automatiskt loggas in på Thoughtworks Mingle (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Thoughtworks Mingle behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Thoughtworks Mingle enda sign-on aktiverat abonnemang

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Thoughtworks Mingle stödjer **SP** initierade SSO

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Lägga Thoughtworks Mingle från galleriet

Om du vill konfigurera integreringen av Thoughtworks Mingle i Azure AD måste du lägga till Thoughtworks Mingle från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Thoughtworks Mingle från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Thoughtworks Mingle**i sökrutan och välj **Thoughtworks Mingle** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![Thoughtworks Mingla i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med Thoughtworks Mingle baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Thoughtworks Mingle upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Thoughtworks Mingle måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Thoughtworks Mingle Single Sign-On](#configure-thoughtworks-mingle-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Thoughtworks Mingle testanvändare](#create-thoughtworks-mingle-test-user)** - att ha en motsvarighet till Britta Simon i Thoughtworks Mingle som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Thoughtworks Mingle:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Thoughtworks Mingle-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Thoughtworks Mingle Domain och webbadresser enkel inloggningsinformation](common/sp-signonurl.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Thoughtworks Mingle Client supportteam](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Thoughtworks Mingle.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Konfigurera Thoughtworks Mingle Enkel inloggning

1. Logga in på din **Webbplats för Thoughtworks Mingle-företag** som administratör.

2. Klicka på fliken **Admin** och sedan på **SSO Config**.
   
    ![Fliken Admin](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO-config")

3. Gör följande i avsnittet **SSO Config:**
   
    ![SSO-config](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO-config")
    
    a. Om du vill ladda upp metadatafilen klickar du på **Välj fil**. 

    b. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure single sign-on genom att bevilja åtkomst till Thoughtworks Mingle.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Thoughtworks Mingle**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Thoughtworks Mingle**.

    ![Länken Thoughtworks Mingle i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-thoughtworks-mingle-test-user"></a>Skapa användare av Thoughtworks Mingle-test

För att Azure AD-användare ska kunna logga in måste de etableras i Thoughtworks Mingle-programmet med sina Azure Active Directory-användarnamn. När det gäller Thoughtworks Mingle är etablering en manuell uppgift.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din Webbplats för Thoughtworks Mingle-företag som administratör.

2. Klicka på **Profil**.
   
    ![Ditt första projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "Ditt första projekt")

3. Klicka på fliken **Admin** och sedan på **Användare**.
   
    ![Användare](./media/thoughtworks-mingle-tutorial/ic785161.png "Användare")

4. Klicka på **Ny användare**.
   
    ![Ny användare](./media/thoughtworks-mingle-tutorial/ic785162.png "Ny användare")

5. I dialogrutan **Ny användare** utför du följande steg:
   
    ![Dialogrutan Ny användare](./media/thoughtworks-mingle-tutorial/ic785163.png "Ny användare")  
 
    a. Skriv **inloggningsnamnet**, **Visningsnamn**, **Välj lösenord**, **Bekräfta lösenord** för ett giltigt Azure AD-konto som du vill etablera i relaterade textrutor. 

    b. Som **användartyp**väljer du **Fullständig användare**.

    c. Klicka på **Skapa den här profilen**.

>[!NOTE]
>Du kan använda andra Verktyg för att skapa ett användarkonto i Thoughtworks Mingle för att etablera Azure AD-användarkonton.
> 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Thoughtworks Mingle på åtkomstpanelen ska du automatiskt loggas in på den Thoughtworks Mingle som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

