---
title: 'Självstudier: Azure Active Directory-integrering med Comm100 Live Chat | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Comm100 Live Chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 203c082275dc75a7dcf948eb42a383300955f355
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858107"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Självstudier: Azure Active Directory-integrering med Comm100 Live Chat

I den här självstudien lär du dig att integrera Comm100 Live Chat med Azure Active Directory (AD Azure).
Integreringen av Comm100 Live Chat med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Comm100 Live Chat.
* Du kan göra så att dina användare automatiskt loggas in på Comm100 Live Chat (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Comm100 Live Chat behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Comm100 Live Chat-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Comm100 Live Chat har stöd för **SP**-initierad enkel inloggning

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Lägga till Comm100 Live Chat från galleriet

För att konfigurera integreringen av Comm100 Live Chat i Azure AD måste du lägga till Comm100 Live Chat från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Comm100 Live Chat från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Comm100 Live Chat**, väljer **Comm100 Live Chat** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Comm100 Live Chat i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Comm100 Live Chat baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Comm100 Live Chat upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med Comm100 Live Chat behöver du utföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Comm100 Live Chat](#configure-comm100-live-chat-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Comm100 Live Chat-testanvändare](#create-comm100-live-chat-test-user)** – för att skapa en motsvarighet till Britta Simon i Comm100 Live Chat som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Comm100 Live Chat:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för **Comm100 Live Chat**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Comm100 Live Chat-domän och -URL:er](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkligt. Du uppdaterar värdet för inloggnings-URL med den faktiska inloggnings-URL:en, vilket förklaras senare i självstudien.

5. Comm100 Live Chat-programmet förväntar sig SAML-försäkran i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg: 

    | Namn |  Källattribut|
    | ---------------| --------------- |
    |   e-post    | user.mail |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Comm100 Live Chat** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-comm100-live-chat-single-sign-on"></a>Konfigurera enkel inloggning för Comm100 Live Chat

1. I ett annat webbläsarfönster loggar du in på Comm100 Live Chat som säkerhetsadministratör.

1. Längst upp till höger på sidan klickar du på **Mitt konto**.

   ![Comm100 Live Chat – myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. Till vänster i menyn klickar du på **Security** och sedan på **Agent Single Sign-On** (Enkel inloggning för agent).

   ![Comm100 Live Chat-säkerhet](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. På sidan **Agent Single Sign-On** (Enkel inloggning för agent) utför du följande steg:

   ![Comm100 Live Chat-säkerhet](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Kopiera den första markerade länken och klistra in den textrutan **Inloggnings-URL** i avsnittet **Comm100 Live Chat Domain and URLs** (Comm100 Live Chat-domän och -URL:er) i Azure-portalen.

   b. I textrutan **SAML SSO URL** (URL för enkel inloggning med SAML) klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.

   c. I textrutan **Remote Logout URL** (URL för fjärrutloggning) klistrar du in det värde för **Utloggnings-URL** som du har kopierat från Azure-portalen.

   d. Klicka på **Välj en fil** för att ladda upp det base-64-kodade certifikat som du har laddat ned från Azure-portalen till **certifikatet**.

   e. Klicka på **Spara ändringar**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Comm100 Live Chat.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Comm100 Live Chat**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Comm100 Live Chat**.

    ![Länken för Comm100 Live Chat i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-comm100-live-chat-test-user"></a>Comm100 Live Chat-testanvändare

För att Azure AD-användare ska kunna logga in i Comm100 Live Chat måste de etableras till Comm100 Live Chat. I Comm100 Live Chat är etablering en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Comm100 Live Chat som administratör.

2. Längst upp till höger på sidan klickar du på **Mitt konto**.

    ![Comm100 Live Chat – myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. Till vänster i menyn klickar du på **Agenter** och sedan på **Ny agent**.

    ![Comm100 Live Chat-agent](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. På sidan **Ny agent** utför du följande steg:

    ![Comm100 Live Chat – ny agent](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. I **e-post** text, ange den e-postadressen för användaren som **Brittasimon\@contoso.com**.

    b. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    c. I textrutan **Efternamn** anger du efternamnet på användaren som **simon**.

    d. I rutan **Visningsnamn** anger du visningsnamnet för användaren: **Britta simon**

    e. I textrutan **Lösenord** skriver du ditt lösenord.

    f. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Comm100 Live Chat-panelen i åtkomstpanelen bör du automatiskt loggas in på Comm100 Live Chat som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

