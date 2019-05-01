---
title: 'Självstudier: Azure Active Directory-integrering med Spotinst | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f0d42562b1c927fe0f87ddc87b5e097022d2be2
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922084"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Självstudier: Azure Active Directory-integrering med Spotinst

I den här självstudien får du lära dig hur du integrerar Spotinst med Azure Active Directory (AD Azure).
Integrera Spotinst med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Spotinst.
* Du kan aktivera användarna att vara automatiskt inloggad till Spotinst (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Spotinst, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Spotinst enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Spotinst **SP och IDP** -initierad SSO

## <a name="adding-spotinst-from-the-gallery"></a>Att lägga till Spotinst från galleriet

För att konfigurera integrering av Spotinst i Azure AD, som du behöver lägga till Spotinst från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Spotinst från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Spotinst**väljer **Spotinst** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Spotinst i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Spotinst baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Spotinst upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Spotinst, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Spotinst Single Sign-On](#configure-spotinst-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Spotinst](#create-spotinst-test-user)**  – du har en motsvarighet för Britta Simon i Spotinst som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Spotinst:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Spotinst** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Spotinst domän och URL: er med enkel inloggning för information](common/idp-preintegrated-relay.png)

    a. Kontrollera **ange ytterligare webbadresser**.

    b. I den **Vidarebefordransstatus** textrutan, ange ett värde: `<ID>`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Spotinst domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I rutan **Inloggnings-URL** anger du följande URL: `https://console.spotinst.com`

    > [!NOTE]
    > Vidarebefordransstatus-värdet är inte verkliga. Vidarebefordransstatus värdet uppdateras med det faktiska värdet Vidarebefordransstatus beskrivs senare i självstudien.

6. Spotinst program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg: 

    | Namn | Källattribut|
    | -----| --------------- |
    | E-post | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

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

9. På den **konfigurera Spotinst** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-spotinst-single-sign-on"></a>Konfigurera Spotinst Single Sign-On

1. I ett annat webbläsarfönster, loggar du in Spotinst som en administratör.

2. Klicka på den **Användarikon** på upp till höger på skärmen och klicka på **inställningar**.

    ![Spotinst inställningar](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Klicka på den **SECURITY** fliken längst upp och välj sedan **Identitetsprovidrar** och utför följande steg:

    ![Spotinst säkerhet](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Kopiera den **Vidarebefordransstatus** för din instans och klistra in den i **Vidarebefordransstatus** -textrutan i **SAML grundkonfiguration** avsnittet på Azure-portalen.

    b. Klicka på **Bläddra** att ladda upp den xml-fil som du har hämtat från Azure-portalen

    c. Klicka på **SPARA**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Spotinst.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Spotinst**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Spotinst**.

    ![Länken Spotinst i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-spotinst-test-user"></a>Skapa Spotinst testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Spotinst.

1. Om du har konfigurerat programmet i den **SP** initierade läge, utför följande steg:

   a. I ett annat webbläsarfönster, loggar du in Spotinst som en administratör.

   b. Klicka på den **Användarikon** på upp till höger på skärmen och klicka på **inställningar**.

    ![Spotinst inställningar](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Klicka på **användare** och välj **Lägg till användare**.

    ![Spotinst inställningar](./media/spotinst-tutorial/adduser1.png)

    d. Utför följande steg på avsnittet Lägg till användare:

    ![Spotinst inställningar](./media/spotinst-tutorial/adduser2.png)

    * I den **fullständigt namn** textrutan anger du det fullständiga namnet på användaren som **BrittaSimon**.

    * I den **e-post** textrutan anger du e-postadressen för användaren som `brittasimon\@contoso.com`.

    * Välj din specifika information för den **konton, Kontorollen och organisation rollen**.

2. Om du har konfigurerat programmet i den **IDP** initierad läge, där är ingen uppgift åt dig i det här avsnittet. Spotinst stöder just-in-time-etablering, vilket är som standard aktiverat. En ny användare har skapats under ett försök att komma åt Spotinst om det inte finns ännu.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Spotinst i åtkomstpanelen, bör det vara loggas in automatiskt till Spotinst som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

