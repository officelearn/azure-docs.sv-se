---
title: 'Självstudier: Azure Active Directory integrering med Veracode | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: jeedes
ms.openlocfilehash: 67b4d4842bfa330e51244cee0d9ad8ad0ab6a24f
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825957"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Självstudier: Azure Active Directory integrering med Veracode

I den här självstudien får du lära dig hur du integrerar Veracode med Azure Active Directory (Azure AD).
Genom att integrera Veracode med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Veracode.
* Du kan göra det möjligt för användarna att logga in automatiskt till Veracode (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Veracode behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Veracode-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Veracode stöder **IDP** INITIERAd SSO

* Veracode stöder **just-in-Time** User-etablering

## <a name="adding-veracode-from-the-gallery"></a>Lägga till Veracode från galleriet

Om du vill konfigurera integreringen av Veracode i Azure AD måste du lägga till Veracode från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Veracode från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Veracode**, väljer **Veracode** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Veracode i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Veracode baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Veracode upprättas.

Om du vill konfigurera och testa enkel inloggning med Veracode i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Veracode-enkel inloggning](#configure-veracode-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Veracode test User](#create-veracode-test-user)** – om du vill ha en motsvarighet till Britta Simon i Veracode som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Veracode i Azure AD:

1. Välj **enkel inloggning**på sidan **Veracode** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![Information om enkel inloggning för Veracode-domän och URL: er](common/preintegrated.png)

5. Veracode-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![image](common/edit-attribute.png)

6. Utöver ovan förväntar sig Veracode-programmet att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | ---------------| --------------- |
    | förnamn |User. givenName |
    | efternamn |Användare. efter namn |
    | email |User.mail |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

8. I avsnittet **Konfigurera Veracode** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-veracode-single-sign-on"></a>Konfigurera Veracode enkel inloggning

1. Logga in på din Veracode-företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **Inställningar**på menyn högst upp och klicka sedan på **admin**.
   
    ![Administration](./media/veracode-tutorial/ic802911.png "Administration")

3. Klicka på fliken **SAML** .

4. I avsnittet **organisations SAML-inställningar** utför du följande steg:
   
    ![Administration](./media/veracode-tutorial/ic802912.png "Administration")
   
    a.  I text rutan utfärdare klistrar du in värdet för **Azure AD** -identifieraren som du kopierade från Azure Portal.
    
    b. Klicka på **Välj fil**om du vill ladda upp det hämtade certifikatet från Azure Portal.
   
    c. Välj **Aktivera själv registrering**.

1. I avsnittet **Inställningar för själv registrering** utför du följande steg och klickar sedan på **Spara**:
   
    ![Administration](./media/veracode-tutorial/ic802913.png "Administration")
   
    a. Välj **ingen aktivering**som **ny användar aktivering**krävs.
   
    b. Välj **Preference Veracode user data**som **uppdateringar av användar data**.
   
    c. För **information om SAML-attribut**väljer du följande:
      * **Användar roller**
      * **Princip administratör**
      * **Granskare**
      * **Säkerhets lead**
      * **Executive**
      * **Jobbets avsändare**
      * **Skapare**
      * **Alla genomsöknings typer**
      * **Grupp medlemskap**
      * **Standard team**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I brittasimon@yourcompanydomain.extensionfältet **användar namn** . Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Veracode.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Veracode**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Veracode**.

    ![Veracode-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-veracode-test-user"></a>Skapa Veracode test användare

För att Azure AD-användare ska kunna logga in på Veracode måste de tillhandahållas i Veracode. När det gäller Veracode är etableringen en automatiserad uppgift. Det finns inget åtgärds objekt för dig. Användare skapas automatiskt vid behov under det första försöket vid enkel inloggning.

> [!NOTE]
> Du kan använda andra verktyg för Veracode av användar konton eller API: er som tillhandahålls av Veracode för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Veracode på åtkomst panelen, bör du loggas in automatiskt på den Veracode som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

