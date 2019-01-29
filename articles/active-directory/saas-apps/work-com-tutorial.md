---
title: 'Självstudier: Azure Active Directory-integrering med Work.com | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 836c9e0dd15da9b117c26cd05fc723607141b059
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194720"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Självstudier: Azure Active Directory-integrering med Work.com

I den här självstudien får du lära dig hur du integrerar Work.com med Azure Active Directory (AD Azure).

Integrera Work.com med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Work.com
- Du kan aktivera användarna att automatiskt få loggat in på Work.com (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Work.com, behöver du följande objekt:

- En Azure AD-prenumeration
- En Work.com enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägg till Work.com från galleriet
1. Konfigurera och testa enkel inloggning med Azure AD

## <a name="add-workcom-from-the-gallery"></a>Lägg till Work.com från galleriet
För att konfigurera integrering av Work.com i Azure AD, som du behöver lägga till Work.com från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Work.com från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Work.com**väljer **Work.com** från resultatrutan klickar **Lägg till** för att lägga till programmet.

    ![Lägg till från galleriet](./media/work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Work.com baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Work.com är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Work.com upprättas.

I Work.com, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Work.com, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Work.com](#create-a-workcom-test-user)**  – du har en motsvarighet för Britta Simon i Work.com som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Work.com program.

>[!NOTE]
>Om du vill konfigurera enkel inloggning, måste du konfigurera ett anpassat domännamn för Work.com ännu. Du måste ange minst ett domännamn, testa ditt domännamn och distribuera den till hela organisationen.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Work.com:**

1. I Azure-portalen på den **Work.com** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserad inloggning](./media/work-com-tutorial/tutorial_work-com_samlbase.png)

1. På den **Work.com domän och URL: er** avsnittet, gör du följande:

    ![Work.com domän och URL: er](./media/work-com-tutorial/tutorial_work-com_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Work.com klienten supportteamet](https://help.salesforce.com/articleView?id=000159855&type=3) att hämta det här värdet. 

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Avsnittet för SAML-signeringscertifikat](./media/work-com-tutorial/tutorial_work-com_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Knappen Spara](./media/work-com-tutorial/tutorial_general_400.png)

1. På den **Work.com Configuration** klickar du på **konfigurera Work.com** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurationsavsnittet för Work.com](./media/work-com-tutorial/tutorial_work-com_configure.png) 
1. Logga in på din Work.com-klient som administratör.

1. Gå till **installationsprogrammet**.
   
    ![Konfiguration](./media/work-com-tutorial/ic794108.png "Konfiguration")

1. I det vänstra navigeringsfönstret i den **administrera** klickar du på **domänhantering** Expandera avsnittet relaterade och klicka sedan på **min domän** att öppna **Min domän** sidan. 
   
    ![My Domain](./media/work-com-tutorial/ic767825.png "My Domain")

1. För att verifiera att din domän har ställts in korrekt, se till att den är i ”**steg 4 distribueras till användarna**” och granska din ”**Mina Domäninställningar**”.
   
    ![Domän som har distribuerats till användaren](./media/work-com-tutorial/ic784377.png "domän som har distribuerats till användare")

1. Logga in på din Work.com-klient.

1. Gå till **installationsprogrammet**.
    
    ![Konfiguration](./media/work-com-tutorial/ic794108.png "Konfiguration")

1. Expandera den **säkerhetskontroller** menyn och klicka sedan på **inställningar för enkel inloggning**.
    
    ![Enkel inloggning inställningar](./media/work-com-tutorial/ic794113.png "enkel inloggnings-inställningar")

1. På den **inställningar för enkel inloggning** dialogrutan utför följande steg:
    
    ![SAML aktiverat](./media/work-com-tutorial/ic781026.png "SAML aktiverat")
    
    a. Välj **SAML aktiverat**.
    
    b. Klicka på **Ny**.

