---
title: 'Självstudier: Azure Active Directory-integrering med Trello | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Trello.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453827b42d12333fd4f27761e7f73484fb749532
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578432"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Självstudier: Azure Active Directory-integrering med Trello

I den här självstudien får du lära dig hur du integrerar Trello med Azure Active Directory (AD Azure).
När du integrerar Trello med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till Trello i Azure AD.
* Du kan aktivera användarna att vara automatiskt inloggad till Trello (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Trello behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).
* Ett Trello single-sign-på-aktiverade prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Trello stöder SP - och IDP-initierad SSO

* Trello har stöd för etablering av Just-In-Time-användare

## <a name="add-trello-from-the-gallery"></a>Lägg till Trello från galleriet

För att konfigurera integrering av Trello i Azure AD, först lägga till Trello från galleriet i din lista över hanterade SaaS-appar.

Om du vill lägga till Trello från galleriet, gör du följande:

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Välj **företagsprogram**, och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Trello**, och välj sedan **Trello** från resultatfönstret.

5. Välj den **Lägg till** för att lägga till programmet.

     ![Trello i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Trello baserat på en testanvändare kallas **Britta Simon**.

För enkel inloggning ska fungera, måste du upprätta en länk mellan en Azure AD-användare och relaterade användaren i Trello.

Om du vill konfigurera och testa Azure AD enkel inloggning med Trello, måste du utföra följande byggblock:

1. [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
2. [Konfigurera Trello enkel inloggning](#configure-trello-single-sign-on) att konfigurera inställningar för enkel inloggning på programsidan.
3. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
4. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
5. [Skapa en Trello-testanvändare](#create-a-trello-test-user) har en motsvarighet för Britta Simon i Trello som är länkad till en Azure AD-representation av användaren.
6. [Testa enkel inloggning](#test-single-sign-on) för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

> [!NOTE]
> Det dynamiska datafältet för **\<företaget\>** får du från Trello. Om du inte har värdet dynamiska datafältet kan kontakta den [Trello supportteamet](mailto:support@trello.com) att hämta det dynamiska datafältet för ditt företag.

Om du vill konfigurera Azure AD enkel inloggning med Trello, gör du följande:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Trello**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** dialogrutan **SAML** att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På den **Konfigurera enkel inloggning med SAML** väljer den **redigera** ikonen för att öppna den **SAML grundkonfiguration** dialogrutan.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I den **SAML grundkonfiguration** om du vill konfigurera programmet i IDP-initierad läge, gör följande:

    ![Trello-domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I den **identifierare** anger en URL med hjälp av följande mönster: `https://trello.com/auth/saml/metadata`

    b. I den **svars-URL** anger en URL med hjälp av följande mönster: `https://trello.com/auth/saml/consume/<enterprise>`

5. Välj **ange ytterligare webbadresser**, och sedan vidta följande steg om du vill konfigurera programmet i SP-initierat läge:

    ![Trello-domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I den **inloggnings-URL** anger en URL med hjälp av följande mönster:  `https://trello.com/auth/saml/login/<enterprise>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren svars-URL och inloggnings-URL. Kontakta den [Trello klienten supportteamet](mailto:support@trello.com) att hämta dessa värden. Du kan även gå till mönster i den **SAML grundkonfiguration** avsnitt i Azure-portalen.

6. Trello-program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På den **ange in enkel inloggning med SAML** väljer den **redigera** knappen för att öppna den **användarattribut** dialogrutan.

    ![Dialogrutan Användarattribut](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i föregående bild. Utför sedan följande steg:

    | Namn |  Källattribut|
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Välj **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Användardialogrutan för anspråk](common/new-save-attribute.png)

    ![Hantera användaranspråk](common/new-attribute-details.png)

    b. I den **namn** Anger attributets namn som visas för den raden.

    c. Lämna **Namespace** tom.

    d. För **källa**väljer **attributet**.

    e. I den **källattribut** listan, anger du attributvärdet som visas för den raden.

    f. Välj **OK**.

    g. Välj **Spara**.

8. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer **hämta** att ladda ned den **certifikat (Base64)**  från de angivna alternativen efter behov. Spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. På den **konfigurera Trello** avsnittet, kopiera den lämpliga URL: er baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-ID

    c. Utloggnings-URL

### <a name="configure-trello-single-sign-on"></a>Konfigurera Trello enkel inloggning

Om du vill konfigurera enkel inloggning på Trello-sida, först skicka de hämtade **certifikat (Base64)** och kopieras URL: er från Azure portal för att den [Trello supportteamet](mailto:support@trello.com). De säkerställer att SAML SSO-anslutningen är korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I den **användaren** dialogrutan rutan, vidta följande steg.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fältet, anger du ”brittasimon@yourcompanydomain.extension”. Exempel: i det här fallet, du kan ange ”BrittaSimon@contoso.com”.

    c. Välj den **Show lösenord** och anteckna värdet som visas i den **lösenord** box.

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Trello.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **Trello**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Trello** i listan över program.

    ![Trello-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj den **Lägg till användare** knappen. I den **Lägg till tilldelning** dialogrutan **användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** på användarlistan. Klicka sedan på den **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran sedan, i den **Välj roll** dialogrutan väljer du rätt roll för användaren i listan. Klicka sedan på den **Välj** längst ned på skärmen.

7. I den **Lägg till tilldelning** dialogrutan den **tilldela** knappen.

### <a name="create-a-trello-test-user"></a>Skapa en Trello-testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Trello. Trello stöder Just-in-Time etableringen av användare, som är aktiverad som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Trello skapas en ny efter autentisering.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du kontakta den [Trello supportteamet](mailto:support@trello.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av MyApps-portalen.

När du väljer panelen Trello MyApps-portalen kan bör du vara loggas in automatiskt till Trello. Läs mer om portalen Mina appar [vad är MyApps-portalen?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

