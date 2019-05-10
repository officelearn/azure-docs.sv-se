---
title: 'Självstudier: Azure Active Directory-integrering med SAML SSO för Jira av lösning GmbH | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO for Jira by resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36f14c8b1a462c7cf8c5ef336a5279bf0f99b2f6
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473147"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Självstudier: Azure Active Directory-integrering med SAML SSO for Jira by resolution GmbH

I den här självstudien får du lära dig hur du ställer in SAML SSO för Jira resolution GmbH med Azure Active Directory (AD Azure).
Integreringen av SAML SSO for Jira by resolution GmbH med Azure AD medför följande fördelar:

* Du kan styra i Azure AD som kan logga in på Jira med plugin-programmet för SAML SSO genom upplösning GmbH.
* Du kan aktivera användarna att vara automatiskt inloggad till i Jira med sina Azure AD-konton med hjälp av SAML SSO för Jira resolution GmbH (Single Sign-On).
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering och SAML SSO för Jira resolution GmbH, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En SAML SSO for Jira by resolution GmbH-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML SSO för Jira resolution GmbH stöder **SP** och **IDP** -initierad SSO

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Lägga till ett Enterprise-program för enkel inloggning

För att ställa in enkel inloggning i Azure AD, som du behöver lägga till ett nytt enterprise-program. I galleriet, det finns en förkonfigurerad program förinställning för detta, **SAML SSO för Jira resolution GmbH**.

**Utför följande steg för att lägga till SAML SSO for Jira by resolution GmbH från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram** och klicka sedan på **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **SAML SSO for Jira by resolution GmbH** i sökrutan, välj **SAML SSO for Jira by resolution GmbH** från resultatpanelen och klicka på **Lägg till** för att lägga till programmet. Du kan också ändra namnet på enterprise-app.

     ![SAML SSO for Jira by resolution GmbH i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Konfigurera och testa enkel inloggning med SAML SSO-plugin-programmet och Azure AD

I det här avsnittet ska du testa och konfigurera enkel inloggning till Jira för Azure AD-användare. Detta görs för en testanvändare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAML SSO for Jira by resolution GmbH upprättas.

Om du vill konfigurera och testa enkel inloggning, måste du utföra följande steg:

1. **[Konfigurera Azure AD-programmet för enkel inloggning för enterprise](#configure-azure-ad-single-sign-on)**  – konfigurera Azure AD-programmet för den enkel inloggning för företag
2. **[Konfigurera SAML SSO-plugin-programmet för Jira-instansen](#configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on)**  – konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – skapa en testanvändare i Azure AD.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  -aktiverar testanvändare att använda enkel inloggning för Azure-sidan.
1. **[Skapa testanvändaren i Jira](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)**  – skapa en motsvarighet testanvändare i Jira för Azure AD-testanvändare.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – kontrollera om konfigurationen fungerar.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Konfigurera Azure AD-programmet för enkel inloggning för företag

I det här avsnittet ska ställa du in den enkel inloggning i Azure-portalen.

Om du vill konfigurera den enkel inloggning med SAML SSO för Jira resolution GmbH, utför du följande steg:

1. I den [Azure-portalen](https://portal.azure.com/), i den just skapade **SAML SSO för Jira resolution GmbH** enterprise-program finns väljer **enkel inloggning** på den vänstra panelen.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. För **väljer du en metod för enkel inloggning**väljer **SAML** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. Därefter klickar du på den **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Information om enkel inloggning med SAML SSO for Jira by resolution GmbH-domäner och -URL:er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Skriv en URL med följande mönster i textrutan **Svars-URL**: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning med SAML SSO for Jira by resolution GmbH-domäner och -URL:er](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > För identifierare, svars-URL och inloggnings-URL kan ersätta  **\<server-bas-url >** med den grundläggande Webbadressen för Jira-instansen. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen. Om du har problem med var inte rädd för att kontakta oss på [SAML SSO för Jira resolution GmbH klienten supportteam](https://www.resolution.de/go/support).

4. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet kan du ladda ner den **Federation Metadata XML** och spara det på din dator.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Konfigurera SAML SSO-plugin-programmet för Jira-instansen] 

1. I ett annat webbläsarfönster logga du in på din Jira-instans som en administratör.

2. Hovra över kugghjulet längst till höger och klicka på **hantera appar**.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon1.png)

3. Om du omdirigeras till administratörsåtkomst sida, anger du den **lösenord** och klicka på den **Bekräfta** knappen.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon2.png)

4. Jira omdirigerar dig till Atlassians marketplace normalt. Om inte, klickar du på **hitta nya appar** på den vänstra panelen. Sök efter **SAML enkel inloggning (SSO) för JIRA** och klicka på den **installera** för att starta installationen SAML-plugin-programmet.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store.png)

5. Installationen av plugin-programmet startar. När det är klart klickar du på den **Stäng** knappen.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store-2.png)

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store-3.png)

6.  Klicka sedan på **hantera**.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store-4.png)
    
8. Klicka därefter på **konfigurera** så här konfigurerar du nyss installerade plugin-programmet.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store-5.png)

9. I den **SAML SingleSignOn plugin-programmet Configuration** guiden klickar du på **lägga till nya IDP: N** att konfigurera Azure AD som en ny identitetsprovider.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon4.png) 

10. Utför följande steg på sidan **Välj din SAML-identitetsprovider**:

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5a.png)
 
    a. Ange **Azure AD** som IdP-typ.
    
    b. Lägg till den **namn** identitetsleverantörens (t.ex Azure AD).
    
    c. Lägg till en (valfri) **beskrivning** identitetsleverantörens (t.ex Azure AD).
    
    d. Klicka på **Nästa**.
    
