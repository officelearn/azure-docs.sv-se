---
title: 'Självstudiekurs: Azure Active Directory-integrering med PolicyStat | Microsoft-dokument'
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
ms.openlocfilehash: 327e470d60235e6bf400293e80e3aec5f6144ff4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943447"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Självstudiekurs: Azure Active Directory-integrering med PolicyStat

I den här självstudien får du lära dig hur du integrerar PolicyStat med Azure Active Directory (Azure AD).
Genom att integrera PolicyStat med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till PolicyStat.
* Du kan aktivera dina användare så att de automatiskt loggas in på PolicyStat (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med PolicyStat behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* PolicyStat enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* PolicyStat stöder **SP** initierade SSO

* PolicyStat stöder just in time-användaretablering **Just In Time**

## <a name="adding-policystat-from-the-gallery"></a>Lägga till PolicyStat från galleriet

Om du vill konfigurera integreringen av PolicyStat i Azure AD måste du lägga till PolicyStat från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till PolicyStat från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **PolicyStat**i sökrutan och välj **PolicyStat** från resultatpanelen och klicka sedan på **Lägg** till för att lägga till programmet.

     ![PolicyStat i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med PolicyStat baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i PolicyStat upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med PolicyStat måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera PolicyStat Single Sign-On](#configure-policystat-single-sign-on)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa PolicyStat-testanvändare](#create-policystat-test-user)** - om du vill ha en motsvarighet till Britta Simon i PolicyStat som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med PolicyStat:

1. Välj Enkel inloggning på sidan PolicyStat-programintegrering på **PolicyStat** [Azure-portalen](https://portal.azure.com/). **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![PolicyStat Domän och webbadresser enkel inloggningsinformation](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.policystat.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [PolicyStat Client support team](http://www.policystat.com/support/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

5. Ditt PolicyStat-program förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon om du vill öppna dialogrutan **Användarattribut.**

    ![image](common/edit-attribute.png)

6. Förutom ovan förväntar sig PolicyStat-programmet att få fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.
    
    ![image](common/new-save-attribute.png)

    ![image](./media/policystat-tutorial/attribute01.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **omvandling**.

    e. Skriv attributvärdet som visas för den raden i listan **Omformning.**
    
    f. Skriv attributvärdet som visas för den raden i listan **Parameter 1.**

    g. Klicka på **Spara**.

7. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera PolicyStat.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-policystat-single-sign-on"></a>Konfigurera enkel inloggning för PrincipStat

1. I ett annat webbläsarfönster loggar du in på policystat-företagets webbplats som administratör.

2. Klicka på fliken **Admin** och sedan på **Enkel inloggningskonfiguration** i det vänstra navigeringsfönstret.
   
    ![Administratörsmeny](./media/policystat-tutorial/ic808633.png "Administratörsmeny")

3. Välj Aktivera enkel **inloggningsintegrering i**avsnittet **Inställningar** .
   
    ![Konfiguration för enkel inloggning](./media/policystat-tutorial/ic808634.png "Konfiguration för enkel inloggning")

4. Klicka på **Konfigurera attribut**och utför sedan följande i avsnittet **Konfigurera attribut:**
   
    ![Konfiguration för enkel inloggning](./media/policystat-tutorial/ic808635.png "Konfiguration för enkel inloggning")
   
    a. Skriv **uid**i textrutan **Användarnamnsattribut** .

    b. Skriv **förnamnnamn för** användaren **Britta**i textrutan **Förnamnsattribut** .

    c. Skriv **efternamnsnamn** för användaren **Simon**i textrutan **Förnamnattribut** .

    d. Skriv **e-postadress till** användaren `BrittaSimon@contoso.com`i textrutan **E-postattribut** .

    e. Klicka på **Spara ändringar**.

5. Klicka på **Dina IDP-metadata**och utför sedan följande i avsnittet **Dina IDP-metadata:**
   
    ![Konfiguration för enkel inloggning](./media/policystat-tutorial/ic808636.png "Konfiguration för enkel inloggning")
   
    a. Öppna den nedladdade metadatafilen, kopiera innehållet och klistra sedan in den i textrutan **Identitetsprovidermetadata.**

    b. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till PolicyStat.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **PolicyStat**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **PolicyStat**i programlistan .

    ![Länken PolicyStat i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-policystat-test-user"></a>Skapa PrincipStat-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i PolicyStat. PolicyStat stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i PolicyStat skapas en ny efter autentisering.

>[!NOTE]
>Du kan använda andra PolicyStat-verktyg för att skapa användarkonton eller API:er som tillhandahålls av PolicyStat för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen PolicyStat på åtkomstpanelen ska du automatiskt loggas in på den PolicyStat som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

