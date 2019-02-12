---
title: 'Självstudier: Azure Active Directory-integrering med CloudPassage | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.openlocfilehash: 0e6ffb7f797746fed30520c1c99130ff585909a9
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697874"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Självstudier: Azure Active Directory-integrering med CloudPassage

I den här självstudien lär du dig att integrera CloudPassage med Azure Active Directory (AD Azure).
Integreringen av CloudPassage med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till CloudPassage.
* Du kan göra så att dina användare automatiskt loggas in på CloudPassage (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med CloudPassage behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* CloudPassage-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* CloudPassage har stöd för **SP**-initierad enkel inloggning

## <a name="adding-cloudpassage-from-the-gallery"></a>Lägga till CloudPassage från galleriet

För att konfigurera integreringen av CloudPassage i Azure AD måste du lägga till CloudPassage från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till CloudPassage från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **CloudPassage**, väljer **CloudPassage** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![CloudPassage i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med CloudPassage baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i CloudPassage upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med CloudPassage behöver du utföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för CloudPassage](#configure-cloudpassage-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa CloudPassage-testanvändare](#create-cloudpassage-test-user)** – för att skapa en motsvarighet till Britta Simon i CloudPassage som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med CloudPassage:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för **CloudPassage**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med CloudPassage-domän och -URL:er](common/sp-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://portal.cloudpassage.com/saml/init/accountid`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://portal.cloudpassage.com/saml/consume/accountid`. Du kan hämta värdet för det här attributet genom att klicka på **SSO Setup documentation** (Dokumentation för konfiguration av enkel inloggning) i avsnittet **Inställningar för enkel inloggning** i CloudPassage-portalen.

    ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med en faktisk inloggnings-URL och svars-URL. Kontakta [kundsupporten för CloudPassage](https://www.cloudpassage.com/company/contact/) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. CloudPassage-programmet förväntar sig SAML-försäkran i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg: 

    | Namn | Källattribut|
    | ---------------| --------------- |
    | förnamn |user.givenname |
    | lastname |user.surname |
    | e-post |user.mail |

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

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

8. I avsnittet **Konfigurera CloudPassage** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-cloudpassage-single-sign-on"></a>Konfigurera enkel inloggning för CloudPassage

1. I ett annat webbläsarfönster loggar du in på din CloudPassage-företagsplats som administratör.

1. På menyn längst upp klickar du på **Inställningar** och sedan på **Webbplatsadministration**. 
   
    ![Konfigurera enkel inloggning][12]

1. Klicka på fliken **Autentiseringsinställningar**. 
   
    ![Konfigurera enkel inloggning][13]

1. I avsnittet **Inställningar för enkel inloggning** utför du följande steg: 
   
    ![Konfigurera enkel inloggning][14]

    a. Markera kryssrutan **Enable Single sign-on(SSO)(SSO Setup Documentation)** (Aktivera enkel inloggning (SSO)(Dokumentation för konfiguration av enkel inloggning)).
    
    b. Klistra in **Azure Ad-identifierare** i textrutan **SAML issuer URL** (URL för SAML-utfärdare).
  
    c. Klistra in **inloggnings-URL** i textrutan **SAML endpoint URL** (URL för SAML-slutpunkt).
  
    d. Klistra in **utloggnings-URL** i textrutan **Logout landing page** (landningssida för utloggning).
  
    e. Öppna det nedladdade certifikatet i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **X.509 certificate** (X.509-certifikat).
  
    f. Klicka på **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till CloudPassage.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **CloudPassage**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **CloudPassage**.

    ![Länken för CloudPassage i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-cloudpassage-test-user"></a>Skapa CloudPassage-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i CloudPassage.

**Skapa en användare med namnet Britta Simon i CloudPassage genom att utföra följande steg:**

1. Logga in på din **CloudPassage**-företagsplats som administratör. 

1. I verktygsfältet längst upp klickar du på **Inställningar** och sedan på **Webbplatsadministration**. 
   
    ![Skapa en CloudPassage-testanvändare][22] 

1. Klicka på fliken **Användare** och sedan på **Lägg till ny användare**. 
   
    ![Skapa en CloudPassage-testanvändare][23]

1. I avsnittet **Lägg till ny användare** utför du följande steg: 
   
    ![Skapa en CloudPassage-testanvändare][24]
    
    a. I textrutan **Förnamn** skriver du Britta. 
  
    b. I textrutan **Efternamn** skriver du Simon.
  
    c. I textrutorna **Användarnamn**, **E-post** och **Ange e-postadressen igen** skriver du Brittas användarnamn i Azure AD.
  
    d. För **Åtkomsttyp** väljer du **Enable Halo Portal Access** (Aktivera Halo Portal-åtkomst).
  
    e. Klicka på **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på CloudPassage-panelen i åtkomstpanelen bör du automatiskt loggas in på CloudPassage som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

