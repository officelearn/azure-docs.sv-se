---
title: 'Självstudier: Azure Active Directory-integrering med Salesforce | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 9d9f9d1e9a87802960fd2f004f3e19f47fdd3fe0
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971809"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Självstudier: Azure Active Directory-integrering med Salesforce

I den här självstudien får du lära dig hur du integrerar Salesforce med Azure Active Directory (Azure AD).
Du får följande fördelar när du integrerar Salesforce med Azure AD:

* Du kan styra i Azure AD vilka som har åtkomst till Salesforce.
* Du kan göra så att dina användare automatiskt loggas in på Salesforce (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure Portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna konfigurera Azure AD-integrering med Salesforce behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Salesforce-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Salesforce har stöd för **SP**-initierad enkel inloggning

* Salesforce stöder **just-in-time**-användaretablering

* Salesforce har stöd för [**automatisk** användaretablering](salesforce-provisioning-tutorial.md)

## <a name="adding-salesforce-from-the-gallery"></a>Lägga till Salesforce från galleriet

För att kunna konfigurera integreringen av Salesforce i Azure AD måste du lägga till Salesforce från galleriet i din lista över hanterade SaaS-appar.

**Gör följande för att lägga till Salesforce från galleriet:**

1. I **[Azure Portal](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Salesforce** i sökrutan, välj **Salesforce** på resultatpanelen och klicka på knappen **Lägg till** i programmet.

     ![Salesforce i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Salesforce baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Salesforce upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Salesforce måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Salesforce](#configure-salesforce-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Skapa Salesforce-testanvändare](#create-salesforce-test-user)** – så att det finns en motsvarighet till Britta Simon i Salesforce som är länkad till Azure AD-representationen av användaren.
5. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure Portal.

Gör följande för att konfigurera enkel inloggning med Azure AD med Salesforce:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **Salesforce** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Salesforce – enkel inloggning-information för domän och -URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du värdet med följande mönster:

    Enterprise-konto: `https://<subdomain>.my.salesforce.com`

    Developer-konto: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. I textrutan **Identifierare** anger du värdet med följande mönster:

    Enterprise-konto: `https://<subdomain>.my.salesforce.com`

    Developer-konto: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren. Kontakta [Salesforce-kundsupporten](https://help.salesforce.com/support) för att få dessa värden.

5. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Salesforce** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-salesforce-single-sign-on"></a>Konfigurera enkel inloggning med Salesforce

1. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-administratörskonto.

2. Klicka på **Setup** (Konfiguration) under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/configure1.png)

3. Rulla ned till **INSTÄLLNINGAR** i navigeringsfönstret och klicka på **Identitet** för att expandera det relaterade avsnittet. Klicka sedan på **Inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-admin-sso.png)

4. På sidan med **Inställningar för enkel inloggning** klickar du på **Redigera**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Om du inte kan aktivera inställningar för enkel inloggning för ditt Salesforce-konto kanske du måste kontakta [Salesforce-kundsupporten](https://help.salesforce.com/support).

5. Välj **SAML-aktiverat** och klicka på **Spara**.

      ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-enable-saml.png)

6. Konfigurera inställningarna för enkel inloggning med SAML genom att klicka på **Nytt från metadatafil**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. Klicka på **Välj fil** för att ladda upp XML-filen med metadata som du har laddat ned från Azure-portalen och klicka på **Skapa**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/xmlchoose.png)

8. På sidan med **inställningar för enkel inloggning med SAML** fylls fälten i automatiskt. Klicka på Spara.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/salesforcexml.png)

9. I det vänstra navigeringsfönstret i Salesforce klickar du på **Företagsinställningar** för att expandera relaterat avsnitt och klickar sedan på **Min domän**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-my-domain.png)

10. Rulla ned till avsnittet **Autentiseringskonfiguration** och klicka på knappen **Redigera**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. I avsnittet **Autentiseringskonfiguration** markerar du **AzureSSO** vid **Autentiseringstjänst** för din SAML SSO-konfiguration och klickar sedan på **Spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Om mer än en autentiseringstjänst har valts uppmanas användarna att välja vilken autentiseringstjänst de vill logga in med när de initierar enkel inloggning i din Salesforce-miljö. Om du inte vill att det ska hända ska du **låta alla andra autentiseringstjänster vara avmarkerade**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure Portal med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure Portal och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

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

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till Salesforce.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Salesforce**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Salesforce** i programlistan.

    ![Salesforce-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-salesforce-test-user"></a>Skapa Salesforce-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Salesforce. Salesforce stöder just-in-time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Salesforce skapas en ny när du försöker komma åt Salesforce. Salesforce stöder även automatisk användaretablering. Mer information finns [här](salesforce-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Salesforce-panelen i åtkomstpanelen bör du automatiskt loggas in på Salesforce som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Konfigurera användaretablering](salesforce-provisioning-tutorial.md)