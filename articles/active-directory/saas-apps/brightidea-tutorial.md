---
title: 'Självstudier: Azure Active Directory-integrering med Brightidea | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Brightidea.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3adae3e0-f43b-492f-b373-6a512d2d6046
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c839c6cfa15c9a2e0874939d05467050f7e4849
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189233"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>Självstudier: Azure Active Directory-integrering med Brightidea

I den här självstudien lär du dig att integrera Brightidea med Azure Active Directory (Azure AD).
Integreringen av Brightidea med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Brightidea.
* Du kan göra så att dina användare automatiskt loggas in på Brightidea (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande saker för att konfigurera Azure AD-integrering med Brightidea:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Brightidea-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.


* Brightidea stöder **SP- och IDP**-initierad enkel inloggning
* Brightidea stöder **just-in-time**-användaretablering


## <a name="adding-brightidea-from-the-gallery"></a>Lägga till Brightidea från galleriet

För att konfigurera integrering av Brightidea med Azure AD behöver du lägga till Brightidea från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Brightidea från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Brightidea**, väljer **Brightidea** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Brightidea i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Brightidea baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Brightidea upprättas.

Du behöver slutföra följande byggstenar för att konfigurera och testa enkel inloggning med Azure AD för Brightidea:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Brightidea](#configure-brightidea-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Brightidea-testanvändare](#create-brightidea-test-user)** – för att ha en motsvarighet till Britta Simon i Brightidea som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Brightidea:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Brightidea** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstprovidern** och vill konfigurera i **IDP**-initierat läge:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp fylls värdena **Identifierare** och **Svars-URL** i automatiskt i textrutan för avsnittet Brightidea:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<SUBDOMAIN>.brightidea.com`

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Ladda ned** för att ladda ned **XML-federationsmetadata** från de angivna alternativen enligt dina behov och sparar dem på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Brightidea** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-brightidea-single-sign-on"></a>Konfigurera enkel inloggning för Brightidea

1. I ett annat webbläsarfönster loggar du in på Brightidea med autentiseringsuppgifter för administratör.

2. Du hittar funktionen för enkel inloggning i ditt Brightidea-system genom att gå till **Enterprise Setup (Företagskonfiguration)** -> **fliken Autentisering**. Där finns två underflikar: Auth Selection (Autentiseringsval) och SAML Profiles (SAML-profiler).

    ![Brightidea-konfiguration](./media/brightidea-tutorial/configure1.png)

3. Välj **Auth Selection** (Autentiseringsval). Som standard visas bara två standardmetoder: Brightidea Login & Registration (Inloggning och registrering). När en metod för enkel inloggning har lagts till visas den i listan.

    ![Brightidea-konfiguration](./media/brightidea-tutorial/configure2.png)

4. Välj **SAML profiler** och utför följande steg:

    ![Brightidea-konfiguration](./media/brightidea-tutorial/configure3.png)

    a. Klicka på **Ladda ned metadata** och ladda upp i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. Klicka på knappen **Lägg till ny** under **Identity Provider Setting** (Inställning för identitetsprovider) och utför följande steg:
    
    ![Brightidea-konfiguration](./media/brightidea-tutorial/configure4.png)
    
     * Ange **SAML-profilnamn**, till exempel `Azure Ad SSO`
    
     * För **Ladda upp metadata** klickar du på Välj fil och laddar upp den nedladdade metadatafilen från Azure-portalen.

     > [!NOTE]
     > När du har laddat upp metadatafilen fylls de återstående fälten **Single Sign-on Service, Identity Provider Issuer, Upload Public Key** (Tjänst för enkel inloggning, Utfärdare för identitetsprovider och Offentlig nyckel för uppladdning) i automatiskt.

     * I textrutan **E-post** anger du värdet `mail`.
     
     * I textrutan **Skärmnamn** anger du värdet `givenName`.
     
     * Klicka på **Spara ändringar**.  

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Brightidea.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Brightidea**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Brightidea**.

    ![Länken för Brightidea i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-brightidea-test-user"></a>Skapa Brightidea-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Brightidea. Brightidea stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Brightidea skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Brightidea-panelen i åtkomstpanelen bör du automatiskt loggas in på Brightidea som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

