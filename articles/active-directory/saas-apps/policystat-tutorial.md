---
title: 'Självstudier: Azure Active Directory-integrering med PolicyStat | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 98ab9904f8afc5c74d9f043b0964bc56b49670cc
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269783"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Självstudier: Azure Active Directory-integrering med PolicyStat

I den här självstudien får du lära dig hur du integrerar PolicyStat med Azure Active Directory (AD Azure).
Integrera PolicyStat med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till PolicyStat.
* Du kan aktivera användarna att vara automatiskt inloggad till PolicyStat (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med PolicyStat, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* PolicyStat enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för PolicyStat **SP** -initierad SSO

* Har stöd för PolicyStat **Just In Time** etableringen av användare

## <a name="adding-policystat-from-the-gallery"></a>Att lägga till PolicyStat från galleriet

För att konfigurera integrering av PolicyStat i Azure AD, som du behöver lägga till PolicyStat från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till PolicyStat från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **PolicyStat**väljer **PolicyStat** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![PolicyStat i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med PolicyStat baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i PolicyStat upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med PolicyStat, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera PolicyStat Single Sign-On](#configure-policystat-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare PolicyStat](#create-policystat-test-user)**  – du har en motsvarighet för Britta Simon i PolicyStat som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med PolicyStat:

1. I den [Azure-portalen](https://portal.azure.com/)på den **PolicyStat** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![PolicyStat domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL enligt följande mönster: `https://<companyname>.policystat.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [PolicyStat klienten supportteamet](http://www.policystat.com/support/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

5. Programmets PolicyStat förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen  **Redigera** för att öppna dialogrutan **Användarattribut** .

    ![image](common/edit-attribute.png)

6. Förutom ovanstående PolicyStat program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.
    
    ![image](common/new-save-attribute.png)

    ![image](./media/policystat-tutorial/attribute01.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj källa som **omvandling**.

    e. Från den **omvandling** anger attributvärdet som visas för den raden.
    
    f. Från den **Parameter 1** anger attributvärdet som visas för den raden.

    g. Klicka på **Spara**.

7. På den **konfigurera PolicyStat** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-policystat-single-sign-on"></a>Konfigurera PolicyStat Single Sign-On

1. Logga in på webbplatsen PolicyStat företag som en administratör i ett annat webbläsarfönster.

2. Klicka på den **Admin** fliken och klicka sedan på **konfigurationen för enkel inloggning** i vänstra navigeringsfönstret.
   
    ![Administratör-menyn](./media/policystat-tutorial/ic808633.png "administratör-menyn")

3. I den **installationsprogrammet** väljer **integrering för aktivera enkel inloggning**.
   
    ![Enkel Inloggningkonfigurationen](./media/policystat-tutorial/ic808634.png "enkel Inloggningkonfigurationen")

4. Klicka på **Konfigurera attribut**, och klicka sedan på **Konfigurera attribut** avsnittet, utför följande steg:
   
    ![Enkel Inloggningkonfigurationen](./media/policystat-tutorial/ic808635.png "enkel Inloggningkonfigurationen")
   
    a. I den **Användarnamnattribut** textrutan typ **uid**.

    b. I den **förnamn attributet** textrutan typ **firstname** användarens **Britta**.

    c. I den **senaste namnattributet** textrutan typ **lastname** användarens **Simon**.

    d. I den **e-attributet** textrutan typ **e-postadress** användarens `BrittaSimon@contoso.com`.

    e. Klicka på **Spara ändringar**.

5. Klicka på **Your IDP-Metadata**, och klicka sedan på **Your IDP-Metadata** avsnittet, utför följande steg:
   
    ![Enkel Inloggningkonfigurationen](./media/policystat-tutorial/ic808636.png "enkel Inloggningkonfigurationen")
   
    a. Öppna din hämtade metadatafilen, kopiera innehållet och klistra in den i den **din identitet providern Metadata** textrutan.

    b. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PolicyStat.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **PolicyStat**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **PolicyStat**.

    ![Länken PolicyStat i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-policystat-test-user"></a>Skapa PolicyStat testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i PolicyStat. PolicyStat stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i PolicyStat, skapas en ny efter autentisering.

>[!NOTE]
>Du kan använda alla andra PolicyStat användare konto verktyg för att skapa eller API: er som tillhandahålls av PolicyStat att etablera användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen PolicyStat i åtkomstpanelen, bör det vara loggas in automatiskt till PolicyStat som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

