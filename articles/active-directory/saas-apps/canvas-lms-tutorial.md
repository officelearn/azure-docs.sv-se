---
title: 'Självstudier: Azure Active Directory-integration med arbetsytan Lms | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LMS-arbetsytan.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: jeedes
ms.openlocfilehash: 9811be78bd6260b17c462c077ed299dff8309b83
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048515"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Självstudier: Azure Active Directory-integration med LMS-arbetsytan

Lär dig hur du integrerar arbetsytan med Azure Active Directory (AD Azure) i den här självstudien.

Integrera arbetsytan med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till arbetsytan
- Du kan aktivera användarna att automatiskt få loggat in på arbetsytan (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med arbetsytor, behöver du följande objekt:

- En Azure AD-prenumeration
- En arbetsyta enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till arbetsytan från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-canvas-from-the-gallery"></a>Att lägga till arbetsytan från galleriet
För att konfigurera integrering av arbetsytor i Azure AD, som du behöver lägga till arbetsytan från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till arbetsytan från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **arbetsytan**.

    ![Skapa en Azure AD-användare för testning](./media/canvas-lms-tutorial/tutorial_canvaslms_search.png)

5. I resultatpanelen väljer **arbetsytan**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/canvas-lms-tutorial/tutorial_canvaslms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med arbetsytor baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i arbetsytan till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i arbetsytan upprättas.

I arbetsytan, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med arbetsyta, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en arbetsyta testanvändare](#creating-a-canvas-test-user)**  – du har en motsvarighet för Britta Simon i arbetsytan som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för arbetsytan.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med arbetsyta:**

1. I Azure-portalen på den **arbetsytan** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/canvas-lms-tutorial/tutorial_canvaslms_samlbase.png)

3. På den **arbetsytan domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/canvas-lms-tutorial/tutorial_canvaslms_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<tenant-name>.instructure.com`

    b. I den **identifierare** textrutan skriver du värdet med följande mönster: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [arbetsytan klienten supportteamet](https://community.canvaslms.com/community/help) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCK** värdet för certifikatet.

    ![Konfigurera enkel inloggning](./media/canvas-lms-tutorial/tutorial_canvaslms_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/canvas-lms-tutorial/tutorial_general_400.png)

6. På den **arbetsytan Configuration** klickar du på **konfigurera arbetsytan** att öppna **konfigurera inloggning** fönster. Kopiera den **ändra lösenord URL, URL: en för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/canvas-lms-tutorial/tutorial_canvaslms_configure.png) 
 
7. I ett annat webbläsarfönster logga du in på din arbetsyta företagets webbplats som administratör.

8. Gå till **kurser \> hanterade konton \> Microsoft**.
   
    ![Arbetsytan](./media/canvas-lms-tutorial/IC775990.png "arbetsytan")

9. I navigeringsfönstret till vänster, Välj **autentisering**, och klicka sedan på **Lägg till ny SAML-konfiguration**.
   
    ![Autentisering](./media/canvas-lms-tutorial/IC775991.png "autentisering")

10. Utför följande steg på sidan aktuella integrering:
   
    ![Aktuell Integration](./media/canvas-lms-tutorial/IC775992.png "aktuella integrering")

    a. I **IdP entitets-ID** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.

    b. I **Log på URL: en** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. I **Log URL: en** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.

    d. I **ändra lösenord-länk** textrutan klistra in värdet för **ändra lösenord URL** som du har kopierat från Azure-portalen. 

    e. I **certifikat fingeravtryck** textrutan klistra in den **tumavtryck** värdet för certifikat som du har kopierat från Azure-portalen.      
        
    f. Från den **inloggning attributet** väljer **NameID**.

    g. Från den **identifierare Format** väljer **e-postadress**.

    h. Klicka på **spara autentiseringsinställningar**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/canvas-lms-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/canvas-lms-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/canvas-lms-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/canvas-lms-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-canvas-test-user"></a>Skapa en testanvändare för arbetsytan

Om du vill aktivera Azure AD-användare att logga in på arbetsytan, måste de etableras i arbetsytan.

Om arbetsytan och är etableringen av användare en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **arbetsytan** klient.

2. Gå till **kurser \> hanterade konton \> Microsoft**.
   
   ![Arbetsytan](./media/canvas-lms-tutorial/IC775990.png "arbetsytan")

3. Klicka på **användare**.
   
   ![Användare](./media/canvas-lms-tutorial/IC775995.png "användare")

4. Klicka på **Lägg till ny användare**.
   
   ![Användare](./media/canvas-lms-tutorial/IC775996.png "användare")

5. Utför följande steg på bladet Lägg till en ny användare dialogrutan sida:
   
   ![Lägg till användare](./media/canvas-lms-tutorial/IC775997.png "lägga till användare")
   
   a. I den **fullständigt namn** textrutan anger du namnet på användaren som **BrittaSimon**.

   b. I den **e-post** textrutan Ange e-postadress för användaren som **brittasimon@contoso.com**.

   c. I den **inloggning** textrutan Ange användarens Azure AD e-postadress som **brittasimon@contoso.com**.

   d. Välj **e-användaren om det här kontot skapas**.

   e. Klicka på **lägga till användare**.

>[!NOTE]
>Du kan använda arbetsytan användaren-konto skapas verktyg från någon annan eller API: er som tillhandahålls av arbetsytan att etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till arbetsytan.

![Tilldela användare][200] 

**Om du vill tilldela arbetsytan Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **arbetsytan**.

    ![Konfigurera enkel inloggning](./media/canvas-lms-tutorial/tutorial_canvaslms_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen arbetsytor i åtkomstpanelen du bör få automatiskt loggat in på programmets arbetsyta.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/canvas-lms-tutorial/tutorial_general_01.png
[2]: ./media/canvas-lms-tutorial/tutorial_general_02.png
[3]: ./media/canvas-lms-tutorial/tutorial_general_03.png
[4]: ./media/canvas-lms-tutorial/tutorial_general_04.png

[100]: ./media/canvas-lms-tutorial/tutorial_general_100.png

[200]: ./media/canvas-lms-tutorial/tutorial_general_200.png
[201]: ./media/canvas-lms-tutorial/tutorial_general_201.png
[202]: ./media/canvas-lms-tutorial/tutorial_general_202.png
[203]: ./media/canvas-lms-tutorial/tutorial_general_203.png

