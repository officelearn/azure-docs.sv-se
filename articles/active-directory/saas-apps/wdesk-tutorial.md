---
title: 'Självstudier: Azure Active Directory-integration med Wdesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 9ff4d38240bf44cdb3112730b6f6962feed09a6a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444444"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Självstudier: Azure Active Directory-integration med Wdesk

I den här självstudien får du lära dig hur du integrerar Wdesk med Azure Active Directory (AD Azure).

Integrera Wdesk med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Wdesk
- Du kan aktivera användarna att automatiskt få loggat in på Wdesk (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i. [Vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Wdesk, behöver du följande objekt:

- En Azure AD-prenumeration
- En Wdesk enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Wdesk från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-wdesk-from-the-gallery"></a>Att lägga till Wdesk från galleriet
För att konfigurera integrering av Wdesk i Azure AD, som du behöver lägga till Wdesk från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Wdesk från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Wdesk**.

    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/tutorial_wdesk_search.png)

1. I resultatpanelen väljer **Wdesk**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Wdesk baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Wdesk är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Wdesk upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Wdesk.

Om du vill konfigurera och testa Azure AD enkel inloggning med Wdesk, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Wdesk](#creating-a-wdesk-test-user)**  – du har en motsvarighet för Britta Simon i Wdesk som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Wdesk program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Wdesk:**

1. I Azure-portalen på den **Wdesk** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_samlbase.png)

1. På den **Wdesk domän och URL: er** om du vill konfigurera programmet i **IDP** initierad läge utför följande steg:

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

1. Kontrollera **visa avancerade URL-inställningar**. Om du vill konfigurera programmet i **SP** initierade läge, utföra följande steg:

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Du kan hämta dessa värden från WDesk portal när du konfigurerar SSO. 
  
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_general_400.png)
    
1. I ett annat webbläsarfönster, logga in på Wdesk som en administratör.

1. I nedre vänstra hörnet, klickar du på **Admin** och välj **kontoadministratören**:
 
     ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. I Wdesk Admin, går du till **Security**, sedan **SAML** > **SAML-inställningar**:

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. Under **allmänna inställningar**, kontrollera den **aktivera SAML enkel inloggning**:

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

1. Under **tjänstinformation för providern**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopiera den **inloggnings-URL** och klistra in den i **inloggnings-Url** textrutan på Azure-portalen.
   
      b. Kopiera den **Metadata_url** och klistra in den i **identifierare** textrutan på Azure-portalen.
       
      c. Kopiera den **konsument url** och klistra in den i **svars-Url** textrutan på Azure-portalen.
   
      d. Klicka på **spara** på Azure portal för att spara ändringarna.      

1. Klicka på **konfigurera inställningarna för IDP: N** att öppna **redigera inställningar för IDP: N** dialogrutan. Klicka på **Välj fil** att hitta den **Metadata.xml** fil som du sparade från Azure-portalen och sedan ladda upp den.
    
    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
1. Klicka på **spara ändringar**.

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-wdesk-test-user"></a>Skapa en Wdesk testanvändare

Om du vill aktivera Azure AD-användare att logga in på Wdesk, måste de etableras i Wdesk. I Wdesk är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Wdesk som en administratör.
1. Gå till **Admin** > **kontot Administratör**.

     ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. Klicka på **medlemmar** under **personer**.

1. Klicka på **Lägg till medlem** att öppna **Lägg till medlem** dialogrutan. 
   
    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/createuser1.png)  

1. I **användaren** text, anger användarnamnet för användaren som **brittasimon@contoso.com** och klicka på **Fortsätt** knappen.

    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/createuser3.png)

1.  Ange information som visas nedan:
  
    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/createuser4.png)
 
    a. I **e** text, ange den e-postadressen för användaren som **brittasimon@contoso.com**.

    b. I **Förnamn** text, ange först namnet på användaren som **Britta**.

    c. I **efternamn** text anger efternamn för användaren som **Simon**.

1. Klicka på **spara medlemmen** knappen.  

    ![Skapa en Azure AD-användare för testning](./media/wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Wdesk.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Wdesk, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Wdesk**.

    ![Konfigurera enkel inloggning](./media/wdesk-tutorial/tutorial_wdesk_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Wdesk i åtkomstpanelen du bör få automatiskt loggat in på ditt Wdesk program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/wdesk-tutorial/tutorial_general_01.png
[2]: ./media/wdesk-tutorial/tutorial_general_02.png
[3]: ./media/wdesk-tutorial/tutorial_general_03.png
[4]: ./media/wdesk-tutorial/tutorial_general_04.png

[100]: ./media/wdesk-tutorial/tutorial_general_100.png

[200]: ./media/wdesk-tutorial/tutorial_general_200.png
[201]: ./media/wdesk-tutorial/tutorial_general_201.png
[202]: ./media/wdesk-tutorial/tutorial_general_202.png
[203]: ./media/wdesk-tutorial/tutorial_general_203.png

