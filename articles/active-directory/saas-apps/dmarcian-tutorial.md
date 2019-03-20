---
title: 'Självstudier: Azure Active Directory-integrering med dmarcian | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c24cbf8ad21c7dd5875a71532a5278e313774e66
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57883975"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Självstudier: Azure Active Directory-integrering med dmarcian

I den här självstudien lär du dig att integrera dmarcian med Azure Active Directory (AD Azure).
Integreringen av dmarcian med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till dmarcian.
* Du kan göra så att dina användare automatiskt loggas in på dmarcian (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med dmarcian behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* dmarcian-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* dmarcian har stöd för **SP**- och **IDP**-initierad enkel inloggning

## <a name="adding-dmarcian-from-the-gallery"></a>Lägga till dmarcian från galleriet

För att konfigurera integrering av dmarcian i Azure AD behöver du lägga till dmarcian från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till dmarcian från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **dmarcian**, väljer **dmarcian** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![dmarcian i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD till dmarcian baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i dmarcian upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med dmarcian behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för dmarcian](#configure-dmarcian-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa dmarcian-testanvändare](#create-dmarcian-test-user)** – för att ha en motsvarighet för Britta Simon i dmarcian som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för dmarcian:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **dmarcian** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![dmarcian-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![dmarcian-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Du kommer att uppdatera de här värdena med faktisk identifierare, svars-URL och inloggnings-URL. Detta förklaras senare i självstudien. 

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-dmarcian-single-sign-on"></a>Konfigurera enkel inloggning för dmarcian

1. I ett annat webbläsarfönster loggar du in på dmarcian som säkerhetsadministratör.

2. Klicka på **Profil** i det övre högra hörnet och navigera till **Inställningar**.

    ![Inställningarna](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

3. Rulla nedåt och klicka på avsnittet **Enkel inloggning** följt av **Konfigurera**.

    ![Enskild](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

4. På sidan **Enkel inloggning med SAML** anger du **Status** till **Aktiverad** och utför följande steg:

    ![Autentiseringen](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * I avsnittet **Add dmarcian to your Identity Provider** (Lägg till dmarcian i identitetsprovidern) klickar du på **COPY** (Kopiera) för att kopiera **URL för konsumenttjänst för försäkran** för din instans och klistrar in den i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    * I avsnittet **Add dmarcian to your Identity Provider** (Lägg till dmarcian i identitetsprovidern) klickar du på **COPY** (Kopiera) för att kopiera **Entitets-ID** för din instans och klistrar in den i textrutan **Identifier** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    * I avsnittet **Konfigurera autentisering** klistrar du i textrutan **Identity Provider Metadata** (Metadata för identitetsprovider) in den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen.

    * I avsnittet **Konfigurera autentisering** klistrar du i textrutan **Attribute Statements** (Attributinstruktioner) in URL:en `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * I avsnittet **Set up Login URL** (Konfigurera inloggnings-URL) kopierar du **Inloggnings-URL** för din instans och klistrar in den i textrutan **Inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

        > [!Note]
        > Du kan ändra **Inloggnings-URL** enligt din organisation.

    * Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till dmarcian.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **dmarcian**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **dmarcian**.

    ![Länken för dmarcian i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-dmarcian-test-user"></a>Skapa dmarcian-testanvändare

För att göra det möjligt för Azure AD-användare att logga in i dmarcian måste de etableras till dmarcian. I dmarcian är etablering en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på dmarcian som säkerhetsadministratör.

2. Klicka på **Profil** i det övre högra hörnet och gå till **Hantera användare**.

    ![Användaren](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. På höger sida av avsnittet **SSO Users** (Användare av enkel inloggning) klickar du på **Lägg till ny användare**.

    ![Lägg till användare](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. I popup-fönstret **Lägg till ny användare** utför du följande steg:

    ![Den nya användaren](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. I den **nya användarens e-postadress** textrutan Ange e-postadress för användaren som **brittasimon\@contoso.com**.

    b. Om du vill ge administratörsrättigheter till användaren väljer du **Make User an Admin** (Gör användaren till administratör).

    c. Klicka på **Lägg till användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på dmarcian-panelen i åtkomstpanelen bör du automatiskt loggas in på dmarcian som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

