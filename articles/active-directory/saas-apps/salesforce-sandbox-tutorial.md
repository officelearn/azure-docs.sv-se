---
title: 'Självstudier: Azure Active Directory-integrering med Salesforce Sandbox | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2940f3eee3112fe1c6d57cc92157c573ecad109
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722561"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Självstudier: Azure Active Directory-integrering med Salesforce Sandbox

I den här självstudien får du lära dig hur du integrerar Salesforce Sandbox med Azure Active Directory (AD Azure).

Sandboxar ger dig möjlighet att skapa flera kopior av din organisation i separata miljöer för olika syften, till exempel utveckling, testning och utbildning, utan att kompromissa med data och program i din Salesforce-produktion organisation.
Mer information finns i [Sandbox översikt](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Integrera Salesforce Sandbox med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Salesforce Sandbox.
* Du kan aktivera användarna att vara automatiskt inloggad till Salesforce begränsat läge (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Salesforce begränsat läge, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Salesforce-Sandbox enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Salesforce Sandbox **SP och IDP** -initierad SSO
* Har stöd för Salesforce Sandbox **Just In Time** etableringen av användare
* Har stöd för Salesforce Sandbox [ **automatisk** etableringen av användare](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Att lägga till Salesforce Sandbox från galleriet

Om du vill konfigurera integreringen av Salesforce begränsat till Azure AD, som du behöver lägga till Salesforce Sandbox från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Salesforce Sandbox från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Salesforce Sandbox**väljer **Salesforce Sandbox** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Salesforce-Sandbox i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Salesforce Sandbox baserat på en testanvändare kallas **Britta Simon**.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Salesforce begränsat till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Salesforce Sandbox upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Salesforce begränsat läge, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Salesforce-Sandbox](#configure-salesforce-sandbox-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Salesforce-Sandbox testanvändare](#create-salesforce-sandbox-test-user)**  – du har en motsvarighet för Britta Simon i Salesforce-Sandbox som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Salesforce begränsat läge:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Salesforce Sandbox** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På den **SAML grundkonfiguration** om du har **tjänstleverantör metadatafil** och vill konfigurera i **IDP** initierad läge utför följande steg:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    > [!NOTE]
    > Du får tjänstleverantören metadatafilen från Salesforce Sandbox-administrationsportalen som beskrivs senare i självstudien.

    c. När metadatafilen har överförts den **svars-URL** värde får automatiskt ifylld i **svars-URL** textrutan.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Om den **svars-URL** värde får inte automatisk polulated, och fyller sedan i värdet manuellt enligt dina behov.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Metadata XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera Salesforce Sandbox** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-salesforce-sandbox-single-sign-on"></a>Konfigurera Salesforce Sandbox enkel inloggning

1. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-Sandbox-administratörskonto.

2. Klicka på **Setup** (Konfiguration) under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure1.png)

3. Rulla ned till den **inställningar** i det vänstra navigeringsfönstret klickar du på **identitet** att expandera avsnittet relaterade. Klicka sedan på **Inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. På sidan med **Inställningar för enkel inloggning** klickar du på **Redigera**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure3.png)

5. Välj **SAML-aktiverat** och klicka på **Spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Konfigurera inställningarna för enkel inloggning med SAML genom att klicka på **Nytt från metadatafil**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Klicka på **Välj fil** för att ladda upp XML-filen med metadata som du har laddat ned från Azure-portalen och klicka på **Skapa**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. På sidan med **inställningar för enkel inloggning med SAML** fylls fälten i automatiskt. Klicka på Spara.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. På den **inställningar för enkel inloggning** klickar du på den **hämta Metadata** för att ladda ned metadatafilen från leverantören. Använd den här filen i den **SAML grundkonfiguration** avsnitt i Azure-portalen för att konfigurera de nödvändiga URL: er enligt beskrivningen ovan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure4.png)

10. Om du vill konfigurera programmet i **SP** initierad läge är förutsättningarna för som:

    a. Du bör ha en verifierad domän.

    b. Du måste du konfigurera och aktivera din domän på Salesforce Sandbox beskrivs stegen för detta senare i den här självstudien.

    c. I Azure-portalen på den **SAML grundkonfiguration** klickar du på **ange ytterligare webbadresser** och utföra följande steg:
  
    ![Salesforce Sandbox-domän och URL: er med enkel inloggning för information](common/both-signonurl.png)

    I den **inloggnings-URL** textrutan skriver du värdet med följande mönster: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Det här värdet ska kopieras från Salesforce Sandbox-portalen när du har aktiverat domänen.

11. På den **SAML-signeringscertifikat** klickar du på **Federation Metadata XML** och spara xml-filen på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

12. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-Sandbox-administratörskonto.

13. Klicka på **Setup** (Konfiguration) under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure1.png)

14. Rulla ned till den **inställningar** i det vänstra navigeringsfönstret klickar du på **identitet** att expandera avsnittet relaterade. Klicka sedan på **Inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. På sidan med **Inställningar för enkel inloggning** klickar du på **Redigera**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure3.png)

16. Välj **SAML-aktiverat** och klicka på **Spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Konfigurera inställningarna för enkel inloggning med SAML genom att klicka på **Nytt från metadatafil**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Klicka på **Välj fil** överföra den XML-fil och klicka på **skapa**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. På den **SAML enkel inloggning inställningar** sidan fält fylla automatiskt, skriver du namnet på konfigurationen (till exempel: *SPSSOWAAD_Test*) i den **namn** textrutan och klicka på Spara.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Om du vill aktivera din domän på Salesforce sandbox-miljön, utför du följande steg:

    > [!NOTE]
    > Innan du aktiverar domänen måste du skapa samma på Salesforce Sandbox. Mer information finns i [definiera domännamn](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). När domänen har skapats, kontrollera att den är korrekt konfigurerad.

21. Klicka på det vänstra navigeringsfönstret i Salesforce Sandbox **Företagsinställningar** Expandera avsnittet relaterade och klicka sedan på **min domän**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. I den **Autentiseringskonfiguration** klickar du på **redigera**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. I den **Autentiseringskonfiguration** avsnittet som **autentiseringstjänst**, Välj namnet på SAML enkel inloggning inställningen som du har angett under konfigurationen för enkel inloggning i Salesforce sandbox-miljön och Klicka på **spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Salesforce Sandbox.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Salesforce Sandbox**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Salesforce Sandbox**.

    ![Salesforce-Sandbox-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-salesforce-sandbox-test-user"></a>Skapa Salesforce-Sandbox testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i Salesforce Sandbox. Salesforce-Sandbox stöder just-in-time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Salesforce Sandbox, skapas en ny när du försöker få åtkomst till Salesforce Sandbox. Salesforce-Sandbox stöder även automatisk användaretablering, kan du hitta mer information om [här](salesforce-sandbox-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Salesforce Sandbox i åtkomstpanelen, bör det vara loggas in automatiskt till Salesforce-Sandbox som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Konfigurera användarförsörjning](salesforce-sandbox-provisioning-tutorial.md)
