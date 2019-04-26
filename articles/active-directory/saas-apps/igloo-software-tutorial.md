---
title: 'Självstudier: Azure Active Directory-integrering med Igloo programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Igloo programvara.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: 60ec322758a3c3b83e7b24cfba37daaec02fc4c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60273952"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Självstudier: Azure Active Directory-integrering med Igloo programvara

I den här självstudien får du lära dig hur du integrerar Igloo programvara med Azure Active Directory (AD Azure).
Integrera Igloo programvara med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Igloo programvara.
* Du kan aktivera användarna att vara automatiskt inloggad Igloo programvara (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Igloo programvara, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Igloo programvara enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Stöder igloo program **SP** -initierad SSO
* Stöder igloo program **Just In Time** etableringen av användare

## <a name="adding-igloo-software-from-the-gallery"></a>Lägga till Igloo programvara från galleriet

För att konfigurera integrering av Igloo programvara i Azure AD, som du behöver lägga till Igloo programvara från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Igloo programvara från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Igloo programvara**väljer **Igloo programvara** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Igloo programvara i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Igloo programvara baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Igloo programvara ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Igloo programvara, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Igloo-programvara](#configure-igloo-software-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Igloo programvara testanvändare](#create-igloo-software-test-user)**  – du har en motsvarighet för Britta Simon Igloo programvara som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Igloo programvara:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Igloo programvara** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Igloo programvara domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<company name>.igloocommmunities.com`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `https://<company name>.igloocommmunities.com/saml.digest`

    c. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Igloo klientprogrammet supportteamet](https://www.igloosoftware.com/services/support) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Igloo programvara** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-igloo-software-single-sign-on"></a>Konfigurera enkel inloggning för Igloo-programvara

1. I ett annat webbläsarfönster logga du in på webbplatsen för företagets Igloo programvara som en administratör.

2. Gå till den **Kontrollpanelen**.

     ![Kontrollpanelen](./media/igloo-software-tutorial/ic799949.png "Kontrollpanelen")

3. I den **medlemskap** fliken **logga i inställningar**.

    ![Inloggningsinställningar](./media/igloo-software-tutorial/ic783968.png "inloggningsinställningar")

4. I avsnittet SAML-konfiguration klickar du på **konfigurera SAML-autentisering**.

    ![SAML-konfiguration](./media/igloo-software-tutorial/ic783969.png "SAML-konfiguration")

5. I den **Allmän konfiguration** avsnittet, utför följande steg:

    ![Allmän konfiguration](./media/igloo-software-tutorial/ic783970.png "Allmän konfiguration")

    a. I den **anslutningsnamn** textrutan skriver du ett eget namn för din konfiguration.

    b. I den **inloggnings-URL för IDP: N** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. I den **utloggnings-URL för IDP: N** textrutan klistra in värdet för **URL för utloggning** som du har kopierat från Azure-portalen.

    d. Välj **utloggning svar och typ av begäran HTTP** som **POST**.

    e. Öppna din **Base64-** kodade certifikatet i anteckningar som hämtats från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **offentligt certifikat** textrutan.

6. I den **svar och Autentiseringskonfiguration**, utför följande steg:

    ![Svar och Autentiseringskonfiguration](./media/igloo-software-tutorial/IC783971.png "svar och konfiguration av autentisering")
  
    a. Som **identitetsprovidern**väljer **Microsoft ADFS**.

    b. Som **typ av identifierare**väljer **e-postadress**. 

    c. I den **e-attributet** textrutan typ **e-postadress**.

    d. I den **förnamn attributet** textrutan typ **givenname**.

    e. I den **senaste namnattributet** textrutan typ **efternamn**.

7. Utför följande steg för att slutföra konfigurationen:

    ![Skapa användare på inloggningen](./media/igloo-software-tutorial/IC783972.png "skapa användare på Logga in") 

    a. Som **skapa användare på inloggningen**väljer **skapa en ny användare på din plats när de loggar in**.

    b. Som **inloggningsinställningar**väljer **Använd SAML-knappen ”Logga in” skärmen**.

    c. Klicka på **Spara**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Igloo programvara.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Igloo programvara**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Igloo programvara**.

    ![Länken Igloo programvara i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-igloo-software-test-user"></a>Skapa Igloo programvara testanvändare

Det finns inga uppgift att konfigurera användaretablering till Igloo programvara.  

När en tilldelad användare försöker logga in på Igloo programvara med hjälp av åtkomstpanelen, kontrollerar Igloo programvara om användaren finns.  Om det finns inget konto ännu, skapas den automatiskt med Igloo program.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Igloo programvara i åtkomstpanelen, bör det vara loggas in automatiskt till Igloo programvara som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)