---
title: 'Självstudiekurs: Azure Active Directory-integrering med iQualify LMS | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: a1db4784eb63df14b7e7971d0273512ba657df96
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944997"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Självstudiekurs: Azure Active Directory-integrering med iQualify LMS

I den här självstudien får du lära dig hur du integrerar iQualify LMS med Azure Active Directory (Azure AD).
Genom att integrera iQualify LMS med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till iQualify LMS.
* Du kan aktivera dina användare så att de automatiskt loggas in för att kvalificera LMS (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med iQualify LMS behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* iKvalificera LMS enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* iKvalificera LMS stöder **SP och IDP** initierade SSO
* iQualify LMS stöder just in time-användaretablering **Just In Time**

## <a name="adding-iqualify-lms-from-the-gallery"></a>Lägga till iQualify LMS från galleriet

Om du vill konfigurera integreringen av iQualify LMS i Azure AD måste du lägga till iQualify LMS från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till iQualify LMS från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **iKvalificera LMS**i sökrutan och välj **kvalificera LMS** från resultatpanelen och klicka sedan på **Lägg** till knappen för att lägga till programmet.

     ![iKvalificera LMS i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du en enda Azure AD-inloggning med iQualify LMS baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i iQualify LMS upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med iQualify LMS måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera iKvalificera LMS Enkel inloggning](#configure-iqualify-lms-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa iQualify LMS-testanvändare](#create-iqualify-lms-test-user)** – om du vill ha en motsvarighet till Britta Simon i iQualify LMS som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med iQualify LMS:

1. Välj [Azure portal](https://portal.azure.com/)Enkel inloggning på sidan **kvalificera LMS-programintegration** på sidan Kvalificera LMS-program. **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![iKvalificera LMS-domän och webbadresser med enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  
    | |
    |--|--|
    | Produktionsmiljö:`https://<yourorg>.iqualify.com/`|
    | Testmiljö:`https://<yourorg>.iqualify.io`|

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:
    | |
    |--|--|
    | Produktionsmiljö:`https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Testmiljö:`https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![iKvalificera LMS-domän och webbadresser med enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:
    | |
    |--|--|
    | Produktionsmiljö:`https://<yourorg>.iqualify.com/login` |
    | Testmiljö:`https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [iQualify LMS Client support team](https://www.iqualify.com/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. I kvalificera LMS-programmet förväntar sig SAML-påståendena i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon om du vill öppna dialogrutan **Användarattribut.**

    ![image](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Namn | Källattribut|
    | --- | --- |
    | e-post | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "ditt attribut" |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

    > [!Note]
    > Attributet **person_id** är **valfritt**

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera kvalificera LMS** enligt dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-iqualify-lms-single-sign-on"></a>Konfigurera iQualify LMS Enkel inloggning

1. Öppna ett nytt webbläsarfönster och logga sedan in på din iQualify-miljö som administratör.

1. När du är inloggad klickar du på din avatar längst upp till höger och klickar sedan på **Kontoinställningar**

    ![Kontoinställningar](./media/iqualify-tutorial/setting1.png)

1. I området kontoinställningar klickar du på menyfliksområdet till vänster och klickar på **INTEGRATIONS**

    ![Integrationer](./media/iqualify-tutorial/setting2.png)

1. Klicka på **SAML-ikonen** under INTEGRATIONER.

    ![Ikon för SAML](./media/iqualify-tutorial/setting3.png)

1. Gör följande i dialogrutan **SAML-autentiseringsinställningar:**

    ![Inställningar för SAML-autentisering](./media/iqualify-tutorial/setting4.png)

    a. I url:en **SAML SINGLE SIGN-ON-TJÄNST-URL:en** klistrar du in värdet **för inloggnings-URL** som kopierats från konfigurationsfönstret för Azure AD-program.

    b. I rutan **SAML LOGOUT URL** klistrar du in **url-värdet för utloggning** som kopierats från konfigurationsfönstret för Azure AD-program.

    c. Öppna den nedladdade certifikatfilen i anteckningar, kopiera innehållet och klistra sedan in den i rutan **OFFENTLIGT CERTIFIKAT.**

    d. I **LOGIN BUTTON LABEL** ange namnet på knappen som ska visas på inloggningssidan.

    e. Klicka på **SPARA**.

    f. Klicka på **UPPDATERA**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till iQualify LMS.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **iQualify LMS**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **iQualify LMS**i programlistan .

    ![Länken kvalera LMS i listan Program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-iqualify-lms-test-user"></a>Skapa iQualify LMS-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i iQualify LMS. iKvalificera LMS stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i iQualify LMS skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på iQualify LMS-panelen i åtkomstpanelen bör du få inloggningssidan för ditt iQualify LMS-program. 

   ![inloggningssida](./media/iqualify-tutorial/login.png) 

Klicka **på Logga in med Azure AD-knappen** och du bör automatiskt logga in på ditt iQualify LMS-program.

Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)