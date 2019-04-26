---
title: 'Självstudier: Azure Active Directory-integrering med EBSCO | Microsoft Docs'
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
ms.openlocfilehash: 2753daf225016d3bd8e07383193a1260b40a36d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60280196"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Självstudier: Azure Active Directory-integrering med EBSCO

I den här självstudien får du lära dig hur du integrerar EBSCO med Azure Active Directory (AD Azure).
Integrera EBSCO med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till EBSCO.
* Du kan aktivera användarna att vara automatiskt inloggad till EBSCO (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med EBSCO, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* EBSCO enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för EBSCO **SP** och **IDP** -initierad SSO

* Har stöd för EBSCO **Just In Time** etableringen av användare

## <a name="adding-ebsco-from-the-gallery"></a>Att lägga till EBSCO från galleriet

För att konfigurera integrering av EBSCO i Azure AD, som du behöver lägga till EBSCO från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till EBSCO från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program, klicka på den **nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **EBSCO**väljer **EBSCO** från panelen resultatet klickar på **Lägg till** för att lägga till programmet.

     ![EBSCO i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med EBSCO baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i EBSCO upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med EBSCO, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera EBSCO Single Sign-On](#configure-ebsco-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare EBSCO](#create-ebsco-test-user)**  – du har en motsvarighet för Britta Simon i EBSCO som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med EBSCO:

1. I den [Azure-portalen](https://portal.azure.com/)på den **EBSCO** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen **Redigeringa** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På den **SAML grundkonfiguration** om du vill konfigurera programmet i **IDP** initierade läge, utföra följande steg:

    ![EBSCO domän och URL: er med enkel inloggning för information](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `pingsso.ebscohost.com`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [EBSCO klienten supportteamet](mailto:sso@ebsco.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    o **unika element:**  

    o **Custid** = RETUR unika EBSCO kund-ID 

    o **profil** = klienter kan anpassa på länken för att dirigera användare till en specifik profil (beroende på vad de köper från EBSCO). De kan ange en specifik profil-ID. De huvudsakliga ID: N är eds (EBSCO identifieringstjänst) och ehost (EBSOCOhost databaser). Instruktioner för samma ges [här](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

6. EBSCO program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen  **Redigera** för att öppna dialogrutan **Användarattribut** .

    ![image](common/edit-attribute.png)

     > [!Note]
    > Den **namn** attributet är obligatoriskt och det är kopplat med **namnidentifierare värdet** i EBSCO program. Detta läggs som standard så du inte behöver lägga till detta manuellt.

7. Förutom ovanstående EBSCO program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan: 

    | Namn | Källattribut|
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | E-post   | user.mail |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Spara**.

8. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

9. På den **konfigurera EBSCO** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ebsco-single-sign-on"></a>Konfigurera EBSCO enkel inloggning

Att konfigurera enkel inloggning på **EBSCO** sida, som du behöver skicka de hämtade **XML-Metadata för** och lämpliga kopierade URL: er från Azure portal för att [EBSCO supportteam](mailto:sso@ebsco.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till EBSCO.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **EBSCO**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **EBSCO**.

    ![Länken EBSCO i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ebsco-test-user"></a>Skapa EBSCO testanvändare

När det gäller EBSCO är etableringen av användare automatisk.

**Utför följande steg för att etablera ett användarkonto:**

Azure AD skickar nödvändiga data till EBSCO program. Etableringen av EBSCOS användare kan vara automatisk eller kräver ett enstaka formulär. Det beror på om klienten har en massa befintlig EBSCOhost konton med personliga inställningar sparas. Samma kan diskuteras med den [EBSCO supportteam](mailto:sso@ebsco.com) under genomförandet. I båda fallen klienten behöver inte skapa EBSCOhost konton innan du testar.

   >[!Note]
   >Du kan automatisera EBSCOhost etablering/Användaranpassning. Kontakta [EBSCO supportteam](mailto:sso@ebsco.com) om Just-In-Time etableringen av användare. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

1. När du klickar på panelen EBSCO i åtkomstpanelen du bör få automatiskt loggat in på ditt EBSCO program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

2. När du loggar in till programmet, klickar du på den **logga in** knappen i övre högra hörnet.

    ![EBSCO inloggningen i listan med program](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Du får ett enstaka meddelande att para ihop institutionella SAML/logga in med ett **länka ditt befintliga MyEBSCOhost-konto till din institution konto nu** eller **skapa ett nytt konto MyEBSCOhost och länka det till din institution konto**. Kontot används för användaranpassning på EBSCOhost-programmet. Välj alternativet **skapar ett nytt konto** och ser du att formuläret för anpassning före slutfördes med värden från saml-svar som visas i skärmbilden nedan. Klicka på **”Fortsätt”** att spara det här alternativet.
    
     ![EBSCO användaren i listan med program](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. När du har slutfört inställningarna ovan kan du rensa cookies/cacheminnet och logga in igen. Du behöver inte manuellt logga in igen och anpassningsinställningar ska sparas

## <a name="additional-sesources"></a>Ytterligare sesources

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

