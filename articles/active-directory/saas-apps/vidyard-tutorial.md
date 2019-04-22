---
title: 'Självstudier: Azure Active Directory-integrering med Vidyard | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 94e5893caa4dc19702f45e9b42727aab4f884bcd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273863"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Självstudier: Azure Active Directory-integrering med Vidyard

I den här självstudien får du lära dig hur du integrerar Vidyard med Azure Active Directory (AD Azure).
Integrera Vidyard med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Vidyard.
* Du kan aktivera användarna att vara automatiskt inloggad till Vidyard (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Vidyard, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Vidyard enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Vidyard **SP** och **IDP** -initierad SSO

* Har stöd för Vidyard **Just In Time** etableringen av användare

## <a name="adding-vidyard-from-the-gallery"></a>Att lägga till Vidyard från galleriet

För att konfigurera integrering av Vidyard i Azure AD, som du behöver lägga till Vidyard från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Vidyard från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Vidyard**väljer **Vidyard** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Vidyard i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Vidyard baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Vidyard upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Vidyard, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Vidyard Single Sign-On](#configure-vidyard-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Vidyard](#create-vidyard-test-user)**  – du har en motsvarighet för Britta Simon i Vidyard som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Vidyard:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Vidyard** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Vidyard domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Vidyard domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Dessa värden uppdateras med de faktiska identifierare, svars-URL och inloggnings-URL, vilket beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. På den **konfigurera Vidyard** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-vidyard-single-sign-on"></a>Konfigurera Vidyard Single Sign-On

1. I ett annat webbläsarfönster, loggar du in din Vidyard programvara företagets webbplats som administratör.

2. Från instrumentpanelen Vidyard väljer **grupp** > **säkerhet**

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure1.png)

3. Klicka på **ny profil** fliken.

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure2.png)

4. I den **SAML-konfiguration** avsnittet, utför följande steg:

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure3.png)

    a. Ange allmänna profilnamn i den **profilnamn** textrutan.

    b. Kopiera **SSO-inloggning användarsidan** värde och klistra in den i **inloggnings-URL** -textrutan i **grundläggande SAML-konfiguration** avsnittet på Azure-portalen.

    c. Kopiera **ACS URL** värde och klistra in den i **svars-URL** -textrutan i **SAML grundkonfiguration** avsnittet på Azure-portalen.

    d. Kopiera **utfärdare/Metadata-URL** värde och klistra in den i **identifierare** textrutan i **SAML grundkonfiguration** avsnittet på Azure-portalen.

    e. Öppna filen nedladdade certifikatet från Azure-portalen i anteckningar och klistra in den i den **X.509-certifikat** textrutan.

    f. I den **slutpunkts-URL för SAML** textrutan klistra in värdet för **inloggnings-URL** kopieras från Azure-portalen.

    g. Klicka på **Bekräfta**.

5. Välj fliken Single Sign On **tilldela** bredvid en befintlig profil

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > När du har skapat en profil för enkel inloggning kan du tilldela den till alla grupper som användarna måste ha åtkomst via Azure. Om användaren inte finns i den grupp som de har tilldelats, Vidyard automatiskt skapa ett användarkonto och tilldela deras roll i realtid.

6. Välj Organisationsgrupp för din, som visas i den **grupper som är tillgängliga att distribuera**.

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure5.png)

7. Du kan se de tilldelade grupperna under den **för närvarande tilldelade grupper**. Välj en roll för gruppen enligt din organisation och klickar på **Bekräfta**.

    ![Vidyard konfiguration](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Mer information finns [det här dokumentet](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Vidyard.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Vidyard**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Vidyard**.

    ![Länken Vidyard i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-vidyard-test-user"></a>Skapa Vidyard testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i Vidyard. Vidyard stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Vidyard, skapas en ny efter autentisering.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Vidyard supportteamet](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Vidyard i åtkomstpanelen, bör det vara loggas in automatiskt till Vidyard som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

