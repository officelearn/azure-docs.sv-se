---
title: 'Självstudier: Azure Active Directory-integration med TeamSeer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 6c13bb4c10adeeba59ff8d0f2c8d7120d6fae43a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050654"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Självstudier: Azure Active Directory-integration med TeamSeer

I den här självstudien får du lära dig hur du integrerar TeamSeer med Azure Active Directory (AD Azure).

Integrera TeamSeer med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TeamSeer
- Du kan aktivera användarna att automatiskt få loggat in på TeamSeer (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TeamSeer, behöver du följande objekt:

- En Azure AD-prenumeration
- En TeamSeer enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till TeamSeer från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-teamseer-from-the-gallery"></a>Att lägga till TeamSeer från galleriet
Om du vill konfigurera integreringen av TeamSeer i till Azure AD, som du behöver lägga till TeamSeer från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TeamSeer från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **TeamSeer**.

    ![Skapa en Azure AD-användare för testning](./media/teamseer-tutorial/tutorial_teamseer_search.png)

5. I resultatpanelen väljer **TeamSeer**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TeamSeer baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i TeamSeer är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i TeamSeer upprättas.

I TeamSeer, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med TeamSeer, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare TeamSeer](#creating-a-teamseer-test-user)**  – du har en motsvarighet för Britta Simon i TeamSeer som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TeamSeer program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TeamSeer:**

1. I Azure-portalen på den **TeamSeer** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/teamseer-tutorial/tutorial_teamseer_samlbase.png)

3. På den **TeamSeer domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/teamseer-tutorial/tutorial_teamseer_url.png)

     I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://www.teamseer.com/<companyid>`

    > [!NOTE] 
    > Värdet är inte verkliga. Uppdatera värdet med faktiska inloggnings-URL: en. Kontakta [TeamSeer klienten supportteamet](http://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) att hämta värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/teamseer-tutorial/tutorial_teamseer_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/teamseer-tutorial/tutorial_general_400.png)

6. På den **TeamSeer Configuration** klickar du på **konfigurera TeamSeer** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/teamseer-tutorial/tutorial_teamseer_configure.png)

7. I ett annat webbläsarfönster logga du in på webbplatsen TeamSeer företag som administratör.

8. Gå till **HR Admin**.
   
    ![HR-administratören](./media/teamseer-tutorial/ic789634.png "HR-administratör")

9. Klicka på **installationsprogrammet**.
   
    ![Installationsprogrammet](./media/teamseer-tutorial/ic789635.png "installationen")

10. Klicka på **ställa in information om SAML-provider**.
   
    ![SAML-inställningar](./media/teamseer-tutorial/ic789636.png "SAML-inställningar")

11. Utför följande steg i informationsavsnittet för SAML-providern:
   
    ![SAML-inställningar](./media/teamseer-tutorial/ic789637.png "SAML-inställningar")   

    a. Klistra in den **enkel inloggnings-URL för** värde i att den **URL** textrutan.
          
    b. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den i till Urklipp och klistra in den till den **IdP offentligt certifikat** textrutan.

12. Utför följande steg för att slutföra konfigurationen av SAML-providern:
    
    ![SAML-inställningar](./media/teamseer-tutorial/ic789638.png "SAML-inställningar") 

    a. I den **testa e-postadresser**, Skriv test-användarens e-postadress. 
  
    b. I den **utfärdare** textrutan skriver utfärdar-URL för tjänstleverantören. 
  
    c. Klicka på **Spara**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/teamseer-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/teamseer-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/teamseer-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/teamseer-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-teamseer-test-user"></a>Skapa en TeamSeer testanvändare

Om du vill aktivera Azure AD-användare att logga in på TeamSeer, måste de vara etablerade i att ShiftPlanning. När det gäller TeamSeer är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **TeamSeer** företagets plats som administratör.

2. Utför följande steg:
   
    ![HR-administratören](./media/teamseer-tutorial/ic789640.png "HR-administratör")  
 
    a. Gå till **HR Admin \> användare**.
  
    b. Klicka på **kör guiden Ny användare**.

3. I den **användarinformation** avsnittet, utför följande steg:
   
    ![Information om användare](./media/teamseer-tutorial/ic789641.png "användarinformation")

    a. Skriv den **Förnamn**, **efternamn**, **användarnamn (e-postadress)** för ett giltigt AAD-konto som du vill etablera i till relaterade textrutor.
  
    b. Klicka på **Nästa**.

4. Följ den på skärmen för att lägga till en ny användare och klicka på **Slutför**.

>[!NOTE]
>Du kan använda alla andra TeamSeer användare konto verktyg för att skapa eller API: er som tillhandahålls av TeamSeer att etablera användarkonton i Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TeamSeer.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon TeamSeer, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **TeamSeer**.

    ![Konfigurera enkel inloggning](./media/teamseer-tutorial/tutorial_teamseer_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Öppna panelen om du vill testa dina inställningar för enkel inloggning. Mer information om åtkomstpanelen finns i [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/teamseer-tutorial/tutorial_general_01.png
[2]: ./media/teamseer-tutorial/tutorial_general_02.png
[3]: ./media/teamseer-tutorial/tutorial_general_03.png
[4]: ./media/teamseer-tutorial/tutorial_general_04.png

[100]: ./media/teamseer-tutorial/tutorial_general_100.png

[200]: ./media/teamseer-tutorial/tutorial_general_200.png
[201]: ./media/teamseer-tutorial/tutorial_general_201.png
[202]: ./media/teamseer-tutorial/tutorial_general_202.png
[203]: ./media/teamseer-tutorial/tutorial_general_203.png