11. På den **identitet providerkonfigurationen** klickar du på **nästa**.
 
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5b.png)

12. Utför följande steg på sidan **Importera SAML IdP-metadata**:

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5c.png)

    a. Klicka på **väljer XML-fil** knappen och välj **XML-Metadata för Federation** filen du laddade ner innan.

    b. Klicka på den **Import** knappen.
     
    c. Vänta en kort stund tills importen slutförts.  
     
    d. Klicka på **Nästa**.
    
13. Klicka på **Nästa** på sidan för **attribut för användar-ID och transformering**.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5d.png)
    
14. Spara inställningarna genom att klicka på **Spara och Nästa** på sidan för **användargenerering och uppdatering**.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon6a.png)
    
15. Hoppa över användartestningen så länge genom att klicka på **Hoppa över test och konfigurera manuellt** på sidan **Testa dina inställningar**. Du ska köra testningen i nästa avsnitt, vilket kräver ytterligare några inställningar på Azure-portalen.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon6b.png)
    
16. Klicka på **OK** att hoppa över varningen.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon. Med användaren, ska du testa enkel inloggning.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I den **användaregenskaper**, utför följande steg:

    ![Dialogrutan användare](common/user-properties.png)

    a. I den **namn** anger **Britta Simon**.
  
    b. I den **användarnamn** anger <b> BrittaSimon@contoso.com </b>.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet lägger du till Britta Simon företagsprogram, vilket gör att henne att använda enkel inloggning.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**. 

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Sök efter företagsprogram som du har skapat i början av den här självstudiekursen i listan med program. Om du följer stegen i självstudien kallas **SAML SSO för Jira resolution GmbH**. Om du får den ett annat namn, Sök efter det namnet.

    ![SAML SSO for Jira by resolution GmbH-länken i listan över program](common/all-applications.png)

3. I den vänstra rutan klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”](common/users-groups-blade.png)

4. Välj **Lägg till användare**, sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-the-test-user-also-in-jira"></a>Skapa testanvändaren också i Jira

För att Azure AD-användare ska kunna logga in i SAML SSO for Jira by resolution GmbH måste de vara etablerade i SAML SSO for Jira by resolution GmbH. För att i fallet med den här självstudien måste du göra etableringen manuellt. Men det finns också andra etablering modeller för SAML SSO-plugin-programmet genom att upplösning som är till exempel **Just In Time** etablering. Referera till deras dokumentation på [SAML SSO resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Om du har frågor om det var inte rädd support på [matchningsstöd](https://www.resolution.de/go/support).

**Om du vill manuellt konfigurera ett användarkonto, utför du följande steg:**

1. Logga in på Jira-instans som administratör.

2. Hovra över kugghjulet och välj **Användarhantering**.

   ![Lägga till medarbetare](./media/samlssojira-tutorial/user1.png)

3. Om du omdirigeras till administratörsåtkomst sida, anger du den **lösenord** och klicka på den **Bekräfta** knappen.

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user2.png) 

4. På avsnittsfliken **Användarhantering** klickar du på **skapa användare**.

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user3-new.png) 

5. På den **”Skapa ny användare”** dialogrutan utför följande steg. Du måste skapa användaren exakt som i Azure AD:

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user4-new.png) 

    a. I den **e-postadress** textrutan skriver du e-postadressen för användaren: <b> BrittaSimon@contoso.com </b>.

    b. I den **fullständigt namn** textrutan fullständiga namn för användaren: **Britta Simon**.

    c. I den **användarnamn** textrutan Skriv e-postadress för användaren: <b> BrittaSimon@contoso.com </b>. 

    d. I den **lösenord** textrutan anger du lösenordet för användaren.

    e. Klicka på **skapa användare** slutförts generering för användare.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen för SAML SSO for Jira by resolution GmbH på åtkomstpanelen bör du loggas in automatiskt i SAML SSO for Jira by resolution GmbH-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Du kan också testa enkel inloggning, om du navigerar till [https://\<server-bas-url >/plugin-program/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). Ersätt  **\<server-bas-url >** med den grundläggande Webbadressen för Jira-instansen.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Aktivera enkel inloggning-omdirigering för Jira

Enligt vad som anges i avsnittet före finns det för närvarande två sätt att utlösa den enkel inloggning. Antingen med hjälp av den **Azure-portalen** eller med hjälp av **en särskild länk till din instans av Jira**. Plugin-programmet för SAML SSO resolution GmbH kan du aktivera enkel inloggning genom att helt enkelt **åtkomst till alla URL: er som pekar på din instans av Jira**.

I princip omdirigeras alla användare med åtkomst till Jira till den enkel inloggning efter aktivering av ett alternativ i plugin-programmet.

Om du vill aktivera omdirigering för enkel inloggning, gör du följande i **Jira-instans**:

1. Komma åt konfigurationssidan av SAML SSO-plugin-programmet i Jira.
1. Klicka på **omdirigering** på den vänstra panelen.
![](./media/samlssojira-tutorial/ssore1.png)

1. Skalstreck **aktivera SSO omdirigering**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Tryck på den **Spara inställningar** knappen i övre högra hörnet.

När du har aktiverat alternativet kan du fortfarande nå användarnamn/lösenord fråga om den **aktivera nosso** alternativet ticked genom att gå till [https://\<server-bas-url > /login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso). Som alltid kan ersätta  **\<server-bas-url >** med bas-URL.


## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

