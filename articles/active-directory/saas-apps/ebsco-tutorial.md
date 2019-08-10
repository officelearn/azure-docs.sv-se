---
title: 'Självstudier: Azure Active Directory integrering med EBSCO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 35cb408473da8c6397c5034ae20ac0a50b0953ea
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944712"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Självstudier: Azure Active Directory integrering med EBSCO

I den här självstudien får du lära dig hur du integrerar EBSCO med Azure Active Directory (Azure AD).
Genom att integrera EBSCO med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till EBSCO.
* Du kan göra det möjligt för användarna att logga in automatiskt till EBSCO (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med EBSCO behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* EBSCO-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* EBSCO stöder **SP** -och **IDP** -initierad SSO

* EBSCO stöder **just-in-Time** User-etablering

## <a name="adding-ebsco-from-the-gallery"></a>Lägga till EBSCO från galleriet

Om du vill konfigurera integreringen av EBSCO i Azure AD måste du lägga till EBSCO från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till EBSCO från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program klickar du på knappen **nytt program** överst i dialog rutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Ebsco**, väljer **Ebsco** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

     ![EBSCO i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med EBSCO baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i EBSCO upprättas.

Om du vill konfigurera och testa enkel inloggning med EBSCO i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Ebsco-enkel inloggning](#configure-ebsco-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Ebsco test User](#create-ebsco-test-user)** – om du vill ha en motsvarighet till Britta Simon i Ebsco som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med EBSCO i Azure AD:

1. Välj **enkel inloggning**på sidan **Ebsco** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen **Redigeringa** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning för EBSCO-domän och URL: er](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `pingsso.ebscohost.com`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Ebsco client support team](mailto:sso@ebsco.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    o **unika element:**  

    o **Custid** = ange unikt Ebsco kund-ID 

    o **Profile** = klienter kan skräddarsy länken för att dirigera användare till en speciell profil (beroende på vad de köper från Ebsco). De kan ange ett angivet profil-ID. Huvud-ID: t är EDS (EBSCO Discovery service) och eHost (EBSOCOhost-databaser). Instruktioner för samma anges [här](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

6. EBSCO-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![image](common/edit-attribute.png)

     > [!Note]
    > Namnattributet är obligatoriskt och det mappas med namn- **ID-värdet** i Ebsco-programmet. Detta läggs till som standard, så du behöver inte lägga till det manuellt.

7. Utöver ovan förväntar sig EBSCO-programmet att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan: 

    | Namn | Källattribut|
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Spara**.

8. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

9. I avsnittet **Konfigurera Ebsco** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-ebsco-single-sign-on"></a>Konfigurera EBSCO enkel inloggning

Om du vill konfigurera enkel inloggning på **Ebsco** sida måste du skicka den hämtade **metadata-XML** och lämpliga kopierade url: er från Azure Portal till [support teamet för Ebsco](mailto:sso@ebsco.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till EBSCO.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Ebsco**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Ebsco**.

    ![EBSCO-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ebsco-test-user"></a>Skapa EBSCO test användare

När det gäller EBSCO är användar etableringen automatisk.

**Utför följande steg för att etablera ett användarkonto:**

Azure AD överför nödvändiga data till EBSCO-program. Användar etableringen för EBSCO kan vara automatisk eller kräver ett formulär med en tids period. Det beror på om klienten har massor av redan befintliga EBSCOhost-konton med personliga inställningar sparade. Samma sak kan diskuteras med [Ebsco support team](mailto:sso@ebsco.com) under implementeringen. Oavsett hur behöver klienten inte skapa några EBSCOhost-konton innan testningen.

   >[!Note]
   >Du kan automatisera EBSCOhost användar etablering/anpassning. Kontakta [Ebsco support team](mailto:sso@ebsco.com) om just-in-Time-etablering av användare. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

1. När du klickar på panelen EBSCO på åtkomst panelen, bör du få automatiskt inloggad till ditt EBSCO-program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

2. När du har loggat in på programmet klickar du på knappen **Logga in** i det övre högra hörnet.

    ![EBSCO-inloggningen i program listan](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Du får en eng ång slö period för att para ihop institutionella/SAML-inloggningen med en **länk ditt befintliga MyEBSCOhost-konto till ditt institutions konto nu** eller **skapa ett nytt MyEBSCOhost-konto och länka det till ditt institutions konto**. Kontot används för anpassning av EBSCOhost-programmet. Välj alternativet **skapa ett nytt konto** så ser du att formuläret för anpassningen har slutförts med värdena från SAML-svaret som visas på skärm bilden nedan. Klicka på **Fortsätt** för att spara det här alternativet.
    
     ![EBSCO-användaren i program listan](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. När du har slutfört ovanstående konfiguration rensar du cookies/cache och loggar in igen. Du behöver inte logga in manuellt igen och anpassnings inställningarna sparas

## <a name="additional-sesources"></a>Ytterligare sesources

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

