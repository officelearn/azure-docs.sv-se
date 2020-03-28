---
title: 'Självstudiekurs: Azure Active Directory-integrering med Jitbit Helpdesk | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jitbit Helpdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 29addcd62afd193af83196b2d942e9778ff3f031
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099415"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Självstudiekurs: Azure Active Directory-integrering med Jitbit Helpdesk

I den här självstudien får du lära dig hur du integrerar Jitbit Helpdesk med Azure Active Directory (Azure AD).
Genom att integrera Jitbit Helpdesk med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Jitbit Helpdesk.
* Du kan aktivera dina användare så att de automatiskt loggas in på Jitbit Helpdesk (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Jitbit Helpdesk behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Jitbit Helpdesk enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Jitbit Helpdesk stöder **SP** initierade SSO

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Lägga till Jitbit Helpdesk från galleriet

Om du vill konfigurera integreringen av Jitbit Helpdesk i Azure AD måste du lägga till Jitbit Helpdesk från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Jitbit Helpdesk från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Jitbit Helpdesk**i sökrutan och välj **Jitbit Helpdesk** från resultatpanelen och klicka sedan på **Lägg** till-knappen för att lägga till programmet.

     ![Jitbit Helpdesk i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med Jitbit Helpdesk baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Jitbit Helpdesk upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Jitbit Helpdesk måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Jitbit Helpdesk Single Sign-On](#configure-jitbit-helpdesk-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Jitbit Helpdesk-testanvändare](#create-jitbit-helpdesk-test-user)** – om du vill ha en motsvarighet till Britta Simon i Jitbit Helpdesk som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Jitbit Helpdesk:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning på sidan **Jitbit Helpdesk-programintegration** . **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Jitbit Helpdesk-domän och webbadresser med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Jitbit Helpdesk Client supportteam](https://www.jitbit.com/support/) för att få det här värdet.

    b. Skriv en URL enligt följande i textrutan **Identifierare (enhets-ID):**`https://www.jitbit.com/web-helpdesk/`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Jitbit Helpdesk** kopierar du lämpliga webbadresser enligt dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Konfigurera Jitbit Helpdesk Enkel inloggning

1. I ett annat webbläsarfönster loggar du in på jitbithjälpföretagets webbplats som administratör.

1. Klicka på **Administration**i verktygsfältet högst upp .

    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administration")

1. Klicka på **Allmänna inställningar**.

    ![Användare, företag och behörigheter](./media/jitbit-helpdesk-tutorial/ic777680.png "Användare, företag och behörigheter")

1. Gör följande i konfigurationsavsnittet **för autentiseringsinställningar:**

    ![Inställningar för autentisering](./media/jitbit-helpdesk-tutorial/ic777683.png "Inställningar för autentisering")

    a. Välj **Aktivera SAML 2.0 enkel inloggning**för att logga in med Enkel inloggning (SSO), med **OneLogin**.

    b. Klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan url till **Slutpunkten.**

    c. Öppna **ditt bas-64-kodade** certifikat i anteckningar, kopiera innehållet i det i Urklipp och klistra sedan in det i textrutan **X.509 Certifikat**

    d. Klicka på **Spara ändringar**.

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

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Jitbit Helpdesk.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Jitbit Helpdesk**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Jitbit Helpdesk i**programlistan .

    ![Länken Jitbit Helpdesk i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-jitbit-helpdesk-test-user"></a>Skapa Jitbit Helpdesk-testanvändare

För att Azure AD-användare ska kunna logga in på Jitbit Helpdesk måste de etableras i Jitbit Helpdesk. När det gäller Jitbit Helpdesk är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **Jitbit Helpdesk-klient.**

1. Klicka på **Administration**på menyn högst upp .

    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administration")

1. Klicka på **Användare, företag och behörigheter**.

    ![Användare, företag och behörigheter](./media/jitbit-helpdesk-tutorial/ic777682.png "Användare, företag och behörigheter")

1. Klicka på **Lägg till användare**.

    ![Lägg till användare](./media/jitbit-helpdesk-tutorial/ic777685.png "Lägga till användare")

1. I avsnittet Skapa skriver du data för det Azure AD-konto som du vill etablera på följande sätt:

    ![Skapa](./media/jitbit-helpdesk-tutorial/ic777686.png "Skapa")

   a. Skriv användarnamnet för användaren som **BrittaSimon**i textrutan **Användarnamn** .

   b. Skriv **Email** e-postmeddelande för användaren som **BrittaSimon@contoso.com**.

   c. Skriv förnamnet för användaren som **Britta**i textrutan **Förnamn** .

   d. Skriv efternamn för användaren som **Simon**i textrutan **Efternamn** .

   e. Klicka på **Skapa**.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa ett annat Jitbit Helpdesk-användarkonton eller API:er som tillhandahålls av Jitbit Helpdesk för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Jitbit Helpdesk på åtkomstpanelen bör du automatiskt loggas in på den Jitbit-helpdesk som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