1. I den **SAML enkel inloggning inställningar** avsnittet, utför följande steg:
    
    ![SAML enkel inloggning inställningen](./media/work-com-tutorial/ic794114.png "SAML enkel inloggning för inställningen")
    
    a. I den **namn** textrutan anger du ett namn för din konfiguration.  
       
    > [!NOTE]
    > Att tillhandahålla ett värde för **namn** automatiskt fylla i **API-namn** textrutan.
    
    b. I **utfärdare** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.
    
    c. Om du vill ladda upp det nedladdade certifikatet från Azure-portalen, klickar du på **Bläddra**.
    
    d. I den **entitets-Id** textrutan typ `https://salesforce-work.com`.
    
    e. Som **SAML identitetstypen**väljer **Assertion innehåller Federation-ID från användarobjektet**.
    
    f. Som **SAML identitet plats**väljer **identitet är i NameIdentfier-elementet i instruktionen ämne**.
    
    g. I **inloggnings-URL för identitetsprovider** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    h. I **utloggnings-URL för identitetsprovider** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.
    
    i. Som **providern initierade begära Tjänstbindning**väljer **HTTP Post**.
    
    j. Klicka på **Spara**.

1. I den klassiska portalen i Work.com, i det vänstra navigeringsfönstret klickar du på **domänhantering** Expandera avsnittet relaterade och klicka sedan på **min domän** att öppna den **min domän** sidan. 
    
    ![My Domain](./media/work-com-tutorial/ic794115.png "My Domain")

1. På den **min domän** sidan den **inloggning anpassningen** klickar du på **redigera**.
    
    ![Inloggningssidan anpassning](./media/work-com-tutorial/ic767826.png "inloggningssidan för anpassning")

1. På den **inloggning anpassningen** sidan den **autentiseringstjänst** avsnittet, namnet på din **SAML SSO-inställningar** visas. Markera den och klicka sedan på **spara**.
    
    ![Inloggningssidan anpassning](./media/work-com-tutorial/ic784366.png "inloggningssidan för anpassning")

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/work-com-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Användare och grupper -> alla användare](./media/work-com-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Lägg till](./media/work-com-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användarsidan](./media/work-com-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-workcom-test-user"></a>Skapa en Work.com testanvändare
För Azure Active Directory-användare för att kunna logga in måste de etableras till Work.com. När det gäller Work.com är etablering en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurera användaretablering genom att utföra följande steg:
1. Logga in på webbplatsen Work.com företag som administratör.

1. Gå till **installationsprogrammet**.
   
    ![Konfiguration](./media/work-com-tutorial/IC794108.png "Konfiguration")
1. Gå till **hantera användare \> användare**.
   
    ![Hantera användare](./media/work-com-tutorial/IC784369.png "hantera användare")

1. Klicka på **Ny användare**.
   
    ![Alla användare](./media/work-com-tutorial/IC794117.png "alla användare")

1. I avsnittet användaren redigera utför du följande steg, i attributen för en giltig Azure AD-konto som du vill etablera till relaterade textrutor:
   
    ![Redigera användare](./media/work-com-tutorial/ic794118.png "Redigera användare")
   
    a. I den **Förnamn** textrutan skriver den **Förnamn** användarens **Britta**.
    
    b. I den **efternamn** textrutan skriver den **efternamn** användarens **Simon**.
    
    c. I den **Alias** textrutan skriver den **namn** användarens **BrittaS**.
    
    d. I den **e-post** textrutan skriver den **e-postadress** användarens **Brittasimon@contoso.com**.
    
    e. I den **användarnamn** textrutan, skriver du in ett användarnamn för användaren som **Brittasimon@contoso.com**.
    
    f. I den **smeknamn** textrutan anger du ett **smeknamn** användarens **Simon**.
    
    g. Välj **rollen**, **användarlicens**, och **profil**.
    
    h. Klicka på **Spara**.  
      
    > [!NOTE]
    > Azure AD-kontoinnehavare får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktiv.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Work.com.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Work.com, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Work.com**.

    ![Work.com i appens lista](./media/work-com-tutorial/tutorial_work-com_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Work.com i åtkomstpanelen du bör få automatiskt loggat in på ditt Work.com program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/work-com-tutorial/tutorial_general_01.png
[2]: ./media/work-com-tutorial/tutorial_general_02.png
[3]: ./media/work-com-tutorial/tutorial_general_03.png
[4]: ./media/work-com-tutorial/tutorial_general_04.png

[100]: ./media/work-com-tutorial/tutorial_general_100.png

[200]: ./media/work-com-tutorial/tutorial_general_200.png
[201]: ./media/work-com-tutorial/tutorial_general_201.png
[202]: ./media/work-com-tutorial/tutorial_general_202.png
[203]: ./media/work-com-tutorial/tutorial_general_203.png

