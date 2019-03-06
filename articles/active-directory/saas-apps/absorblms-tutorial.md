---
title: 'Självstudier: Azure Active Directory-integrering med Absorb LMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Absorb LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 998a46bc6255898ab4754de57a2570a77896cf85
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876746"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Självstudier: Azure Active Directory-integrering med Absorb LMS

Lär dig hur du integrerar Absorb LMS med Azure Active Directory (Azure AD) i den här självstudien.
Att integrera Absorb LMS med Azure Active Directory ger dig följande fördelar:

* Du kan styra i Azure Active Directory vem som har åtkomst till Absorb LMS.
* Du kan göra så att dina användare automatiskt loggas in på Absorb LMS (enkel inloggning) med sina Azure Active Directory-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure Active Directory-integrering med Absorb LMS, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Prenumeration med aktiverad enkel inloggning med Absorb LMS

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Absorb LMS stöder **IDP**-initierad enkel inloggning

## <a name="adding-absorb-lms-from-the-gallery"></a>Att lägga till Absorb LMS från galleriet

För att konfigurera integrering av Absorb LMS i Azure Active Directory, behöver du lägga till Absorb LMS från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Absorb LMS från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Absorb LMS** och väljer **Absorb LMS** från resultatpanelen och klickar sedan på knappen**Lägg till** för att lägga till programmet.

     ![Absorb LMS i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure Active Directory med Absorb LMS baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure Active Directory-användare och den relaterade användaren i Absorb LMS upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med Absorb LMS, måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning med Absorb LMS](#configure-absorb-lms-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Absorb LMS-testanvändare](#create-absorb-lms-test-user)** – för att ha en motsvarighet till Britta Simon i Absorb LMS som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med Absorb LMS:

1. I [Azure Portal](https://portal.azure.com/), på sidan för **Absorb LMS**-programintegrering, väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Information om enkel inloggning med Absorb LMS-domän och URL:er](common/idp-intiated.png)

    Om du använder **Absorb 5 – UI** använder du följande konfiguration:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://company.myabsorb.com/account/saml`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://company.myabsorb.com/account/saml`

    Om du använder **Absorb 5 – New Learner Experience** använder du följande konfiguration:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Supportteamet för Absorb LMS](https://support.absorblms.com/hc/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Metadata XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Absorb LMS** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-absorb-lms-single-sign-on"></a>Konfigurera Absorb LMS-enkel inloggning

1. I ett nytt webbläsarfönster, loggar du in på din Absorb LMS-företagsplats som administratör.

2. Välj knappen **Konto** längst upp till höger.

    ![Knappen Konto](./media/absorblms-tutorial/1.png)

3. I rutan Konto väljer du **Portalinställningar**.

    ![Exportera länken Portalinställningar](./media/absorblms-tutorial/2.png)

4. Välj fliken **Hantera inställningar för enkel inloggning**.

    ![Fliken Användare](./media/absorblms-tutorial/managesso.png)

5. På sidan **Hantera inställningar för enkel inloggning** gör du följande:

    ![Konfigurationssidan för enkel inloggning](./media/absorblms-tutorial/settings.png)

    a. I textrutan **Namn** anger du namnet till Azure AD Marketplace SSO.

    b. Välj **SAML** som en **Metod**.

    c. I anteckningar, öppnar du det certifikat som du laddade ned från Azure Portal. Ta bort taggarna **---BEGIN CERTIFICATE---** och **---END CERTIFICATE---**. I rutan **Nyckel** klistrar du in det återstående innehållet.

    d. I rutan **Läge** väljer du **Identitetsprovidern initierad**.

    e. I rutan **Id-egenskap** väljer du det attribut som du har konfigurerat som användaridentifierare i Azure AD. Till exempel om *userPrincipalName* väljs i Azure AD, väljer du **Användarnamn**.

    f. Välj **Sha256** som en **Signaturtyp**.

    g. I rutan **inloggnings-URL** klistrar du in **URL för användaråtkomst** från programmets sida **Egenskaper** i Azure Portal.

    h. I **URL för utloggning**, klistrar du in det värde för **URL:en för utloggning** som du kopierade från fönstret **Konfigurera inloggning** i Azure Portal.

    i. Växla **Omdirigera automatiskt** till **På**.

6. Välj **Spara.**

    ![Växla Endast tillåta SSO-inloggning](./media/absorblms-tutorial/save.png)

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

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kommer du att göra det möjligt för Britta Simon att använda Azure-enkel inloggning genom att ge åtkomst till Absorb LMS.

1. I Azure Portal väljer du **Företagsprogram**, **Alla program** och sedan **Absorb LMS**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **Absorb LMS**.

    ![Absorb LMS-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-absorb-lms-test-user"></a>Skapa Absorb LMS-testanvändare

För att Azure AD-användare ska kunna logga in på Absorb LMS, måste de konfigureras i Absorb LMS. När det gäller Absorb LMS är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din Absorb LMS-företagsplats som administratör.

2. I fönstret **Användare** väljer du **Användare**.

    ![Länken Användare](./media/absorblms-tutorial/absorblms_userssub.png)

3. Välj fliken **Användare**.

    ![Listrutan Lägg till ny](./media/absorblms-tutorial/absorblms_createuser.png)

4. På sidan **Lägg till användare** gör du följande:

    ![Sidan Lägg till användare](./media/absorblms-tutorial/user.png)

    a. I rutan **Förnamn** skriver du in förnamn, till exempel **Britta**.

    b. I rutan **Efternamn** skriver du in efternamn, till exempel **Simon**.

    c. I rutan **Användarnamn** skriver du in hela namnet, till exempel **Britta Simon**.

    d. I rutan **Lösenord** skriver du in lösenordet.

    e. I rutan **Bekräfta lösenord** skriver du in lösenordet igen.

    f. Ange växlingsknappen **Är aktiv** till **Aktiv**.

5. Välj **Spara.**

    ![Växla Endast tillåta SSO-inloggning](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Som standard är användarförsörjning inte aktiverat i enkel inloggning. Om kunden vill aktivera den här funktionen, behöver hen konfigurera den enligt [denna](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) dokumentation. Tänk på att användarförsörjning bara är tillgänglig på **Absorb 5 – New Learner Experience** med ACS URL-`https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Absorb LMS-panelen i åtkomstpanelen bör du automatiskt loggas in på Absorb LMS som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
