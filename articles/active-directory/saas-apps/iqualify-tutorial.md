---
title: 'Självstudier: Azure Active Directory-integrering med iQualify LMS | Microsoft Docs'
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
ms.openlocfilehash: 95c24f74e9af4443db994a6655a82108de18efdd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59280153"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Självstudier: Azure Active Directory-integrering med iQualify LMS

I den här självstudien får du lära dig hur du integrerar iQualify LMS med Azure Active Directory (AD Azure).
Integrera iQualify LMS med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till iQualify LMS.
* Du kan aktivera användarna att vara automatiskt inloggad till iQualify LMS (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med iQualify LMS, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* iQualify LMS-enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* har stöd för iQualify LMS **SP och IDP** -initierad SSO
* har stöd för iQualify LMS **Just In Time** etableringen av användare

## <a name="adding-iqualify-lms-from-the-gallery"></a>Att lägga till iQualify LMS från galleriet

För att konfigurera integrering av iQualify LMS i Azure AD, som du behöver lägga till iQualify LMS från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till iQualify LMS från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **iQualify LMS**väljer **iQualify LMS** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![iQualify LMS i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med iQualify LMS baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning att fungera, en länk förhållandet mellan en Azure AD-användare och relaterade användaren i iQualify LMS måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med iQualify LMS, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera iQualify LMS-enkel inloggning](#configure-iqualify-lms-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa iQualify LMS testanvändare](#create-iqualify-lms-test-user)**  – du har en motsvarighet för Britta Simon i iQualify LMS som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med iQualify LMS:

1. I den [Azure-portalen](https://portal.azure.com/)på den **iQualify LMS** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![information om iQualify LMS-domän och URL: er enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:
    | |
    |--|--|
    | Produktionsmiljö: `https://<yourorg>.iqualify.com/`|
    | Test-miljö: `https://<yourorg>.iqualify.io`|

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:
    | |
    |--|--|
    | Produktionsmiljö: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Test-miljö: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![information om iQualify LMS-domän och URL: er enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:
    | |
    |--|--|
    | Produktionsmiljö: `https://<yourorg>.iqualify.com/login` |
    | Test-miljö: `https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [iQualify LMS-klienten supportteamet](https://www.iqualify.com/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. IQualify LMS-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen  **Redigera** för att öppna dialogrutan **Användarattribut** .

    ![image](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Namn | Källattribut|
    | --- | --- |
    | e-post | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | ”din attributet” |

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
    > Den **person_id** attributet är **valfritt**

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. På den **konfigurera iQualify LMS** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-iqualify-lms-single-sign-on"></a>Konfigurera iQualify LMS-enkel inloggning

1. Öppna ett nytt webbläsarfönster och logga sedan in på din iQualify miljö som administratör.

1. När du är inloggad, klicka på din avatar längst upp till höger och sedan klicka på **kontoinställningar**

    ![Kontoinställningar](./media/iqualify-tutorial/setting1.png)

1. I området konto inställningar klickar du på menyfliksområdet-menyn till vänster och klicka på **INTEGRERINGAR**

    ![INTEGRERINGAR](./media/iqualify-tutorial/setting2.png)

1. Under INTEGRERINGAR, klickar du på den **SAML** ikon.

    ![SAML-ikon](./media/iqualify-tutorial/setting3.png)

1. I den **SAML autentiseringsinställningar** dialogrutan utför följande steg:

    ![Inställningar för SAML-autentisering](./media/iqualify-tutorial/setting4.png)

    a. I den **URL för SAML enkel inloggnings-tjänsten** rutan, klistra in den **inloggnings-URL** värdet kopieras från konfigurationsfönstret för Azure AD-program.

    b. I den **URL för SAML-UTLOGGNING** rutan, klistra in den **URL för utloggning** värdet kopieras från konfigurationsfönstret för Azure AD-program.

    c. Öppna filen nedladdade certifikatet i anteckningar, kopiera innehållet och klistra in den i den **OFFENTLIGT certifikat** box.

    d. I **etikett för inloggning** anger du namnet för knappen som ska visas på inloggningssidan.

    e. Klicka på **SPARA**.

    f. Klicka på **uppdatering**.

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

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till iQualify LMS.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **iQualify LMS**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **iQualify LMS**.

    ![IQualify LMS-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-iqualify-lms-test-user"></a>Skapa iQualify LMS testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i iQualify LMS. iQualify LMS stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i iQualify LMS, skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på iQualify LMS-panelen i åtkomstpanelen, bör du få inloggningssidan programmets iQualify LMS. 

   ![inloggningssidan](./media/iqualify-tutorial/login.png) 

Klicka på **inloggning med Azure AD** du bör få automatiskt loggat in på ditt iQualify LMS-program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)