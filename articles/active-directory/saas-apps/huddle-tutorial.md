---
title: 'Självstudier: Azure Active Directory-integration med Huddle | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Huddle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 8aec15cbd3094adeceb12574c47c8246825e4ece
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043745"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Självstudier: Azure Active Directory-integration med Huddle

I den här självstudien får du lära dig hur du integrerar Huddle med Azure Active Directory (AD Azure).

Integrera Huddle med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Huddle
- Du kan aktivera användarna att automatiskt få loggat in på Huddle (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Huddle, behöver du följande objekt:

- En Azure AD-prenumeration
- En Huddle enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Huddle från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-huddle-from-the-gallery"></a>Att lägga till Huddle från galleriet
Om du vill konfigurera integreringen av Huddle i Azure AD, som du behöver lägga till Huddle från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Huddle från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Huddle**.

    ![Skapa en Azure AD-användare för testning](./media/huddle-tutorial/tutorial_huddle_search.png)

5. I resultatpanelen väljer **Huddle**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Huddle baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Huddle är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Huddle upprättas.

I Huddle, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Huddle, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.

2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.

3. **[Skapa en testanvändare Huddle](#creating-a-huddle-test-user)**  – du har en motsvarighet för Britta Simon i Huddle som är länkad till en Azure AD-representation av användaren.

4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.

5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Huddle program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Huddle:**

1. I Azure-portalen på den **Huddle** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/huddle-tutorial/tutorial_huddle_samlbase.png)

3. På den **Huddle domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/huddle-tutorial/tutorial_huddle_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `http://<company name>.huddle.com`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Huddle klienten supportteamet](https://huddle.zendesk.com) att hämta det här värdet. 

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/huddle-tutorial/tutorial_huddle_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/huddle-tutorial/tutorial_general_400.png)

6. På den **Huddle Configuration** klickar du på **konfigurera Huddle** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.** 

    ![Konfigurera enkel inloggning](./media/huddle-tutorial/tutorial_huddle_configure.png) 
    
7. Om du vill konfigurera enkel inloggning på Huddle sida, du behöver skicka de hämtade **certifikat**, **SAML enkel inloggning för tjänst-URL**, och **SAML entitets-ID** till [ Huddle klienten supportteamet](https://huddle.zendesk.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.  
   
    >[!NOTE]
    > Enkel inloggning måste aktiveras av Huddle support-teamet. Du får ett meddelande när konfigurationen har slutförts. 
    > 

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 
   
### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/huddle-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/huddle-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/huddle-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/huddle-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-huddle-test-user"></a>Skapa en Huddle testanvändare

Om du vill aktivera Azure AD-användare att logga in på Huddle, måste de etableras i Huddle. När det gäller Huddle är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på din **Huddle** företagets plats som administratör.
2. Klicka på **arbetsytan**.
3. Klicka på **personer \> bjuda in människor**.
   
   ![Personer](./media/huddle-tutorial/IC787838.png "personer")

4. I den **skapa en ny inbjudan** avsnittet, utför följande steg:
   
   ![Ny inbjudan](./media/huddle-tutorial/IC787839.png "ny inbjudan")
   
   a. I den **Välj ett team för att bjuda in människor att ansluta till** väljer **team**.

   b. Typen i **e-postadress** av en giltig Azure AD-konto som du vill etablera i att **RETUR e-postadress för personer som du vill bjuda in** textrutan.

   c. Klicka på **bjuda in**.   
   
    >[!NOTE]
    > Azure AD-kontoinnehavare får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktiv. 
    > 

>[!NOTE]
>Du kan använda andra verktyg för Huddle att skapa användaren-konto eller API: er som tillhandahålls av Huddle för att etablera användarkonton i Azure AD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Huddle.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Huddle, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Huddle**.

    ![Konfigurera enkel inloggning](./media/huddle-tutorial/tutorial_huddle_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Huddle i åtkomstpanelen, får du automatiskt inloggningssidan i Huddle program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png
