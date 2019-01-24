---
title: 'Självstudier: Azure Active Directory-integrering med FreshDesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.openlocfilehash: f8f47201149ce2384eb0adcef86936dc9d017c85
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813601"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Självstudier: Azure Active Directory-integrering med FreshDesk

I den här självstudien lär du dig att integrera FreshDesk med Azure Active Directory (AD Azure).
Genom att integrera FreshDesk med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till FreshDesk.
* Du kan göra så att dina användare automatiskt loggas in på FreshDesk (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med FreshDesk behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* FreshDesk-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* FreshDesk stöder **IDP**-initierad enkel inloggning

## <a name="adding-freshdesk-from-the-gallery"></a>Lägga till FreshDesk från galleriet

För att konfigurera integreringen av FreshDesk till Azure AD behöver du lägga till FreshDesk från galleriet till listan över hanterade SaaS-appar.

**Lägg till FreshDesk från galleriet genom att utföra följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **FreshDesk**, väljer **FreshDesk** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![FreshDesk i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med FreshDesk baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i FreshDesk upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med FreshDesk behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för FreshDesk](#configure-freshdesk-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa FreshDesk-testanvändare](#create-freshdesk-test-user)** – för att ha en motsvarighet för Britta Simon i FreshDesk som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med FreshDesk:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **FreshDesk**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![FreshDesk-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.freshdesk.com` eller något annat värde som Freshdesk har föreslagit.

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.freshdesk.com` eller något annat värde så Freshdesk har föreslagit.

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Hämta dessa värden genom att kontakta [supportteamet för FreshDesk-klienten](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. FreshDesk-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar ett exempel på detta. Standardvärdet för **Unik användaridentifierare** är **user.userprincipalname**, men FreshDesk förväntar sig att detta mappas med användarens e-postadress. Till det kan du använda **user.mail**-attributet från listan eller rätt attributvärde baserat på organisationens konfiguration. 

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:
    
    | Namn | Källattribut |
    | ---------------| --------------- |
    | Unik användaridentifierare | user.mail |

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

8. Öppna **kommandotolken** och kör följande kommandon:

    a. Ange värdet `certutil.exe -dump FreshDesk.cer` i kommandotolken.

    > [!NOTE]
    > Här är **FreshDesk.cer** det certifikat som du har laddat ned från Azure-portalen.

    b. Kopiera värdet **Cert Hash(sha256)** och klistra in det i Anteckningar. 

9. I avsnittet **Konfigurera FreshDesk** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-freshdesk-single-sign-on"></a>Konfigurera enkel inloggning för FreshDesk

1. I ett annat webbläsarfönster loggar du in på din Freshdesk-företagsplats som administratör.

2. Välj **ikonen Inställningar**. I avsnittet **Säkerhet** utför du följande steg:

    ![Enkel inloggning](./media/freshdesk-tutorial/IC776770.png "Enkel inloggning")
  
    a. För **Enkel inloggning (SSO)** väljer du **På**.

    b. Välj **SAML SSO**.

    c. I textrutan **SAML Login URL** (Inloggnings-URL för SAML) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    d. I textrutan **Utloggnings-URL** klistrar du värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    e. I textrutan **Security Certificate Fingerprint** (Fingeravtryck för säkerhetscertifikat) klistrar du in värdet för den **Cert Hash(sha256)** som du hämtade tidigare.
  
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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till FreshDesk.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **FreshDesk**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan skriver du in och väljer **FreshDesk**.

    ![FreshDesk-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-freshdesk-test-user"></a>Skapa Freshdesk-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på FreshDesk måste de etableras i FreshDesk.  
När det gäller FreshDesk är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på **Freshdesk**-klientorganisationen.

2. På menyn längst upp klickar du på **Admin**.

    ![Admin](./media/freshdesk-tutorial/IC776772.png "Admin")

3. På fliken **Allmänna inställningar** klickar du på **Agenter**.
  
    ![Agenter](./media/freshdesk-tutorial/IC776773.png "Agenter")

4. Klicka på **Ny Agent**.

    ![Ny Agent](./media/freshdesk-tutorial/IC776774.png "Ny Agent")

5. I dialogrutan Agentinformation utför du följande steg:

    ![Agentinformation](./media/freshdesk-tutorial/IC776775.png "Agentinformation")

    a. I textrutan **E-post** skriver du in Azure AD-e-postadressen för det Azure AD-konto som du vill etablera.

    b. I textrutan **Fullständigt namn** skriver du in namnet på det Azure AD-konto som du vill etablera.

    c. I textrutan **Rubrik** skriver du in rubriken för det Azure AD-konto som du vill etablera.

    d. Klicka på **Spara**.

    >[!NOTE]
    >Azure AD-kontoinnehavaren får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det aktiveras.
    >
    >[!NOTE]
    >Du kan använda andra verktyg eller API:er för Freshdesk-kontoskapande som tillhandahålls av Freshdesk för att etablera AAD-användarkonton till Freshdesk.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på FreshDesk-panelen i åtkomstpanelen bör du automatiskt loggas in på FreshDesk som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

