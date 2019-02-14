---
title: 'Självstudier: Azure Active Directory-katalogintegrering med ClickTime | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ClickTime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a606345655e18cf4991387cfd3cd18716d2df9dd
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188196"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Självstudier: Azure Active Directory-katalogintegrering med ClickTime

I den här självstudien lär du dig att integrera ClickTime med Azure Active Directory (AD Azure).
När du integrerar ClickTime med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till ClickTime.
* Du kan låta dina användare automatiskt loggas in på ClickTime (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med ClickTime behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ClickTime-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ClickTime stöder **IDP**-initierad enkel inloggning

## <a name="adding-clicktime-from-the-gallery"></a>Lägga till ClickTime från galleriet

För att konfigurera integrering av ClickTime i Azure AD måste du lägga till ClickTime från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ClickTime från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **ClickTime**, väljer **ClickTime** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![ClickTime i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med ClickTime baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i ClickTime upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med ClickTime måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för ClickTime](#configure-clicktime-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ClickTime-testanvändare](#create-clicktime-test-user)** – för att ha en motsvarighet för Britta Simon i ClickTime som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med ClickTime:

1. Välj **Enkel inloggning** på sidan för programintegrering av **ClickTime** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om enkel inloggning med ClickTime-domäner och -URL:er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du in en URL: `https://app.clicktime.com/sp/`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:
    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera ClickTime** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-clicktime-single-sign-on"></a>Konfigurera enkel inloggning för ClickTime

1. Logga in på ClickTime-företagswebbplatsen som administratör i ett annat webbläsarfönster.

1. I verktygsfältet längst upp klickar du på **Inställningar** och sedan på **Säkerhetsinställningar**.

1. Gör följande i avsnittet **Single Sign-On Preferences** (Inställningar för enkel inloggning):
   
    ![Säkerhetsinställningar](./media/clicktime-tutorial/tic777280.png "Säkerhetsinställningar")
   
    a.  Välj **Tillåt** för inloggning med enkel inloggning (SSO) med **Azure AD**.
   
    b. I textrutan **Identity Provider Endpoint** (Slutpunkt för identitetsprovider) klistrar du in den **inloggnings-URL** som du har kopierat från Azure-portalen.
   
    c.  Öppna det **base64-kodade certifikat** som du laddade ned från Azure-portalen i **Anteckningar**, kopierar innehållet och klistrar sedan in det i textrutan för **X.509-certifikatet**.
   
    d.  Klicka på **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till ClickTime.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **ClickTime**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **ClickTime**.

    ![Länken ClickTime i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-clicktime-test-user"></a>Skapa ClickTime-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på ClickTime måste de etableras i ClickTime.  
När det gäller ClickTime är etablering en manuell aktivitet.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa ClickTime-användarkonton som tillhandahålls av ClickTime för att etablera Azure AD-användarkonton.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **ClickTime**-klient.

1. I verktygsfältet högst upp klickar du på **Företag** och klicka sedan på **Personer**.
   
    ![Personer](./media/clicktime-tutorial/tic777282.png "Personer")

1. Klicka på **Lägg till person**.
   
    ![Lägg till person](./media/clicktime-tutorial/tic777283.png "Lägg till person")

1. I avsnittet New Person (Ny person) utför du följande steg:
   
    ![Personer](./media/clicktime-tutorial/tic777284.png "Personer")
   
    a.  I textrutan **Fullständigt namn** skriver du det fullständiga namnet för användaren som **Britta Simon**. 
  
    b.  I textrutan för **e-postadress** skriver du e-postadressen för användaren som **brittasimon@contoso.com**.
       
    > [!NOTE]
    > Om du vill kan du ange du ytterligare egenskaper för personobjektet.
   
    c.  Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på ClickTime-panelen i åtkomstpanelen så borde du automatiskt loggas in på den ClickTime som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

