---
title: 'Självstudiekurs: Azure Active Directory-integrering med Picturepark | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 08c5bd8da0dda74156b2d44c8106ed345ef749dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73177015"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Självstudiekurs: Azure Active Directory-integrering med Picturepark

I den här självstudien får du lära dig hur du integrerar Picturepark med Azure Active Directory (Azure AD).
Genom att integrera Picturepark med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Picturepark.
* Du kan aktivera dina användare så att de automatiskt loggas in på Picturepark (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Picturepark behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Bildpark enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Picturepark stöder **SP** initierade SSO

## <a name="adding-picturepark-from-the-gallery"></a>Lägga till Picturepark från galleriet

Om du vill konfigurera integreringen av Picturepark i Azure AD måste du lägga till Picturepark från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Picturepark från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Picturepark**i sökrutan och välj **Picturepark** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![Picturepark i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Picturepark baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Picturepark upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Picturepark måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Bildpark Enkel inloggning](#configure-picturepark-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Picturepark-testanvändare](#create-picturepark-test-user)** – om du vill ha en motsvarighet till Britta Simon i Picturepark som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Picturepark:

1. Välj Enkel inloggning på sidan Picturepark-programintegrering på [Azure-portalen](https://portal.azure.com/). **Picturepark** **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Picturepark Domän och webbadresser enkel inloggningsinformation](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.picturepark.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta Supportteamet för [Picturepark Client](https://picturepark.com/company/picturepark-customer-support) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **tumavtrycket** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Picturepark.** För **inloggnings-URL**använder du värdet med följande mönster:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ är klient-ID för Azure AD-prenumeration.

    ![Kopiera konfigurations-URL:er](./media/picturepark-tutorial/configurls.png)

    a. Azure AD-identifierare

    b. Utloggnings-URL

### <a name="configure-picturepark-single-sign-on"></a>Konfigurera bildpark enkel inloggning

1. I ett annat webbläsarfönster loggar du in på webbplatsen Picturepark som administratör.

2. Klicka på **Administrationsverktyg**i verktygsfältet högst upp och klicka sedan på **Hanteringskonsol.**
   
    ![Hanteringskonsol](./media/picturepark-tutorial/ic795062.png "Hanteringskonsol")

3. Klicka på **Autentisering**och sedan på **Identitetsleverantörer**.
   
    ![Autentisering](./media/picturepark-tutorial/ic795063.png "Autentisering")

4. Gör följande i konfigurationsavsnittet **för identitetsprovider:**
   
    ![Konfiguration av identitetsprovider](./media/picturepark-tutorial/ic795064.png "Konfiguration av identitetsprovider")
   
    a. Klicka på **Lägg till**.
  
    b. Skriv ett namn på konfigurationen.
   
    c. Välj **Ange som standard**.
   
    d. I **URI-textrutan Utfärdare** klistrar du in värdet **för inloggnings-URL:en** som du har kopierat från Azure-portalen.
   
    e. I textrutan **Betrodd utfärdare av tummen** klistrar du in värdet för **tumavtryck** som du har kopierat från avsnittet **SAML-signeringscertifikat.** 

5. Klicka på **JoinDefaultUsersGroup**.

6. Om du vill ange **attributet E-postadress** i textrutan **Anspråk** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` och klickar på **Spara**.

      ![Konfiguration](./media/picturepark-tutorial/ic795065.png "Konfiguration")

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Picturepark.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Picturepark**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Picturepark**i programlistan .

    ![Länken Picturepark i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-picturepark-test-user"></a>Skapa picturepark-testanvändare

För att Azure AD-användare ska kunna logga in på Picturepark måste de etableras i Picturepark. När det gäller Picturepark är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **Picturepark-klient.**

1. Klicka på **Administrationsverktyg**i verktygsfältet högst upp och klicka sedan på **Användare**.
   
    ![Användare](./media/picturepark-tutorial/ic795067.png "Användare")

1. Klicka på **Nytt**på fliken **Användares översikt** .
   
    ![Användarhantering](./media/picturepark-tutorial/ic795068.png "Användarhantering")

1. I dialogrutan **Skapa användare** utför du följande steg i en giltig Azure Active Directory-användare som du vill etablera:
   
    ![Skapa användare](./media/picturepark-tutorial/ic795069.png "Skapa användare")
   
    a. Skriv användarens `BrittaSimon@contoso.com`e-postadress i textrutan **E-postadress** . **email address**  
   
    b. Skriv **lösenordet** för BrittaSimon i textrutorna **Lösenord** och **Bekräfta lösenord.** 
   
    c. Skriv **förnamnet för** användaren **Britta**i textrutan **Förnamn** . 
   
    d. Skriv **efternamnet** för användaren **Simon**i textrutan **Efternamn** .
   
    e. Skriv **användarens företagsnamn** i textrutan **För** företag. 
   
    f. Välj användarens **land/region** i textrutan **Land.**
  
    g. Skriv orts **postnummer** i **textrutan.**
   
    h. Skriv namnet på den ort som ska vara känd för användaren i textrutan **Ort.** **City name**

    i. Välj ett **språk**.
   
    j. Klicka på **Skapa**.

>[!NOTE]
>Du kan använda andra verktyg eller API:er för att skapa bilder eller API:er för att etablera Azure AD-användarkonton.
>

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Picturepark på Åtkomstpanelen bör du automatiskt loggas in i den Picturepark som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

