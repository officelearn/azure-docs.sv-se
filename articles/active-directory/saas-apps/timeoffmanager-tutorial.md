---
title: 'Självstudier: Azure Active Directory-integrering med TimeOffManager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 4ca13640f64c88164aa98c5434c252d844cebd72
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825194"
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Självstudier: Azure Active Directory-integrering med TimeOffManager

I den här självstudien får du lära dig hur du integrerar TimeOffManager med Azure Active Directory (AD Azure).

Integrera TimeOffManager med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TimeOffManager
- Du kan aktivera användarna att automatiskt få loggat in på TimeOffManager (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TimeOffManager, behöver du följande objekt:

- En Azure AD-prenumeration
- En TimeOffManager enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägg till TimeOffManager från galleriet
1. Konfigurera och testa enkel inloggning med Azure AD

## <a name="add-timeoffmanager-from-the-gallery"></a>Lägg till TimeOffManager från galleriet
För att konfigurera integrering av TimeOffManager i Azure AD, som du behöver lägga till TimeOffManager från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TimeOffManager från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **TimeOffManager**väljer **TimeOffManager** resultatet panelen och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Lägg till från galleriet](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TimeOffManager baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i TimeOffManager är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i TimeOffManager upprättas.

I TimeOffManager, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med TimeOffManager, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare TimeOffManager](#create-a-timeoffmanager-test-user)**  – du har en motsvarighet för Britta Simon i TimeOffManager som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TimeOffManager program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TimeOffManager:**

1. I Azure-portalen på den **TimeOffManager** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserad inloggning](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_samlbase.png)

1. På den **TimeOffManager domän och URL: er** avsnittet, gör du följande:

     ![TimeOffManager domän och URL: er](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_url.png)

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska svars-URL. Du kan få det här värdet från **inställningssidan för enkelinloggning** som beskrivs senare i självstudien eller kontakta [TimeOffManager supportteamet](https://www.purelyhr.com/contact-us).
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Avsnittet för SAML-signeringscertifikat](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_certificate.png) 

1. Målet med det här avsnittet som beskriver hur du aktiverar användare att autentisera till TimeOffManger med ett konto i Azure AD med federation baserat på SAML-protokoll.
    
    Programmets TimeOffManger förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar ett exempel på detta.

    ![SAML-tokenattribut](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_attrb.png "saml-tokenattribut")
    
    | Attributnamn | Attributvärde |
    | --- | --- |
    | Förnamn |User.givenName |
    | Efternamn |User.surname |
    | E-post |User.mail |
    
    a.  För varje datarad i tabellen ovan, klickar du på **lägga till användarattribut**.
    
    ![SAML-tokenattribut](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb.png "saml-tokenattribut")
    
    ![SAML-tokenattribut](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb1.png "saml-tokenattribut")
    
    b.  I den **attributnamnet** textrutan skriver du attributnamnet som visas för den raden.
    
    c.  I den **attributvärdet** textrutan väljer attribut-värde som visas för den raden.
    
    d.  Klicka på **OK**.
    
1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/timeoffmanager-tutorial/tutorial_general_400.png)

1. På den **TimeOffManager Configuration** klickar du på **konfigurera TimeOffManager** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![TimeOffManager konfigurationsavsnittet](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_configure.png) 

1. Logga in på webbplatsen TimeOffManager företag som en administratör i ett annat webbläsarfönster.

1. Gå till **konto \> kontoalternativ \> enkel inloggning inställningar**.
   
   ![Enkel inloggning inställningar](./media/timeoffmanager-tutorial/ic795917.png "enkel inloggnings-inställningar")
1. I den **inställningar för enkel inloggning** avsnittet, utför följande steg:
   
   ![Enkel inloggning inställningar](./media/timeoffmanager-tutorial/ic795918.png "enkel inloggnings-inställningar")
   
   a. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in hela certifikatet i **X.509-certifikat** textrutan.
   
   b. I **IDP: N utfärdar** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.
   
   c. I **slutpunkts-URL för IDP: N** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
   d. Som **framtvinga SAML**väljer **nr**.
   
   e. Som **skapa automatiskt användare**väljer **Ja**.
   
   f. I **URL för utloggning** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.
   
   g. Klicka på **spara ändringar**.

1. I **inställningar för enkelinloggning** sidan, Kopiera värdet för **URL för Konsumenttjänst för försäkran** och klistra in den i den **svars-URL** textrutan under **TimeOffManager Domän och URL: er** avsnitt i Azure-portalen. 

      ![Enkel inloggning inställningar](./media/timeoffmanager-tutorial/ic795915.png "enkel inloggnings-inställningar")

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/timeoffmanager-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Användare och grupper--> alla användare](./media/timeoffmanager-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Lägg till knapp](./media/timeoffmanager-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användarsidan](./media/timeoffmanager-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-timeoffmanager-test-user"></a>Skapa en TimeOffManager testanvändare

För att aktivera Azure AD-användare att logga in på TimeOffManager, måste de etableras till TimeOffManager.  

TimeOffManager stöder just-in-time användaretablering. Det finns inga uppgift åt dig.  

Användare läggs till automatiskt vid första inloggningen med enkel inloggning på.

>[!NOTE]
>Du kan använda alla andra TimeOffManager användare konto verktyg för att skapa eller API: er som tillhandahålls av TimeOffManager att etablera användarkonton i Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TimeOffManager.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon TimeOffManager, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **TimeOffManager**.

    ![TimeOffManager i applistan](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen TimeOffManager i åtkomstpanelen du bör få automatiskt loggat in på ditt TimeOffManager program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/timeoffmanager-tutorial/tutorial_general_01.png
[2]: ./media/timeoffmanager-tutorial/tutorial_general_02.png
[3]: ./media/timeoffmanager-tutorial/tutorial_general_03.png
[4]: ./media/timeoffmanager-tutorial/tutorial_general_04.png

[100]: ./media/timeoffmanager-tutorial/tutorial_general_100.png

[200]: ./media/timeoffmanager-tutorial/tutorial_general_200.png
[201]: ./media/timeoffmanager-tutorial/tutorial_general_201.png
[202]: ./media/timeoffmanager-tutorial/tutorial_general_202.png
[203]: ./media/timeoffmanager-tutorial/tutorial_general_203.png

