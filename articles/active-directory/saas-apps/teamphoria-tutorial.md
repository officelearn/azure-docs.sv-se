---
title: 'Självstudier: Azure Active Directory-integrering med Teamphoria | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 11d0a0a67d1ce5049166cab3d9ca3e4903b6b460
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59277212"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Självstudier: Azure Active Directory-integrering med Teamphoria

I den här självstudien får du lära dig hur du integrerar Teamphoria med Azure Active Directory (AD Azure).
Integrera Teamphoria med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Teamphoria.
* Du kan aktivera användarna att vara automatiskt inloggad till Teamphoria (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Teamphoria, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Teamphoria enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Teamphoria **SP** -initierad SSO

## <a name="adding-teamphoria-from-the-gallery"></a>Att lägga till Teamphoria från galleriet

För att konfigurera integrering av Teamphoria i Azure AD, som du behöver lägga till Teamphoria från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Teamphoria från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Teamphoria**väljer **Teamphoria** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Teamphoria i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Teamphoria baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Teamphoria upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Teamphoria, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Teamphoria Single Sign-On](#configure-teamphoria-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Teamphoria](#create-teamphoria-test-user)**  – du har en motsvarighet för Britta Simon i Teamphoria som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Teamphoria:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Teamphoria** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Teamphoria domän och URL: er med enkel inloggning för information](common/sp-intiated.png)

    I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Du måste uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Teamphoria klienten supportteamet](https://www.teamphoria.com/) att hämta inloggnings URL. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera Teamphoria** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-teamphoria-single-sign-on"></a>Konfigurera Teamphoria Single Sign-On

1. Att konfigurera enkel inloggning på **Teamphoria** sida, logga in på ditt Teamphoria program som administratör.

1. Gå till **ADMINISTRATÖRSINSTÄLLNINGAR** alternativet i det vänstra verktygsfältet och på fliken Konfigurera klickar du på **enkel inloggning** att öppna fönstret SSO-konfiguration.

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/admin_sso_configure.png)

1. Klicka på **lägga till nya IDENTITETSPROVIDER** alternativet i det övre högra hörnet för att öppna formuläret för att lägga till inställningar för enkel inloggning.

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. Ange information i fälten som beskrivs nedan-

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **VISNINGSNAMN**: Ange visningsnamnet för plugin-programmet på sidan.

    b. **KNAPPNAMN**: Namnet på fliken som visas på inloggningssidan för att logga in via enkel inloggning.

    c. **CERTIFIKATET**: Öppna certifikatet hämtade tidigare från Azure-portalen i anteckningar, kopiera innehållet i samma och klistra in det här i rutan.

    d. **STARTPUNKTEN**: Klistra in den **inloggnings-URL** kopierade tidigare från Azure-portalen.

    e. Växla kan **på** och klicka på **spara**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Teamphoria.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Teamphoria**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Teamphoria**.

    ![Länken Teamphoria i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-teamphoria-test-user"></a>Skapa Teamphoria testanvändare

För att aktivera Azure AD-användare att logga in på Teamphoria, måste de etableras i Teamphoria. När det gäller Teamphoria är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Teamphoria företag som administratör.

1. Klicka på **ADMIN** inställningar i det vänstra verktygsfältet och under den **hantera** fliken klickar du på på **användare** att öppna sidan för användare.

    ![Lägga till medarbetare](./media/teamphoria-tutorial/admin_manage_users.png)

1. Klicka på den **manuell bjuda in** alternativet.

    ![Bjuda in](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. På den här sidan kan du utföra följande åtgärd.

    ![Bjuda in](./media/teamphoria-tutorial/manual_user_invite.png)

    a. I den **e-postadress** textrutan anger du den **e-postadress** för användaren som BrittaSimon.

    b. I den **Förnamn** textrutan Ange först namnet på användaren som **Britta**.

    c. I den **efternamn** textrutan Ange efternamn för användaren som **Simon**.

    d. Klicka på **inbjudan 1 användare**. Användare måste acceptera inbjudan att skapas i systemet.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Teamphoria i åtkomstpanelen, bör det vara loggas in automatiskt till Teamphoria som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

