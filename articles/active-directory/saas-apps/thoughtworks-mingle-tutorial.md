---
title: 'Självstudier: Azure Active Directory-integrering med Thoughtworks Mingle | Microsoft Docs'
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
ms.openlocfilehash: 5acf02c021fdfd9f85eeb2b6b1c697ce92c48a15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259515"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Självstudier: Azure Active Directory-integrering med Thoughtworks Mingle

I den här självstudien får du lära dig hur du integrerar Thoughtworks Mingle med Azure Active Directory (AD Azure).
Integrera Thoughtworks Mingle med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Thoughtworks Mingle.
* Du kan aktivera användarna att vara automatiskt inloggad till Thoughtworks Mingle (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Thoughtworks Mingle, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Thoughtworks Mingle enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Thoughtworks Mingle **SP** -initierad SSO

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Att lägga till Thoughtworks Mingle från galleriet

För att konfigurera integrering av Thoughtworks Mingle i Azure AD, som du behöver lägga till Thoughtworks Mingle från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Thoughtworks Mingle från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Thoughtworks Mingle**väljer **Thoughtworks Mingle** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Thoughtworks Mingle i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Thoughtworks Mingle baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Thoughtworks Mingle upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Thoughtworks Mingle, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Thoughtworks Mingle enkel inloggning](#configure-thoughtworks-mingle-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Thoughtworks Mingle testanvändare](#create-thoughtworks-mingle-test-user)**  – du har en motsvarighet för Britta Simon i Thoughtworks Mingle som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Thoughtworks Mingle:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Thoughtworks Mingle** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Thoughtworks Mingle domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Thoughtworks Mingle klienten supportteamet](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera Thoughtworks Mingle** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Konfigurera Thoughtworks Mingle enkel inloggning

1. Logga in på din **Thoughtworks Mingle** företagets plats som administratör.

2. Klicka på den **Admin** fliken och klicka sedan på **SSO Config**.
   
    ![Fliken Administration](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO-konfiguration")

3. I den **SSO Config** avsnittet, utför följande steg:
   
    ![SSO Config](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO Config")
    
    a. Om du vill ladda upp metadatafilen, klickar du på **Välj fil**. 

    b. Klicka på **Spara ändringar**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Thoughtworks Mingle.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Thoughtworks Mingle**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Thoughtworks Mingle**.

    ![Länken Thoughtworks Mingle i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-thoughtworks-mingle-test-user"></a>Skapa Thoughtworks Mingle testanvändare

För Azure AD-användare för att kunna logga in, måste de etableras till programmet Thoughtworks Mingle med deras Azure Active Directory-användarnamn. När det gäller Thoughtworks Mingle är etablering en manuell aktivitet.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på webbplatsen Thoughtworks Mingle företag som administratör.

2. Klicka på **profil**.
   
    ![Ditt första projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "ditt första projekt")

3. Klicka på den **Admin** fliken och klicka sedan på **användare**.
   
    ![Användare](./media/thoughtworks-mingle-tutorial/ic785161.png "Användare")

4. Klicka på **Ny användare**.
   
    ![Ny användare](./media/thoughtworks-mingle-tutorial/ic785162.png "Ny användare")

5. I dialogrutan **Ny användare** utför du följande steg:
   
    ![Dialogrutan Ny användare](./media/thoughtworks-mingle-tutorial/ic785163.png "ny användare")  
 
    a. Skriv den **inloggningsnamn**, **visningsnamn**, **Välj lösenord**, **Bekräfta lösenord** av en giltig Azure AD-konto som du vill etablera i den relaterade textrutor. 

    b. Som **användartyp**väljer **användarens fullständiga**.

    c. Klicka på **skapa den här profilen**.

>[!NOTE]
>Du kan använda alla andra Thoughtworks Mingle användare konto verktyg för att skapa eller API: er som tillhandahålls av Thoughtworks Mingle att etablera AAD-användarkonton.
> 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Thoughtworks Mingle i åtkomstpanelen, bör det vara loggas in automatiskt till Thoughtworks Mingle som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

